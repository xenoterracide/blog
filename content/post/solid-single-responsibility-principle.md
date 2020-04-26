+++
title = "Single Responsibility Principle"
date = 2013-08-08T21:32:00Z
updated = 2013-08-08T21:32:11Z
draft = true
blogimport = true 
type = "post"
tags = ["SOLID Principals"]
categories = ["Software Design"]
+++

[SOLID] is an acronym which stands for: Single Responsibility, Open-Closed, Liskov substitution, Interface Segregation,
and Dependency Inversion. If you follow these principles your software will be easy to test, easy to modify, and
generally easy to maintain.

## Single Responsibility Principle

> In object-oriented programming, the [single responsibility principle (SRP)][srp] states that every class should have
a single responsibility, and that responsibility should be entirely encapsulated by the class. All its services should
be narrowly aligned with that responsibility.

I agree with this, but take it a bit further. Single Responsibility does not only apply to your class, but your
application, suite of application, variables, methods, functions, routes, basically every single building block. This
becomes even more obvious in ambitious systems like Perl 6 where everything is an object, even down to the syntax. To
me the Single Responsibility principle is the most important principle, hardest to get right, and you could almost
derive the other principles from it.

[solid]: https://en.wikipedia.org/wiki/Solid_(object-oriented_design)
[srp]: https://en.wikipedia.org/wiki/Single_responsibility_principle
