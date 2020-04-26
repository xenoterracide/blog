+++
title = "Providing with Providers and Bread::Board"
date = 2013-10-23T05:56:00Z
updated = 2013-10-24T19:21:43Z
tags = ["Bread::Board", "Dependency Injection", "object oriented design",  "perl", "design patterns", "service locator pattern", "Inversion of Control", "provider pattern"]
blogimport = true 
type = "post"
aliases = ["/2013/10/providing-with-providers-and-breadboard.html"]
+++

So when I started using Dependency Injection the following problem happened, how do I Inject this dependency when the
container is not accessible at this point. Ok, that sentence even confused me a little bit, so what do I mean. Let's 
say I have a [Repository][repo] for Products that is injected into my controller. Each Product stored has one or more
ProductVariants that is part of it's aggregate, which itself has Nested Categories. Loading this entire graph at once
would be relatively expensive, so we decide to do some lazy loading via DBI in the classes. One problem, how on earth
do we Inject a Database Handle all the way down to Categories. Most of these ways are against DI, but they are
solutions to the problem, there are also ways to combine these. Also, your model class having a database handle is
probably bad design itself, but I'm not going to get into that. Sadly I've done every one of these

## Manual

```perl
package ProductVariant;
...

sub _load_categories {
...
     my $config = Config::Merge->new('/path/to/config');

     my $dbh = DBI->connect( $config->('db.dsn'), $config->('db.user'), $config->('db.pass') );
}
```
Well at least you aren't hard coding the way to read your config file, or your database driver. You're smart enough to
rely on an Interface rather than an Implementation. This is fraught with so many problems. Firstly if your web server
(assuming it's a web application) is getting any kind of traffic at all you'll end up creating tons of database
connections, you'll also be reading that config file every time (ok I forget if Config::Merge caches to memory, it
might, but often when I see people design this way, they are basically slurping the file every time). Someday 5 years
from now, someone is going to hate you because now they need to support replicants... and the config needs to support
more connection strings, which means modifying every place you've done this. Also, you've completely lost the ability
to inject your dependencies for whatever reason you may want to. 

## Inheritance/Composition

```perl
package Role::DBH;
...


has _config => (
    isa     => 'Config::Merge',
    is      => 'ro'
    lazy    => 1,
    default => sub { Config::Merge->new('/path/to/config'); },
);

has _dbh => (
    isa => 'DBI::db',
    is  => 'ro',
    lazy => 1,
    default => sub { my $self = shift;
        DBI->connect( $self->_config->('db.dsn'), $self->_config->('db.user'), $self->_config->('db.pass') );
    },
);

package ProductVariant;
with 'Role::DBH';

sub _load_categories {
...
     $self->_dbh
}
```
Ok, this is a little bit better than before, at least now you have Inverted your dependencies, you could provide the
config or the database handle to the class. You've also put the code in a centralized place so it's easy to change when
you need to. You're still reading the file fairly often, though perhaps less because it now depends on how long Product
variant is alive. So what happens if your connection is lost? We still have a connection for each class, a connection
that may now be held much longer. Why does Product Variant need access to the config? this is a violation of the Law of
Demeter.

## Naive Service Locator

```perl
package MyContainer;
use 5.010;
use Moose;
use Bread::Board;

sub instance {
    state $locator = __PACKAGE__->new;
    return $locator;
}

sub BUILD {
    my $self = shift;

    container $self => as {
        service config => (
            class     => 'Config::Merge',
            lifecycle => 'Singleton',
            block     => sub { Config::Merge->new('/path/to/config'); },
        );
        service dbh => (
            class        => 'DBI::db',
            lifecycle    => 'Singleton',
            dependencies => {
                config => depends_on('config'),
            },
            block        => sub {
                 my $s = shift;
                 my $config = $s->param('config');
                 return DBI->connect( $config->('db.dsn'), $config->('db.user'), $config->('db.pass') );
            },
        );
    };
}
 
package ProductVariant;
 
sub _load_categories {
...
     my $dbh = MyContainer->instance>resolve( service => 'dbh' );
}
```
We need to get rid of knowledge of the config. We can do this by using a [Service Locator][svclocator], which is simply
a well known service to retrieve other services, usually a global singleton. In our example we're at least smart enough
to allow ourselves to change the class out via injection for testing. We no longer have tons of connections or config
reads. However, we now have a new problems, what happens when our Application Server forks a process and we lose the
database connection? What about when our locator gets more complex, like nested containers, that could change or
access, specifically with replication. Also our class is now directly dependent on Bread::Board, and its interface. At
least we've stopped caring how our database handle is built. Our locator is a global singleton, and we can't change our
Container class for testing.

## Robust Service Locator

