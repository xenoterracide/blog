+++
title = "Solving code generation problems in dzil"
date = 2010-06-26T06:16:00Z
updated = 2011-05-28T15:27:20Z
tags = ["cpan",  "perl", "Dist::Zilla"]
blogimport = true 
type = "post"
aliases = ["/2010/06/solving-code-generation-problems-in.html"]
+++

Firstly I want to clarify a bit on my opinions of [PluginBundle::USERNAME][post] modules, as some comments there have
inspired this post. I don't think you should use them because it makes it harder to disableplugins, and I think Robin 
Smidsrød put it best:

> Mostly it is because the Dist::Zilla::PluginBundle::USERNAME doesn't actually say anything about its intention. It
only says use Dist::Zilla as this person does, but what does that actually mean? If you don't know the person it
doesn't really tell you anything.

> I'd much more prefer PluginBundles that actually advocate certain types of standards or behaviors.

Essentially Bundle's like `@Git` and `@Basic` don't cause problems because they're generic well defined and contained
within their distributions. A bundle that wasn't contained within its dist might not cause a problem if it's for a
generic well defined purpose. I'd be in favor of a BundleQATests or similar so long as the author was considerate that
some tests (`PodSpellingTests`) don't work well on some *nix distributions and isn't included in the bundle. But 
something like `@Xeno` (my cpan username) would be my own special settings... who wants to use something that's only
for me? and more importantly why would they ever think they have a right to bug something that the name itself implies
it's only for me.

But another problem was brought up... code generation. Here's what Nilson said: 

> dzil is very nice, but I don't if I like the idea of different line numbers and files in the repository vs. the CPAN
release. I'm still trying to fully digest this idea.
 
> remember one of the main mentioned drawbacks of source filters were the possibilities of error messages in the wrong
lines. And now, everyone seems to embrace this without hesitation.

So to start `dzil`'s generation isn't quite as bad as a source filter which will give you the wrong line number period,
using this generation will still give you the right line number for the module being run at the time. It just means the
cpan line number may not match the repo line number. I'm going to show you how to fix this, but you don't always have
to. The most common place I've found problems with in code generation has been tests created by extending 
`InlineFiles`. In these cases you just want to go and bug the author of the Plugin. I think using these plugins is
much better than writing your own EOL Tests, NoTabs, Critic, and Kwalitee etc. It helps keep your dist quality up
without extra work, ultimately leaving you with just the responsibility of writing good tests that are specific to
just your distribution.

Now on to making sure that your repository matches your cpan dist. The first thing you want to do is use
`[Git::CommitBuild]` (if you're not using git look for something like it or write something like it) This will take all
the generated output and commit it to another branch each time you build. Then make sure this branch is pushed to your
public repository. I think the biggest problem this solves is having things like your `README` and your `LICENSE`
actually be in your public repository (if your repo doesn't have a `LICENSE`... what's the legal situation?). If you're
using github you can also change this to be the default branch to be displayed in the repo admin settings.

Next thing is don't use [`::Plugin::Prepender`][plugin-prepender] This will evil-y insert lines at the beginning of
your code which will definitely throw off the line numbers being output. It even seems to suggest using it to insert
`use strict; use warnings;`. If you need output prepended to all files, I suggest writing a plugin that takes advantage
of `dzil new` and maybe just write a script that you can fire off whenever you need to create a new file. This is the
only module I know for sure that does this, but avoid ANY that insert actual code or prepend lines to your files (in a
way that isn't added to your actual `master`/`trunk` that should be patched).

POD can also screw up your line numbers, if you're using `PodWeaver` (or module that has similar side effects) with
`dzil`, which you likely are. [Perl Best Practices][pbp] page 140 will save you here (actual quote pg 475 a summary chapter).

> * Keep all user documentation in a single place within your source file. [Contiguity]
> * Place POD as close as possible to the end of the file. [Position]

This includes the `# ABSTRACT: my abstract here` line. If you put the `# ABSTRACT` and any actual pod after the code
then all the pod generated will be after the code in your build, and thus any line number errors will be correct. Of
course this doesn't save you if your error is in the output pod, but I suspect that's not the original complaint
anyhow, and there are lots of dzil plugins to help you keep your pod sane.

Essentially don't do anything that will change line numbers for the code in the resulting build output. Following this
will ease contribution, and debugging; I do not believe it significantly increases maintainer load. Happy Hacking!

**UPDATE:** just remembered... dzil inserts a `BEGIN` block... sigh... can't win for nothing.

**UPDATE:** I recommend using [`Dist::Zilla::Plugin::OurPkgVersion`][OurPkgVersion] to avoid dzil's `BEGIN` block /
`VERSION` insertion.

[plugin-prepender]: https://metacpan.org/pod/Dist::Zilla::Plugin::Prepender
[pbp]: http://www.amazon.com/Perl-Best-Practices-Damian-Conway/dp/0596001738?ie=UTF8&tag=xenotsblog-20&link_code=btl&camp=213689&creative=392969
[OurPkgVersion]: https://metacpan.org/pod/Dist::Zilla::Plugin::OurPkgVersion
[post]: /post/please-dont-use-distzillapluginbundleusername
