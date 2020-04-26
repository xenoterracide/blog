+++
title = "Would You Miss Autoderef in 5.20? solutions in search of a problem"
date = 2013-11-02T02:13:00Z
updated = 2013-11-02T02:33:11Z
tags = ["perl"]
categories = ["Development"]
blogimport = true 
type = "post"
aliases = ["/2013/11/would-you-miss-autoderef-in-520.html"]
+++

This is a response to Chromatics blog post [Would You Miss Autoderef in 5.20?][mpb], because I can't ever get comments
to work on his MT for something like a year (500, or some blogger openid incompat).

In all honesty I don't find either particularly interesting. I've too often been targeting 5.8 or 5.10 for syntax...
`@{ $foo }` is really the most I've ever needed,`@$foo` is nicer, but beyond that don't need it. I can't figure out the
value of either autoderef or postfix deref, neither of these seem to be solving actual pain points, I think perhaps
they're a solution in search of a problem.  Maybe I just need someone to point out a good use case that this stuff is
solving.

Where are the things I actually need? Here's hoping that 5.20 will get method signatures, or exception handling or
maybe figure out how to get given/when out of experimental, something useful.

I really do appreciate all the hard work the people who are improving core perl are doing, and it's all needed. Things
like `__SUB__` and `my sub {}` are absolutely awesome, as well as all the work on unicode, and other general
improvements. Maybe lexical subs will be moved to stable? but I doubt it. Basically I want something that I can point
to my friends outside of the echo chamber, something they could look at and say, yeah that's cool, Perl is moving
forward.

[mpb]: http://www.modernperlbooks.com/mt/2013/11/would-you-miss-autoderef-in-520.html
