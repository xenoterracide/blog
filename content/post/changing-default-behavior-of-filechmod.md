+++
title = "Changing default behavior of File::chmod"
date = 2013-07-06T06:03:00Z
updated = 2013-07-06T06:07:42Z
tags = ["perl"]
categories = ["Development"]
blogimport = true 
type = "post"
aliases = ["/2013/07/changing-default-behavior-of-filechmod.html"]
+++

[File::chmod][fc] has been around for a long time, and is really stable, and really hasn't changed since 1999. It is far
more user friendly than the`chmod()` in core Perl. I recently used it for an interview test. It took
me a few times to get right however because it's default behavior in `symchmod()` mode is to use the systems `umask`. I
find this to be very confusing behavior. I actually thought it was a bug at first, and asked for comaint since it
hadn't been updated in so long. Now that I realize it's intentional I'm unsure how to best proceed. On one hand I
believe that the most obvious behavior (mimicking unix chmod) should be the default, on the other changing something
that has been around this long... So I'm writing this blog post. What do you think? should I preserve the behavior?
if not I'm aiming for a long deprecation cycle. Unfortunately because it used package variables for this setting,
I haven't come up with a way to deprecate code wise that won't be annoying.

Regardless of what I do with this, there'll be a new release that has proper metadata, tests rewritten with Test::More,
etc.

[fc]: https://metacpan.org/module/File::chmod
