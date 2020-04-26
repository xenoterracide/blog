+++
title = "The ShareDir Problem"
date = 2014-02-27T15:47:00Z
updated = 2014-02-27T16:01:42Z
tags = ["perl"]
categories = ["Perl"]
aliases = ["/2014/02/the-sharedir-problem.html"]
blogimport = true 
type = "post"
+++

Some of you may have noticed a while back that converted [Pod::Spell][ps] to the use of 
[File::ShareDir::ProjectDistDir][fsp] instead of keeping the wordlist in [`Pod::Wordlist::__DATA__`][pw]. This move was
made in conjunction with making Pod::Wordlist an Object, and in preparation for a time when you'll be able to specify
your own wordlist file. It was also made so that non technical contributors could more easily update the wordlist
without going near anything that looked like code.

So why shouldn't you put them in `__DATA__`? According to [File::ShareDir][fs]

> Quite often you want or need your Perl module (CPAN or otherwise) to have access to a large amount of read-only data
that is stored on the file-system at run-time. On a linux-like system, this would be in a place such as `/usr/share`,
however Perl runs on a wide variety of different systems, and so the use of any one location is unreliable. Perl
provides a little-known method for doing this, but almost nobody is aware that it exists. As a result, module authors
often go through some very strange ways to make the data available to their code.

> The most common of these is to dump the data out to an enormous Perl data structure and save it into the module
itself. The result are enormous multi-megabyte .pm files that chew up a lot of memory needlessly.

> Another method is to put the data "file" after the `__DATA__` compiler tag and limit yourself to access as a
filehandle.

> The problem to solve is really quite simple.
> 1. Write the data files to the system at install time.
> 1. Know where you put them at run-time.

Knowing where you put them at run-time is actually still a problem, because, we don't develop in the same spot that
perl installs stuff. The first portion of the problem is, "my tests can't find my sharedir file". So
[Test::File::ShareDir][tfs], which overrides the  File::ShareDir method. People say, use Test::File::ShareDir, it
solves the pain, well that's not true, they're missing a different pain. What happens if you're trying to run, say
`bin/podspell` from the git directory? oh right now it can't find the sharedir file again. In that case I could
probably work around it, but it's a mild symptom of a greater problem I've encountered, people aren't deploying
CPAN modules, they're deploying from git. Now I could say, "not supported", but unfortunately I'd usually have to
say that to my current boss, or coworker, whomever that may be (and I tried it, didn't work). This isn't actually
the root of the problem with Pod::Spell, but I guarantee it was a problem with [Business::CyberSource][bc]. Mostly I
feel like leaving Pod::Spell this way is helping to weed out the issues people will have with
File::ShareDir::ProjectDistDir.
 
 So what do I think the solution is? There are obviously numerous "social" problems here, that I don't think can be
 easily solved. I'm sure that [Kent Fredric][kf], has a better grasp than I of the technical solutions. Though I have
 had one reoccurring idea which is apparently not tangible without significant effort. Have a searchable sharedir path,
 like in unix `PERL5_SHAREDIR="./share:$DETECTED_DEV_DIR:$PERL5_LIB..."`, and try looking for the "file in the path"
 until you find it, then cache that location in memory so you only have to search once per run. This is probably not a
 good solution for various reasons, or perhaps it's certainly grossly oversimplified in how it could work.
 
 Ultimately, there isn't a good solution right now, and I'm not sure we've actually thought of one.

[ps]: https://metacpan.org/module/Pod::Spell
[fsp]: https://metacpan.org/pod/File::ShareDir::ProjectDistDir
[pw]: https://metacpan.org/module/Pod::Wordlist
[fs]: https://metacpan.org/pod/File::ShareDir
[tfs]: https://metacpan.org/pod/Test::File::ShareDir
[bc]: https://metacpan.org/module/Business::CyberSource
[kf]: https://metacpan.org/author/KENTNL
