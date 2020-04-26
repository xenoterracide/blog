+++
title = "Making re.pl usable"
date = 2010-07-25T18:00:00Z
updated = 2013-03-20T18:20:18Z
tags = [ "perl", "tips", "Devel::REPL", "modern perl"]
blogimport = true 
type = "post"
aliases = ["/2010/07/making-repl-usable.html"]
+++

For starters I'm not a super fan of interactive shell's for non-system shells. I don't see the point as much. When I
first tried [Devel::REPL][develrepl] I didn't like it at all, compared to every other interactive shell I've used
(with the notable exception of Oracle's SQLPLUS, which is the worst shell ever) it was an unfriendly toy not worthy of
mention. I couldn't go up in history, there was no tab completion, and all in all the default output was ugly no colors
or formatting. I noticed in the docs

> By default the re.pl program looks for (all of the following code goes in this file) `$HOME/.re.pl/repl.rc`, and runs
whatever code is in there as if you had entered it at the REPL shell yourself.

and that `re.pl` has plugins, I also noticed some strange syntax and what seemed to be overly complicated code for just
getting a few things working. Sure the file supports perl, but do I really want to write full perl programs in an RC
file just to use plugins?

Well fortunately it's not as complex as all that. First thing you want to do is set some good perl defaults.
```perl
use 5.012;
use warnings;
```

Since this is our private shell might as well get all the current features of perl. Of course if you want to have other
modules always accessible load them here to.

Now lets get some sanity into the shell by using plugins for REPL. It's really much simpler than the docs say.
```perl
load_plugin 'PluginName';
# or
load_plugin qw(
   Plugin
   NextPlugin
   YetAnotherPlugin
   ...
);
```
Wow that's way easier than what any of the documentation describes.

Ok let's look at some plugins. First if your typing isn't perfect you've probably noticed that you can't use your up
arrow to go back in history, to fix this you can enable Readline support by loading 
[Devel::REPL::Plugin::ReadlineHistory][readline] like so, 
```perl
load_plugin qw(
    ReadLineHistory
);
```
This will allow you to up arrow to go back through lines you've previously typed, which you can then edit just like
your normal *nix shell prompt. This one thing makes re.pl about 100x less annoying.

But I wish I had some kind of tab completion so I didn't have to type so much. Hey there's a whole set of completion
plugins, lets enable those too.
```perl
load_plugin qw(
    Completion
    CompletionDriver::INC
    CompletionDriver::LexEnv
    CompletionDriver::Keywords
    CompletionDriver::Methods
    ReadLineHistory
);
```


So the Keywords Driver allows you to type `pri< tab >`and it'll complete `print` plus showing you `printf` just like
your *nix shell. LexEnv will allow you to auto-complete your variable names and so on. There are a few more besides
what I mentioned here.

Next I'll mention the [Colors][colors] plugin. You may have noticed when you enter code such as 
`my $var = 'blah'; say $var;` you get the output 
```
blah
1
```
or the output text and return code. well if you enable colors the return code is now in green making it easy to
distinguish between output and extra info.

Last but not least I'll mention [DumpHistory][dumphist], enabling DumpHistory allows you to Dump your input history to
the screen or to a file.

Now we have a fully functioning, 'user' friendly, perl shell.

For your convenience here's my current file.
```perl
use 5.012;
use warnings;

load_plugin qw(
    Colors
    Completion
    CompletionDriver::INC
    CompletionDriver::LexEnv
    CompletionDriver::Keywords
    CompletionDriver::Methods
    DumpHistory
    History
    LexEnv
    ReadLineHistory
);
```

[develrepl]: https://metacpan.org/pod/Devel::REPL
[readline]: https://metacpan.org/pod/Devel::REPL::Plugin::ReadLineHistory
[colors]: https://metacpan.org/pod/Devel::REPL::Plugin::Colors
[dumphist]: https://metacpan.org/pod/Devel::REPL::Plugin::DumpHistory
