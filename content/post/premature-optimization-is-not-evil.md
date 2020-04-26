+++
title = "Premature optimization is not evil"
date = 2015-01-07T20:46:00Z
updated = 2015-01-07T21:27:45Z
categories = ["Editorial"]
tags = ["rant"]
blogimport = true 
type = "post"
aliases = ["/2015/01/premature-optimization-is-not-evil.html"]
+++

Or rather people should stop saying this because most of the people that say it don't actually seem to actually know
what is meant by "Premature Optimization" or how to determine when it is evil. I've heard people say premature
optimization is evil to asking. "Is there a 3rd party library that does this more efficiently?" (knowing if there are
better options is premature optimization?), "Thinking about architecting your app for horizontal scalability is
premature optimization" (it is if the design is significantly more complex, but if it's just between using REST and
ensuring stateless (which is about the same complexity up front, but it'd be harder to convert later)), "wanting to do
Dependency Injection is..", "making that code easier to read and simpler and thus faster", and on and on. On the other
hand, no one seems to think that requiring Redis, Mongodb, and NodeJS because it's webscale is premature optimization,
even if the clustering is horribly convoluted and you end up in callback hell (not saying you are, just saying).
Basically, you're not asking to do the thing that everyone else is doing, is premature optimization.

So let's talk about what the hell premature optimization is. Premature spending a week making sure you can spin up
infinite instances on AWS because someday you might get slashdotted. Premature optimization is writing a method in a
less than clear manner because you think it's faster. Premature optimization is rewriting `String.format` to 
`StringBuilder` because `StringBuilder` is faster. Premature optimization is any time that you write code that is less
readable for the sake of performance, or spend an inordinate amount of time ensuring optimizing it without benchmarking
to see if it's slow. </p><p>I've spent a significant amount of time in the past few months working on optimizing code,
why? because no one ever thought about optimization, it never occurred to the author, in one case, that querying the
same data from a database that had been previously queried, in a loop, outside of a transaction, was inefficient. It
never occurred to the author that not re-fetching from the database to do an on screen sort every time you sort would be
inefficient. Why think about what you're writing? because premature optimization is evil, or at least that's what I'd
be told.

Here's are examples of premature optimization that are not evil. I choose to use `EnumMap` when I'm storing Enum's in a
`Map`, I presume it's more efficient, so I do it, increase in code? a class name to the constructor. `StringBuilder` is
faster than `StringBuffer`, so when I come across `StringBuffer` I convert it, increase in code, none. I use dependency
injection to wire stateless (or unchanging state) singletons so I'm not constantly creating instances, code increase is
use of a DI framework. I use onClick handlers to ensure that things happen lazily, only when needed.

Basically what I'm saying is that "Premature optimization is evil" is sadly used anytime when anyone is even thinking
about anything that could remotely be considered optimization. I personally optimize my code for paradigm or pattern
matching the problem first (which leads to 2 and 3), readability second, performance last. Making smarter decisions
about how to write your code is not premature optimization.

I think the real "evil" is encouraging people not to think about performance, or to further understand their craft.
