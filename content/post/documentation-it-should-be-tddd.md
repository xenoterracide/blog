+++
title = "Documentation! it should be TDDD"
date = 2010-05-09T22:21:00Z
updated = 2010-06-11T16:49:15Z
categories = ["Editorial"]
aliases = ["/2010/05/documentation-it-should-be-tddd.html"]
blogimport = true 
type = "post"
+++

UPDATE: I shouldn't have said what I said about RJBS and have [apologized publicly][apology]

Test Driven Development should be Test and Documentation Driven Development. I don't want to read your code to figure
out how to use it. I don't really care if you write the Test, documentation, or the feature first. But you should do
all three before moving on to the next one.

I'm gonna pick on Dist::Zilla this seems like a very good tool. Except it's documentation is so sub par it's not funny.
The [new documentation][dzil] is quite good, although, I have not decided that I like the 'choose your own adventure' format.
However none of the documentation on [CPAN][dzil] is good. It ranges from [significantly less][tut] than the new
tutorial, to virtually [non-existant][tester], to [completely inaccurate][new].

You know what? It's not acceptable not to write documentation, especially not when you were apparently paid to do it
and have shown that you are capable of doing it. Some people I've talked to think not documenting things is ok. I don't
think it is, and it's yet another one of my unpopular opinions. Sure you can not document them... If someone else has
volunteered and is actually doing it.

Yet maybe this is one of the reasons perl has fallen by the wayside. Documentation is usually either cryptic,
incomplete, or inaccurate. Even the core docs like [perlintro][intro] has inaccurate and potentially damaging
information. Why are we so surprised that so many people don't know how to program perl in a modern way if the
documentation is so bad?

Documentation should explain who, what, when, where, why and how. Who should use this module, what they should use it
for, when they should (and shouldn't) use it, where they should use it, why they should use it, and how to use it. 
Unfortunately most documentation fails at all but the how, and the what. *Who* is an Abstract, and Description
thing, you should consider who doesn't need it. *Why* why can be tightly related to who, but really I want to
know why this module and not another, consider who needs a templating engine vs why it should (or shouldn't) be 
HTML::Zoom. When is also closely related to the previous 2 and will probably be answered with them, although consider
when to use a for loop vs when to use a while. *Where* could be anywhere from top of file, to your view in a
catalyst program. How is obviously the actual code and should be covered in the synopsis. Sometimes you'll have to
think if you've answered all these, but if you manage to answer them all in a comprehensive and complete manner then
you're documentation is probably good enough to be useful.

I don't expect people to be perfect in this, but some effort is better than none. Also if you write documentation,
you may end up answering less questions, and save people many hours finagling a problem.

I'm sure even my modules fail to meet my own standards.

The best documentation I've seen is PostgreSQL's and Gentoo's.

**Update:** perlintro was fixed in 5.12.1

[apology]: http://xenoterracide.blogspot.com/2010/06/i-am-explicitive-or-formal-apology-to.html
[docs]: http://dzil.org
[dzil]: https://metacpan.org/pod/Dist::Zilla
[tut]: http://search.cpan.org/~rjbs/Dist-Zilla-2.101241/lib/Dist/Zilla/Tutorial.pm
[tester]: http://search.cpan.org/~rjbs/Dist-Zilla-2.101241/lib/Dist/Zilla/Tester.pm
[new]: http://search.cpan.org/~rjbs/Dist-Zilla-2.101241/lib/Dist/Zilla/App/Command/new.pm
[intro]: http://perldoc.perl.org/perlintro.html#Conditional-and-looping-constructs
