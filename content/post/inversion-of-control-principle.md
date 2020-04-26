+++
title = "Inversion of Control Principle"
date = 2013-01-03T21:00:00Z
updated = 2013-01-03T21:00:04Z
categories = ["Perl", "Software Design" ]
tags = ["Bread::Board", "Dependency Injection", "object oriented design",  "perl", "Inversion of Control"]
aliases = ["/2013/01/inversion-of-control-principle.html"]
blogimport = true 
type = "post"
+++

If you're not familiar with the term "Inversion of Control"( IoC ) or "Dependency Injection" ( DI )you may wish to
start with [Martin Fowler's post on the subject][ioc]. If you're looking for a way to do it with Perl, 
[Bread::Board][bbmc] is the way to go. This post however is about the theory behind it, and a path to grokitude if
you're finding the concepts challenging. I should advise that I am not yet a buddha on implementation. 

## What is it?

Now that you're familiar with an understanding of the terms that is not mine (or even if you didn't bother), you may be
wondering what I mean by "Inversion of Control Principle", seeing as how we have the Dependency Injection Pattern and
Inversion of Control Containers. I'm not sure if anyone actually uses the term "Inversion of Control Principle", though
google seems to suggest I am not the first.

The essence of the Inversion of Control Principle is do not attempt to control your code, let its callers control it.
Give your caller as much power as you can. 

This is of course not a very academic statement.

### Dependency Inversion

An important piece of the Inversion of Control Principle is the common "Dependency  Inversion". It is defined as:
> * High-level modules should not depend on low-level modules. Both should depend on abstractions.
> * Abstractions should not depend upon details. Details should depend upon abstractions.

In more laymans terms depend on an interface and not a concrete implementation. For the Perler's. Do not depend on LWP
or DBI, but depend on their interface. This is covered more in my post on [Interface Driven Design][idd]. 

### Lifecycle Inversion of Control

A second piece of Inversion of Control is do not attempt to control your objects lifecycle. This means, do not enforce
a singleton pattern, do not enforce a flyweight pattern, do not enforce an instance. Your objects should be instances
always, if you want a singleton put it at the top level of your application or use a Dependency Injection framework to
manage the lifecycle. Also package variables that are not constants are evil, they will bite you because they are
essentially singletons. Always make your objects instances and let their client code determine their lifecycle.

## How do I do it?

Well you don't have to use a fancy IoC/DI framework to do it. Those are simple tools to make life easier after you've
designed your software to be consumed by them. To properly invert your control you first start by letting go. Huh?
yeah I know.

You start by ensuring that your software has as few concrete dependencies as possible. Like I said you depend on
interfaces. Some concrete dependencies are unavoidable, but try to make them uninteresting, or architecturally
significant.

### With Perl

Start by ensuring that concrete dependencies can be easily replaced at runtime. The easiest way to accomplish this is
by allowing them to be passed to your constructor. when using Moose I tend to use lazy loading defaults a lot. Here's
an example from cybersource.
```perl
sub _build_cybs_wsdl {
    my $self = shift;
 
    my $dir = $self->_production ? 'production' : 'test';
 
    load 'File::ShareDir::ProjectDistDir', 'dist_file';
    return load_class('Path::Class::File')->new(
            dist_file(
                'Business-CyberSource',
                $dir
                . '/'
                . 'CyberSourceTransaction_'
                . $self->cybs_api_version
                . '.wsdl'
            )
        );
}

has cybs_wsdl => (
    required  => 0,
    lazy      => 1,
    is        => 'ro',
    isa       => File,
    builder   => '_build_cybs_wsdl',
);
```
Although I don't consider it likely that anyone will ever need to use another WSDL for Cybersource, one could easily
inject a new Path::Class::File to one when creating the client at the constructor. Another perfect example is the
Client itself. It's designed to be useable as a singleton, because I never intend for someone to need to instantiate
it twice, however I have not made it a singleton and internally I'm not aware of any code that it actually is treated
as such (it appears to usually be instantiated at every request ).

