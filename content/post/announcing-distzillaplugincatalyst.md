+++
title = "Announcing Dist::Zilla::Plugin::Catalyst"
date = 2010-06-27T21:37:00Z
updated = 2010-06-27T21:37:42Z
categories = ["Development"]
tags = ["cpan",  "perl", "Dist::Zilla", "catalyst", "code" ]
aliases = ["/2010/06/announcing-distzillaplugincatalyst.html"]
blogimport = true 
type = "post"
+++

So I just recently finished reading [Restful Web Services][rws] and decided I wanted to go back and play with Catalyst
and REST some.

The original way to create a Catalyst skeleton is to run `catalyst.pl`. This creates a lot of nice files to get you
started. `dzil new` basically does the same thing for a generic cpan module. Honestly, without any plugins `dzil new`
isn't that useful. However, once you add [Git::Init][dzpgit] , you remove several steps from the creation of a new
module and repository. Git::Init also makes your first commit of everything it added. I got to thinking why on earth
would I want to do the following to get a cat module going and convert it to dzil. 

```shell
catalyst.pl  MyApp && cd MyApp 

vi dist.ini # and add numerous lines
rm Makefile.PL README t/* # and maybe more since dzil is better at managing these
git init
git add .
git commit
```

when I could just be doing this `dzil new -p catalyst MyApp`

A lot simpler huh? to get you started you need a few things but then creating cat apps will be easy.

first you can run [`dzil setup`][setup] or if your version of dzil doesn't support that yet, you need to create the
following `~/.dzil/config.ini` file by hand
 
 ```ini
[%User]
name = Caleb Cushing
email = xenoterracide@gmail.com

[%Rights]
license_class = Artistic_2_0
copyright_holder = Caleb Cushing

[%PAUSE]
username = XENO
password = it'sagoodone
```

obviously fill in your own credentials and license preferences. This file is pretty much needed for any `dzil new`
operations.

Next you need to create a minting profile (not necessary for barebones doesn't do much for you). run 
`mkdir -p ~/.dzil/profiles/catalyst/` (note: catalyst is arbitrary, it can be anything). now create a `profile.ini` in
that directory.
 
```ini
[DistINI]
[Catalyst::New / :DefaultModuleMaker]
[Git::Init]
```
 
The only mandatory line in this file for this to work is the `[Catalyst ...]` one, I think you'll want the other two
however. You also want to set `AUTHOR="your name youremail@example.com` since that's how
[Catalyst::Helper][ch] inserts it into its files (I'll probably work on fixing helper later).
Now you can just run `dzil new -p catalyst MyApp`. Hopefully, this simplifies your catalyst app creation a little bit.
Good patches are welcome, so are feature suggestions and bug reports. Also I registered it as 
[Dist::Zilla::Plugin::Catalyst][dzpc] So if there are any other plugins that you think could be useful that are
specific to Catalyst I'd be willing to add them.
 
Special thanks to Tomas Doran who helped me (ok... he wrote most of it) create this module.

[rws]: http://shop.oreilly.com/product/9780596529260.do
[dzpgit]: https://metacpan.org/pod/Dist::Zilla::Plugin::Git::Init
[setup]: http://rjbs.manxome.org/rubric/entry/1848
[ch]: https://metacpan.org/pod/Catalyst::Helper
[dzpc]: https://metacpan.org/pod/Dist::Zilla::Plugin::Catalyst
