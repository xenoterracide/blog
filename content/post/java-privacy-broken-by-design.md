+++
title = "Java Privacy, broken by design"
date = 2014-06-03T04:00:00Z
updated = 2014-08-19T23:10:41Z
tags = ["object oriented design", "SOLID Principals", "polymorphism", "java"]
aliases = ["/2014/06/java-privacy-broken-by-design.html"]
categories = ["Editorial", "Software Design"]
blogimport = true 
type = "post"
+++

It is worth noting that none of the following arguments apply to anything using the keyword `static` which makes things
more procedural (or in some cases functional, than Object Oriented.

The suggestion in Java is to give the least required permission, but this,  in my humble opinion, violates the 
[Open-Closed Principle][ocp]. Java has [four privacy levels][ac]. Giving something the least permission required to
function is fine in a Security context, privacy in programming however is simply there to discourage developers from
doing stupid things. In most cases, unlike security, it only makes them difficult, not impossible. I believe that any
SOLID principle should make your code more easily extensible, so while in fact Java's privacy is not in literal
violation of Open-Closed, it does make extension more difficult than it otherwise should be, thus violating the spirit
of the principle.

Before I continue on to how I think Java's design, and common usage, violates the Open Closed Principle, I should
explain how I interpret the Principle, as my interpretation appears to be slightly different from what's on Wikipedia.
The Principle as described on Wikipedia appears to be combining it with two other SOLID Principles, namely Liskov
Substitution and Interface Segregation. So first let's assume that The principle stands alone, and that although it'd
be bad design to not be completely SOLID, Open-Closed by itself does not require a subclass to support the same
interface. Let's also assume that Not modifying the source to add features is also an unrealistic expectation.
The purpose of Open-Closed is to ensure that your subclasses are not modifying the the structure or data of their 
child classes and that a child may easily add to, or change the behavior it got from its parent (Liskov says that it
must be substitutable for its parent).

First let's talk about final, marking a class as final, means you can't
extend it. This by the very definition is in violation of Open-Closed, because the class is not Open for extension.
Classes such as `UUID` are marked `final`, you might ask, why would I want to extend a `UUID`? maybe I want to give it a
`toURISafeBase64` method. That wouldn't break any of the orignal behavior, and is almost as legitimately belonging as
representing the UUID as hex. What if I wanted to extend a nested final class like an `Iterator` on a `Map`? I can't do
that, which means I have to completely reimplement the Iterator to add simple functionality. In fact the way those are
implemented I have to implement much more than just the Iterator.

It is recommended by the official Java Docs,
and the community, to make member variables private unless otherwise necessary. Private variables are only accessible
to the current class and nested classes, they are not visible to subclasses, in or out of the package. In my opinion
this violates Open-Closed because now, if I subclass I need to reimplement all the fields, or use getters/setters.
Getters and Setters for every single attribute are actually almost no better than the attribute itself, and an object
that is nothing more than those is an [Anemic][anemic]. Now it could be argued that making subclasses call methods
makes them more... impervious to change, because if you change the data structure you can preserve the methods. The
problem is that most classes wouldn't use their own getters internally, and thus break this, because then extending
that getter won't actually modify the class as completely as desired. Also remember that subclasses are by definition,
tightly coupled, usually changes to the superclass require taking a look at the subclasses. So if you are using getters
and setters to ensure extensibility and preserve internal/external interface changes, use them exclusively, meaning
only they can have raw access, all constructors, and business logic methods must go through them. At that point they
are the replacment for direct member access and private won't matter as much (I will probably advocate a variant of
this in the next article). However if you still want to access some member data hidden by the class directly, you
should ensure that your subclasses can easily do so as well. You should *only* make a member private if it would
actually cause a bug in any subclass.

So if we go on to assume that all subclasses, even ones in a different
package (because you know people using your code are going to extend things) then we should be making all members
protected. This would mean that all subclasses could reuse the member variables. Of course the problem is now your data
is not encapsulated in *your* package, once a member variable is not private, is is available to your entire
package. To me this also seems like a bad idea, other classes in my package don't need to see my objects internals
unless they're a subclass. So now you have to choose, make all classes easily extended? or protect people who are
programming in your package from themselves. You can probably control who's modifying your package and how, and have
static code analysis to check that you're not calling obj.foo only this.foo. But nothing can give you back 
extensibility you've taken away (outside of adding it back).

So let's look at interfaces, interfaces generally have two options, public, or protected. This is fine, but has a
problem, protected interfaces are only applicable to the package that has the interface defined. Methods implementing
the interface must have the same privacy level. Most of the time what I actually want is an interface which I've
defined globally as a contract, but I want the implementations to only be called by their package. For example, a
DAO (Data Access Object) might be able to share the same interface (with judicious generic usage), between entities.
However if you do this, you may find that your interface must be public, so it can be between packages, now the DAO
itself must have these methods as public, even if it's being called only by something in the same package, because the
interface was public so that the interface could be shared. I don't see that you can get away with this whether you
use package by feature or package by layer. If you follow this through with previous design thoughts such as
everything is an Interface, and those end up being public, and you want nice subclassibility, whether through
protected members or through interfaced getters/setters, now everything is public, and we've completely lost any real
encapsulation.

So how could it be done better? have a privacy type subclass which makes the method or member
available to only subclasses and not throughout the package. Allow interfaces that have global definitions, but
implementations of the methods can be at a package or subclass level. I feel like this could still be accomplished,
perhaps by creating an interface type that is a "contract", and a new privacy keyword for "subclass". Contracts could
define that methods be subclass, or protected, in their implementation. At that point you could have all kinds of
methods that are still hidden to the general world. You could then build package by feature, have all methods that are
required within the package have contracts, but share contracts between features, so all CRUD controllers would have
the same method signatures, all repositories would share signatures, etc, etc.

What if I actually want more
privacy? well you could not share interfaces between packages, and then have interfaces not be public. You could also
not use an interface at all unless it's for a method on your bounded context that must be public. You can also say that
ease of extensibility is not a goal and continue to not use your getters/setters internally, and yet make your members
private.

You could also say, privacy is irrelevant, if the language is then preventing good, SOLID, design. Specifically here,
Open-Closed, Liskov Substitution, and Interface Segregation. If you go this route you'll need conventions, and to trust
other developers, because a lot of things will be public or protected. I recommend Perl's convention of prefixing
subclass private methods with _ and assuming that all member fields are subclass/trait private and should never be
called outside of their inheritance hierarchy.

**Update** Java 9 is getting private methods in interfaces, still no subclass only modifier though.

[ocp]: https://en.wikipedia.org/wiki/Open/closed_principle
[ac]: http://docs.oracle.com/javase/tutorial/java/javaOO/accesscontrol.html
[anemic]: http://www.martinfowler.com/bliki/AnemicDomainModel.html