```perl
package MyContainer;
use 5.010;
use Moose;
use Bread::Board::Declare;
 
sub instance {
    state $locator = __PACKAGE__->new;
    return $locator;
}
 
has config => (
    isa       => 'Config::Merge',
    is        => 'ro',
    lifecycle => 'Singleton'
    block     => sub { Config::Merge->new('/path/to/config'); },
):

has conn => (
  isa          => 'DBIx::Connector',
  dependencies => [ qw( config ) ],
  lifecycle    => 'Singleton',
  handles      => ['dbh'],
  block        => sub {
       my $s = shift;
       my $config = $s->param('config');
                 return DBIx::Connector->new( $config->('db.dsn'), $config->('db.user'), $config->('db.pass') );
            },
        );
    },
);
 
package Role::Locator;
...
use Module::Runtime qw( use_module );
 
has _locator_class => (
    isa      => 'Str',
    is       => 'ro',
    lazy     => 1,
    default  => sub { 'MyContainer' },
);
 
 has _locator => (
    isa      => 'Bread::Board::Container',
    is       => 'ro',
    lazy     => 1,
    default  => sub { use_module( $self->_locator_class )->instance },
);
 
package ProductVariant;
with 'Role::Locator';
 
sub _load_categories {
...
     my $dbh = $self->_locator->dbh;
}
```
Ok, so this is much better we can now configure which locator instance we use at runtime. We have removed the
dependency on the Bread::board interface. There is no longer a problem with database connections being dropped.
However, our container is still a global singleton, and our class still knows about it, which again, law of Demeter.

## Dependency Injection and Pass it down

For now I've been basically ignoring other classes because with all of these other approaches they aren't really a
concern because you would do the same thing in every class, fetch your service. Much of the code is required here
anyways, we always would have to do the sql, the transforms the loops. Dependency inversion is the opposite, do not
think of how to retrieve the dependency instead have the dependency provided. But this becomes tricky to think of when
you're 3 or more levels deep in your hierarchy. One way to do it simply pass the reference. We create a specific
problem here, our Repository lifecycle is a singleton so we need to ensure re-connection, thus we must inject the
connector which means we are immediately dependent on the DBIx::Connector interface. This doesn't seem that tricky
until you add more than one service, which still may not seem that bad, until you have to add one later, and oh my god,
now you're modifying several classes.
```perl
package MyContainer;
use 5.010;
use Moose;
use Bread::Board::Declare;

has config => (
    isa       => 'Config::Merge',
    is        => 'bare',
    lifecycle => 'Singleton'
    block     => sub { Config::Merge->new('/path/to/config'); },
):

has conn => (
  isa          => 'DBIx::Connector',
  dependencies => [ qw( config ) ],
  lifecycle    => 'Singleton',
  handles      => ['dbh'],
  is           => 'bare',
  block        => sub {
       my $s = shift;
       my $config = $s->param('config');
                 return DBIx::Connector->new( $config->('db.dsn'), $config->('db.user'), $config->('db.pass') );
            },
        );
    },
);

has controller => (
	isa   => 'MyController',
	is    => 'bare',
	infer => 1,
);

has product_repository => (
	isa       => 'Repository::Product',
	is        => 'bare',
	lifecycle => 'Singleton',
	infer     => 1,
);
 
package Role::DBH;
...
use Module::Runtime qw( use_module );
 
has _conn => (
	isa      => 'DBIx::Connector',
	is       => 'ro',
	required => 1,
);
 
package Repository::Product;
with 'Role::DBH';

sub get {
	... #deal with input and sql creation
	
	my $data = $self->_conn->dbh...
	
	... # transform the data structure
	
	return Product->new({ _conn => $self->_conn, %{ $data } });

}

package Product;
with 'Role::DBH';

sub variants {
	... #deal with input and sql creation
	
	my $data = $self->_conn->dbh->...
	
	... # transform the data structure
	my @variants;
	foreach my $params ( @$data ) {
	    ProductVariant->new({ _conn => $self->_conn, %{ $data } });
	}
	return @variants;
}

package ProductVariant;
with 'Role::DBH';
 
sub categories {
	... #deal with input and sql creation
	
	my $data = $self->_conn->dbh->...
	
	... # transform the data structure
	my @cats;
	foreach my $params ( @$data ) {
	    push @cats, Category->new({ _conn-> => $self->_conn, %{ $data } });
	}
	return @cats;
}

package Categories;
with 'Role::DBH';

package MyController;
use Moose;

has _products => (
	isa      => 'Repository::Product',
	is       => 'ro',
	required => 1,

sub do_the_needful {
	...
	
	my @variants = $self->_products->get($id)->variants;
	
	foreach my $variant ( @variants ) {
		my @cats = $variants->categories;
	}  
}
```

## Dependency Injection with Providers

This next and final sample show's one way of doing this with Providers. A little context on a Provider first, a
Provider is simply an object that can be used to retrieve a an instance of an object you need. It's really just a kind
of factory, but tends to be specific to dependency injection, in scenarios where you need a new instance of an object
each time. It seems that it might also work well for other cases, such as objects with a longer lifespan than a new
instance on every request from the injector, but shorter than a permanent singleton. In short a provider should be able
to provide you with an instance on request, without requiring to to depend on retrieval.