Prefer Class, and Object interfaces to functional ones. Here's some code that demonstrates why.
```perl
# this works

use Class::Load 0.20 'load_class';

my $var = 'baz';

my $class = 'Foo::Bar';

my $result = load_class( $class )->class_method( $var ); # works wonders with ->new

# sometimes this will work, but not always. Try this with the various Dumper functions and see which ones work.

use Module::Load 'load';

my $package = 'Foo::Bar';

load $package, 'function';

my $result = function( $var ); # seems to only work if Foo::Bar's exporter works right, many packages have to be exported at compile time not runtime

# perl doesn't like stuff like this or really anything where I've tried substituting the package name into calling a function. at least not with strict on. This is a large reason I'd avoid functional interfaces. They are hard to substitute at runtime. Maybe I just don't know a good way to do it, without disabling strict, feel free to enlighten me.

load( $package );

my $function = $package . '::' . 'function';

$function->( $var );
```
I have even better real examples, such as my one in interfaces where I replaced LWP with the AnyEvent::LWP... but
unfortunately that code has not been released to the public. The examples given are basically the same principle.

## Dependency Injection Frameworks

Dependency Injection Frameworks are not required to use the Inversion of Control Principle they simply make injecting
your dependencies and managing lifecycles easier. It also allows you to do concrete class substitution from one place
along with deciding that objects lifecyle, and have it's dependencies inserted. When using a DI framework you should
strive to call it only once per controller. Though a book that I've read suggests this is Domain Model controller and
not an MVC controller, I was unable to make a full distinction.

This example runs particularly slow, probably due to reasons that people hate moose, however, if you remove the loop at
the end you'll notice executing one time is about the same as ten, which means that most of this is due to class
compiling and loading, actual runtime is fast (or at least, fast enough).

The important thing to pick up is how the Order object requires a `payment_gateway` that does `submit`, but it doesn't
know how to get that object, or what the implementation will be. Our Dependency Injection framework then simply
provides that dependency as needed. It would be trivial to replace our implementation of payment gateway with a
different one. </p> <p>The code is commented, to explain what it's doing where.
```perl
use 5.014;
use warnings;
use Class::Load 'load_class';

package PaymentGateway {
    use Moose;

    sub submit {
        return int( rand(2) );
    }
    __PACKAGE__->meta->make_immutable;
}

package Order {
    use Moose;

    sub make_payment {
        my $self = shift;

        return $self->_payment_gateway->submit( $self );
    }

    has _payment_gateway => (
        isa      => 'Object',
        is       => 'ro',
        required => 1,
    );

    __PACKAGE__->meta->make_immutable;
}

package AppContainer {
    use Moose;
    extends 'Bread::Board::Container';

    use Bread::Board; #import sugar

    sub _build_config {
        my $self = shift;

        # hardcode for example
        return {
            username   => 'foo',
            password   => 'bar',
        };
    }

    has _config => (
        isa     => 'HashRef',
        is      => 'ro',
        lazy    => 1,
        builder => '_build_config',
    );

    sub BUILD {
        my $self = shift;

        # container can take a container as a parameter. if you do this it
        # will simply add to the container it tooks as a paramter
        # this will be our root container /
        container $self => as {
            # subcontainer path to will be /model
            container model => as {

                #  order uses constructor injection
                service order => (
                    class => 'Order',
                    dependencies => {
                        # use full path
                        _payment_gateway =>
                            depends_on('/remote/payment_gateway'),
                    },
                );
            };
            # another subcontainer /service
            container remote => as {
                service username   => $self->_config->{username};
                service password   => $self->_config->{password};

                service payment_gateway => (
                    class        => 'PaymentGateway',
                    # we don't need more than one instance
                    lifecycle    => 'Singleton',
                    dependencies => {
                        username   => depends_on('username'),
                        password   => depends_on('password'),
                    },
                );
            };
        };
    }

    __PACKAGE__->meta->make_immutable;
}

package Controller {
    # this could be any framework controller
    use Moose;

    has _c => (
        isa  => 'Bread::Board::Container',
        is   => 'ro',
    );

    sub process_order {
        my $self = shift;

        my $order = $self->_c->resolve( service => '/model/order' );

        if ( $order->make_payment ) {
            say 'payment succeeded';
        }
        else {
            say 'payment failed';
        }
    }
    __PACKAGE__->meta->make_immutable;
}

my $app = AppContainer->new({ name => 'app' });

# controller has not idea there's only one app containerr
my $controller = Controller->new( _c => $app );

for ( my $i = 0; $i < 10 ; $i++ ) {
    $controller->process_order;
}
1;
```

[ioc]: https://martinfowler.com/articles/injection.html
[bbmc]: https://metacpan.org/module/Bread::Board::Manual::Concepts
[idd]: /post/interface-driven-design/
