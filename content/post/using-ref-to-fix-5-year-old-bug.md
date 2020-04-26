+++
title = "Using ref to fix 5 year old bug"
date = 2010-06-04T12:00:00Z
updated = 2010-06-04T12:00:05Z
categories = ["Perl"]
tags = [ "perl", "Template Toolkit"]
aliases = ["/2010/06/using-ref-to-fix-5-year-old-bug.html"]
blogimport = true 
type = "post"
+++

So I haven't been hacking perl for 5 (or more) years but I forked [Template::ShowStartStop][tsss] from
[Template::Timer][tt] which is that old. since I forked it
[this test][test] has bugged me since I didn't really understand the test, the section of code it referred to or the
actual problem.

This is an approximation of the error you'd get.

> Couldn't render template "undef error - Can't call method "name" on unblessed reference
at /usr/lib/perl5/site_perl/5.8.0/Template/Timer.pm line 66.

This error is actually a copy from [bug #15457][rt1] but I happen to know it's basically the same error you'd get with
an eval.

So what's the problem? can you spot it? ( original timer code )
```perl
my $template =
    ref($what) eq 'ARRAY'
        ? join( ' + ', @{$what} )
        : ref($what)
            ? $what->name
            : $what;
```
Ugh! That's ugly, let's take a cue from [Perl Best Practices][pbp] and format it as a tabular ternary instead.
```perl
my $template
    # conditional           # set $template to
    = ref($what) eq 'ARRAY' ? join( ' + ', @{$what} )
    : ref($what)            ? $what->name
    :                         $what
    ;
```
Now we can read it... 

[Bug #13225][rt2] suggests a problem with eval and the following fix (which I translated into something more similar to
the current code).
```perl
my $template
    # conditional           # set $template to
    = ref($what) eq 'ARRAY'  ? join( ' + ', @{$what} )
    : ref($what) eq 'SCALAR' ? '(evaluated block)'
    : ref($what)             ? $what->name
    :                          $what
    ;
```
well that should fix our eval problem... since an eval will be  a reference of type `SCALAR`. but what happens if it's
a reference not of type `ARRAY` or `SCALAR`? then we'll still get that error.

According to the [ref function documentation][ref]

> If the referenced object has been blessed into a package, then that package name is returned instead. You can think
of ref as a typeof operator.

what this means is I could check what type of object it was that I was normally getting. In order to do this I put
`$what` into the output I get. I found out that most of the time `$what` is a `Template::Document`. So now I optimized
my code for that situation.
```perl
my $template
    # conditional                        # set $template to
    = ref($what) eq 'Template::Document' ? $what->name
    : ref($what) eq 'ARRAY'              ? join( ' + ', @{$what} )
    : ref($what) eq 'SCALAR'             ? '(evaluated block)'
    :                                      $what
    ;
```
See the thing is that we really don't want `$what->name` method to be called unless it's a `Template::Document` which
actually has that method. I'm not sure that I'm not missing any tests at this point, but I'm pretty confident that, at
least, this kind of bug won't crash my module anymore.



[tsss]: https://metacpan.org/pod/Template::ShowStartStop
[tt]: https://metacpan.org/pod/Template::Timer
[test]: https://github.com/petdance/template-timer/blob/master/t/eval.t
[rt1]: https://rt.cpan.org/Public/Bug/Display.html?id=15457
[rt2]: https://rt.cpan.org/Public/Bug/Display.html?id=13225
[ref]: https://perldoc.perl.org/functions/ref.html
[pbp]: http://www.amazon.com/Perl-Best-Practices-Damian-Conway/dp/0596001738?ie=UTF8&tag=xenotsblog-20&link_code=btl&camp=213689&creative=392969