The code that I'm demonstrating will not work currently practical scenario, meaning one where variant parameters are
required. [I've opened a bug about resolving the issue][https://github.com/stevan/BreadBoard/issues/28]. In the mean
time, the patch is simple and you could apply it yourself. You could use `BUILDARGS` to rename an alternate key to the
primary hashkey, in your models. You could also just define each model service one at a time instead of looping them,
and actually validating their parameters.
```perl
use 5.014;
use warnings;

package MyContainer {
        use Moose;
        use Bread::Board::Declare;
        use Bread::Board;
        use Bread::Board::Dumper;
        use DBIx::Connector;

        my %models = (
                Product        => { _provider => '/provider/ProductVariant' },
                ProductVariant => { _provider => '/provider/Category'       },
                Category       => {                                         },
        );

        sub BUILD {
                my $self = shift;

                container $self => as {
                        container provider => as {
                                service dbh => (
                                        lifecycle => 'Singleton',
                                        block     => sub {
                                                return sub {
                                                        $self->resolve( service => '/conn' )->dbh;
                                                };
                                        },
                                );
                                foreach my $model ( keys %models ) {
                                        service $model => (
                                                lifecycle    => 'Singleton',
                                                block        => sub {
                                                        my $svc = shift;
                                                        return sub {
                                                                $self->resolve(
                                                                        service    => "/$model",
                                                                        parameters => $_[1] // {},
                                                                );
                                                        };
                                                },
                                        );
                                }
                        };
                };

                print Bread::Board::Dumper->dump( $self );
        }

        has conn => (
                isa          => 'DBIx::Connector',
                lifecycle    => 'Singleton',
                is           => 'ro',
                block        => sub {
                        return DBIx::Connector->new( 'dbi:NullP:' );
                },
        );
         
        has controller => (
                isa   => 'MyController',
                is    => 'ro',
                infer => 1,
        );
         
        has products => (
                isa          => 'Repository::Product',
                is           => 'ro',
                lifecycle    => 'Singleton',
                infer        => 1,
                dependencies => {
                        _provider     => '/provider/Product',
                        _dbh_provider => '/provider/dbh',
                },
        );


        has $_ => (
                isa          => $_,
                is           => 'bare',
                infer        => 1,
                dependencies => { _dbh_provider => '/provider/dbh', %{ $models{$_} } },
        ) foreach keys %models;
}
 
package Role::DBH {
        use Moose::Role;
         
        has _dbh_provider => (
                isa      => 'CodeRef',
                traits   => ['Code'],
                is       => 'bare',
                required => 1,
                handles  => {
                        _dbh => 'execute',
                },
        );
}
 
package Repository::Product {
        use Moose;
        with 'Role::DBH';
         
        sub get {
                my $self = shift;
        #       ... #deal with input and sql creation

        #... transform the data structure

                return $self->_new_product({});
        }

        has _provider => (
                isa      => 'CodeRef',
                traits   => ['Code'],
                is       => 'bare',
                required => 1,
                handles  => {
                        _new_product => 'execute',
                },
        );
}

package Product {
        use Moose;
        with 'Role::DBH';

        sub variants {
                my $self = shift;

        # pretend where processing a list with dbh and mutating to constructors

                return ( $self->_new_variant({}) );
        }

        has _provider => (
                isa      => 'CodeRef',
                traits   => ['Code'],
                is       => 'bare',
                required => 1,
                handles  => {
                        _new_variant  => 'execute',
                },
        );

}

package ProductVariant {
        use Moose;
        with 'Role::DBH';

        sub categories {
                my $self = shift;
                return ( $self->_new_category({}) );
        }

        has _provider => (
                isa      => 'CodeRef',
                traits   => ['Code'],
                is       => 'bare',
                required => 1,
                handles  => {
                        _new_category => 'execute',
                },
        );
}

package Category {
        use Moose;
        with 'Role::DBH';
}

package MyController {
        use Moose;
        use Test::More;
        use Scalar::Util qw( refaddr );

        sub test {
                my $self = shift;

                my $product      = $self->_products->get;
                my ( $variant  ) = $product->variants;
                my ( $category ) = $variant->categories;

                isa_ok $product,  'Product';
                isa_ok $variant,  'ProductVariant';
                isa_ok $category, 'Category';

                isa_ok $product->_dbh,  'DBI::db';
                isa_ok $variant->_dbh,  'DBI::db';
                isa_ok $category->_dbh, 'DBI::db';

                is refaddr($self->_products->_dbh), refaddr($product->_dbh), 'refs are the same';
        }

        has _products => (
                isa      => 'Repository::Product',
                is       => 'ro',
                required => 1,
        );
}
use Test::More;

my $di = MyContainer->new;

$di->controller->test;

done_testing;
```

You may note that I've removed the config, this was simply so I could build the code out so it works in completion. It
maybe advantageous not to put config processing code in the Dependency injector, but rather provide the config to
[Bread::Board::Declare][bbd] at the constructor via required services. This way of doing things requires much more
code, but is also much more flexible. Every piece of the model, even those hat could not normally be accessed by the
injector, can now have it's dependencies injected to it.

[repo]: http://www.martinfowler.com/eaaCatalog/repository.html
[svclocator]: http://martinfowler.com/articles/injection.html#UsingAServiceLocator
[issue28]: https://github.com/stevan/BreadBoard/issues/28
[bbd]: https://metacpan.org/pod/Bread::Board::Declare
