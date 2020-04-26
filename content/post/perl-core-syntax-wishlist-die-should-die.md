+++
title = "Perl Core Syntax Wishlist: die should die"
date = 2012-05-14T06:00:00Z
updated = 2012-05-14T06:00:05Z
categories = ["Perl"]
tags = ["exceptions",  "perl", "perl syntax wishlist"]
blogimport = true 
type = "post"
aliases = ["/2012/05/perl-core-syntax-wishlist-die-should.html"]
+++

I hate [`die`][die] it is, in my humble opinion, one of the worst parts of perl. I really wish it would be deprecated,
and removed, or at least replaced with something that would tell you were the code that was die-ing was being called.
Replace its implementation with that of [Carp's][carp] `croak` or `confess` and I'd be happy. Better yet, let's just
get [real exception support][ex] and deprecate die (even if that's never removed deprecation just make that real big
on its pod). If you're using `die` please stop and [use Carp][carp], anyone using your module will thank (and by thank
I mean not curse) you later.

[die]: https://perldoc.perl.org/functions/die.html
[carp]: https://perldoc.perl.org/Carp.html
[ex]: http://www.xenoterracide.com/2012/05/perl-core-syntax-wishlist-exception.html
