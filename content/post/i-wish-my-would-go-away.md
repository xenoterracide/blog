+++
title = "I wish my would go away"
date = 2010-07-10T21:53:00Z
updated = 2010-07-11T16:23:28Z
tags = [ "perl", "wishlist", "language design", "unpopular opinion"]
blogimport = true 
type = "post"
aliases = ["/2010/07/i-wish-my-would-go-away.html"]
+++

I been thinking about it for a few days, and of course consider I know nothing about grammar parsing or how any of
this works. I wish the `my $var;` syntax would go away... or at least be less necessary. In almost all
cases you want a lexical variable and `use strict;` doesn't allow you to use `$var` anyways. So what'd I'd like to
 see is `my` become a mostly unnecessary reserved word, make `$var` a lexical variable by default, e.g.
 `my $var;` be the same as `$var`. `my` would only be needed for redeclaration's and back compat. It could be enabled
 with a pragma or something like `use feature 'lexical';`. There might be some reason I'm completely wrong or stupid,
 but this doesn't seem like a bad idea, and of course `our, state, local` would still need to exist.
 
 **Update:** the point  of this would be to make variables lexically scoped by default as opposed to there current
 global default.
