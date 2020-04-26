+++
title = "Perl Core Syntax Wishlist: Role Support"
date = 2012-06-11T06:00:00Z
updated = 2012-06-11T06:00:09Z
tags = [ "perl", "perl syntax wishlist"]
blogimport = true 
type = "post"
aliases = ["/2012/06/perl-core-syntax-wishlist-role-support.html"]
+++

I want to see Role's added, even [PHP got Traits before Perl][phptraits]. It doesn't have to be a huge thing, in fact
all I want is the composition aspect. Let me do this:
```perl
package MyRole {
    sub foo { return 'test' }
}
package MyClass {
    with 'MyRole';
    ...
}

MyClass->new->foo;
```
I don't really want or need anything else right now, just that would be fine. We should have interfaces too, but they
aren't really required just to support Role's. We should probably have some sugar like I did for `class` (e.g. `role`
keyword. and some of the same modules/pragmas loaded for this too).

[phptraits]: http://php.net/manual/en/language.oop5.traits.php
