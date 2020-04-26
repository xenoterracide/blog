+++
title = "Perl Core Syntax Wishlist: an Exception Stack"
date = 2012-05-07T22:30:00Z
updated = 2012-05-11T02:53:31Z
tags = ["exceptions",  "perl", "perl syntax wishlist"]
blogimport = true 
type = "post"
aliases = ["/2012/05/perl-core-syntax-wishlist-exception.html"]
+++

I have come to wish many things were part of [Perl syntax][perlsyn] that are not, and no using external modules is not
enough for me. I think it's time Perl got the features as part of the language itself (and yes I suppose I could settle
for `feature.pm`, and no I'm probably not going to write them myself, I'm not smart enough yet). The first of these is
a proper exception stack. I want to be able to write:
```perl
use 5.018;
use warnings;

sub foo {
    throw Exception->new( 'message' );
}

try {
    foo();
}
catch ( 'Exception' ) {
     say "$_"; # object stringifies
}
catch ( 'Exception::Other' ) {
     say $_->message #also has an accessor
}
finally {
     ...
}
```
I think we need `throw`, `try`, `catch`, and `finally` keywords.  And no I don't think it makes sense to have
`Object->throw`. In fact I think this [Original Perl 6 Syntax Proposal][p6spec] reads like just what we need in Perl 5.
Unfortunately I think [this][p6advent] is what we are getting in Perl 6, which IMO is not as nice.

[perlsyn]: http://perldoc.perl.org/perlsyn.html
[p6spec]: http://www.perl6.org/archive/rfc/63.html
[p6advent]: http://perl6advent.wordpress.com/2011/12/15/day-15-something-exceptional
