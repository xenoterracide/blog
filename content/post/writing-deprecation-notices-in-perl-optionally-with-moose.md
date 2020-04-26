+++
title = "Writing deprecation notices in perl, optionally with Moose"
date = 2014-07-01T08:49:00Z
updated = 2014-07-01T08:49:00Z
tags = [ "perl", "deprecation", "Moose", "code"]
categories = [ "Development" ]
blogimport = true 
type = "post"
aliases = ["/2014/07/writing-deprecation-notices-in-perl.html"]
+++

Sometimes you want to remove behavior from your code in a future version, here's the right way to do it.
<!--more-->
```perl
package DeperecatedMethod;
use Moose;
our @CARP_NOT = ( 'Class::MOP::Method::Wrapped', __PACKAGE__ );

has value => (
	isa     => 'Int',
	is      => 'ro',
	lazy    => 1,
	default => &random_generator,
);

has seed_value => (
	isa     => 'Int',
	is      => 'ro',
	lazy    => 1,
	default => &random_generator,
);

before value => sub {
	warnings::warnif('deprecated',
		'attribute `value` is deprecated use `seed_value`'
	);
};

before mymethod => sub {
	warnings::warnif('deprecated',
		'method `mymethod` is deprecated use `random_generator_instead`'
	);
};

sub mymethod {
  return random_generator;
}

sub random_generator {
  return 9;
}

1;
```
Here's the quick of how it works, the `before` has to come after attributes because the methods aren't yet created.
Using `before` also means it'll always run with your method, without actually touching your method, insuring no
accidental consequences to your method. The `@CARP_NOT` ensures that the warning thrown doesn't show a line
number in your package, or from within where Method Modifiers are actually run. `warnings::warnif( 'deprecated', ...`,
ensures that these warnings are only emitted if you have the deprecated category enabled. But what if people don't have
warnings enabled? um... oh well? that's there problem because what if people do and they want to silence these until
they can get to them. I highly suggest putting the name of the method being called and it's successor into the message
so that people know how to correct their code.

If you don't want Moose, just don't use the method modifier and put `warnings:warnif` directly in your code. if you're
using a different AOP before, modify `@CARP_NOT` to have the correct module.
