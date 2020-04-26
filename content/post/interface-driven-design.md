+++
title = "Interface Driven Design"
date = 2012-11-03T05:37:00Z
updated = 2013-05-25T13:30:35Z
categories = ["Software Design"]
tags = ["Business::CyberSource", "object oriented design",  "perl", "Interfaces"]
aliases = ["/2012/11/interface-driven-design.html"]
blogimport = true 
type = "post"
+++

## What is Interface Driven Design?

Interface Driven Design simply means that you should design your software around a flexible, easy to use, easy to
understand interface. This is easy to achieve if your objects are of [SOLID][solid] design. There is a simple table and
reference link if you're not familiar with the principles.

## My Work is SOLID already

Then you're on the right track but it's not enough if you don't fully marry the concept to best practices. I've seen
quite a bit of work that's SOLID enough but fails to provide good interfaces.

## Why is this so important?

Getting your interfaces correct is important because someone should be able to replace your code with new code, or
subclassed code and it should still work.

### Example: LWP::UserAgent and Mojo::Useragent

These two libraries do exactly the same thing, they provide an HTTP Client. However, they do not conform to the same
interface. This means That if you're using Mojolicious to write a web application, but require an external library to
interface with a remote API, because its interface will make your development easier, you cannot change it's use of
LWP::UserAgent to Mojo::Useragent. Now you've added another dependency and complexity to your application.

### Example: DBI

DBI is an example of a common interface to many different database drivers that do similar things, but underlyingly
with different syntax. This allows you to use a common interface and ignore the differences in implementation between,
say DBD::mysql and DBD::Pg.

## How do I get there?

To begin, and as a general rule your interface should conform to style choices in the language you're using. Meaning
that in Perl you should use `$obj->foo_bar` not `$obj->getFooBar` as it is the style most objects use.

### Domain Driven Design

The first thing I suggest doing is design your initial interface using Domain Driven Design. Look at the common
language used to describe the thing that you're building, and name your package, classes, methods, functions, after
words from the common language. You're writing a new HTTP client? you probably have some concept of POST, so 
`$client->post`makes a lot of sense. If you're writing a billing system you may have some concept of `Invoice->process`.
When doing Domain Driven Design your Interface should be easy to understand by an Expert in that domain (regardless of
whether they are technically savvy ). Example if I told a Billing expert I was writing the code for `Invoice->process`
they probably would have no idea what the internals meant, but they should easily understand the purpose and a general
idea of what it actually does. (note: having an Invoice object might be bad, as an invoice is a request for payment on
a Sale, and a receipt is a record of, therefore they are just views on a Sale, but that's a more complex notion).

### Pure Fabrication

Unfortunately sometimes what you're creating has no real world equivalent (actually HTTP is an example that is now more
of its own domain). So you're making it up as you go. In this case you need to create objects that are a 
[Pure Fabrication][grasp]. When creating interfaces for these I suggest looking to patterns, simple interfaces, and the
interfaces for similar things in other projects, or languages. Use names that are as descriptive as you can get.

### Existing Interfaces

You want to do this whenever there is an existing implementation that's not good enough, but has a decent interface.
Perfect examples are DBI and LWP. They both have  good interfaces, but there's a chance that the implementation isn't
good enough (or you have need of a nonexistant driver).

An example with be AnyEvent::HTTP::LWP::UserAgent. If you're using AnyEvent you'll probably know you don't want LWP's
blocking interface, but unfortunately the library you need to use uses LWP, what a dilemma. You could rewrite the
library entirely to use AnyEvent::HTTP, but this will be both tedious and error prone. However,
Anyevent::HTTP::LWP::UserAgent provides an LWP Interface, this means that you can simply substitute it in the library 
hopefully the library made this easy by following the Inversion of Control Principle to be discussed in a future post).

Like AnyEvent::HTTP::LWP::UserAgent you may need to build a Facade interface that mimicks another interface. It would
be better to start with this interface, but then again sometimes that's not ideal either.

### Combinations

Sometimes you have to combine all of these strategies. Business::OnlinePayment::CyberSource (BOPC) and
Business::CyberSource ( things I'm responsible for ) are good examples. Business::CyberSource was written because BOPC
2.x was no longer maintained and relied on a proprietary library which was not 64 bit compatible. I decided that I did
not like the Business::OnlinePayment interface (and still don't to be honest ) and so set out to create a new one.

My first attempts was in retrospect focussed more on creating a perlish API than a Model driven API. In the long run
this caused significant pain and resulted in some bad code. As of version 0.7.x (in TRIAL) Business::CyberSource's API
is modeled after the remote API that CyberSource provides, and as such it has become much easier for me to provide
access to new remote API features. Because I have continued work on ensuring that my Interface only relies on it's own
interfaces it should now be trivial to replace any single piece of Business::CyberSources API. Don't want to use my
request objects? you could simply pass an object that can serialize to a hashref that looks like what
XML::Compile::SOAP expects. Any Expert at CyberSource should be able to read and understand my API (not tested), where
they might not understand BOPC's. Unfortunately to get to this point I've had to break my interface several times.

Later due to new business concerns we had  a need to conform to Business::OnlinePayments Interface, and so we rewrote
BOPC to use Business::CyberSource as the backend. It does not provide access to all of the features, but it can be used
in anything that knows how to use a Business::OnlinePayment API. I would have preferred to have this done sooner, but
due to tuits and business constraints it was put off.

If you read the Source of either of these you will find a few fabrications, such as the use of Factories.

## Interface as a Language Feature

Many languages support specifying the interface via a language feature. If your language supports this you should take
advantage of it. Unfortunately Perl's simple `can` support really isn't enough, and Moose's Interface as
Role support doesn't really work due to ordering issues. (I implemented the interface but unfortunately due to ordering
my implementation is runtime and happens comes after the compiletime requirement ). I will say though that I believe it
is more important to provide the actual calling convention in a dynamic language like Perl, than use of an actual
interface. At least with Moose I feel that an interface is (generally) as concrete as the isa for the class, and so I
don't bother checking them, they are an implementation detail.

## Conclusion

Ultimately the goal is to create easy interfaces to understand, use, and properly reflective of the problem. By doing
so you also make concrete implementations easy to update and reuse without breaking your clients.

[solid]: https://en.wikipedia.org/wiki/SOLID_(object-oriented_design)
[grasp]: https://en.wikipedia.org/wiki/GRASP_(object-oriented_design)#Pure_Fabrication
