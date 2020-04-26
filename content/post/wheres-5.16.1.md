+++
title = "Where's 5.16.1?"
date = 2012-07-24T11:58:00Z
updated = 2012-07-24T14:11:10Z
blogimport = true 
type = "post"
tags = ["perl"]
categories = ["Editorial"]
aliases = ["/2012/07/wheres-5161.html"]
+++

Per the [5.12 release announcement][perl]

> This release cycle marks a change to a time-based release process. Beginning with version 5.11.0, we make a new
development release of Perl available on the 20th of each month. Each spring, we will release a new stable version of
Perl. One month later, we will make a minor update to deal with any issues discovered after the initial ".0" release.
Future releases in the stable series will follow quarterly. In contrast to releases of Perl, maintenance releases will
contain fixes for issues discovered after the .0 release, but will not include new features or behavior.

5.16.0 was released on May 20, 2012, it is over 2 months later, was 5.16.0 so good that there are literally no bugs? or
has something gone wrong with the time based release schedule? I also I note that there was never a 5.14.3. I'm just a
concerned citizen of the Perl community, I don't want to see us go back to the days of pre 5.12, when releases happened
whenever/never.

**Update:** Per RJBS comment below, apparently [there was an interesting bug in require][rjbs] which has just been
fixed and will be getting backported.

[perl]: http://dev.perl.org/perl5/news/2010/perl-5.12.0.html
[rjbs]: http://www.xray.mpe.mpg.de/mailing-lists/perl5-porters/2012-07/msg00954.html
