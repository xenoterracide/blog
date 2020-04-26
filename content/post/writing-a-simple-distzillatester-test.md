+++
title = "Writing a simple Dist::Zilla::Tester test"
date = 2010-11-10T08:59:00Z
updated = 2010-11-23T19:21:17Z
tags = ["perl", "Dist::Zilla"]
categories = ["Development"]
blogimport = true 
type = "post"
aliases = ["/2010/11/writing-simple-distzillatester-test.html"]
+++

Hopefully, someone will use the blog post to write an actual doc patch, seeing as how this is undocumented.

I finally wrote [A test for DZP::OurPkgVersion][test] with the help of [CJM][cjm]. So I figure it's best to share the
knowledge imparted upon me to all those who are writing plugins without tests.

Before we get started I'm going to advise that this test will only check the output that dzil built, if you need it to
test anything more sophisticated, you'll have to learn more.

First you'll want to create a corpus repo like `/corpus/MyDZTRepo` with a basic minimal repo. This repo is simply a
repo that you are using to test your plugin against, to make sure it works right. You put it in corpus so that if you
have tests that you have to check in your corpus, those tests themselves aren't run when the test suite is run. The 
`dist.ini` doesn't need to contain anymore than the basic stuff needed to build. You `.pm` files need not have anymore
data than what you're going to need to make your dzil plugin do its job. In the case of
[DZP::OurPkgVersion][OurPkgVersion] I only needed to test that the output found the `# VERSION` string correctly in a
couple of scenario's. So that meant having `# VERSION` in the `.pm`'s and `[OurPkgVersion]` in the `dist.ini`.

```ini
name    = DZT0
author  = Caleb Cushing <xenoterracide@gmail.com>
license =  Artistic_2_0
version = 0.1.0
copyright_holder = Caleb Cushing

[@Filter]
-bundle = @Basic
-remove = Readme

[OurPkgVersion]
```
```perl
use strict;
use warnings;
package DZT0;
# VERSION
# ABSTRACT: my abstract
1;
```
```perl
#!/usr/bin/perl
use strict;
use warnings;
use Test::More;
use Dist::Zilla::Tester;

# specify where the dist lives
my $tzil = Dist::Zilla::Tester->from_config({ dist_root => 'corpus/DZT' });

# function to essentially run dzil build
$tzil->build;
...
```

First DZT (Dist::Zilla::Tester) doesn't provide any tests of its own so you still need to use `Test::More` or some
other testing framework. Next you need to initialize the tester object by telling it where the root of your corpus
repo is.  After that, unless you need to do other work, you can run `$tzil->build` so that the build is run.

So now lets slurp a file into memory so we can check to see if it was built right. You'll want to look in `'build/*'`
as the basic root of the build directory. So `'build/t/test1.t'` if you need to slurp a test.

```perl
...
my $lib_0 = $tzil->slurp_file('build/lib/DZT0.pm');
...
```

Now that we've pulled our build files into memory lets code up what the result should be. We can just do this with a
simple heredoc, obviously you can do it another way.
```perl
...
my $elib_0 = <<'END LIB0';
use strict;
use warnings;
package DZT0;
our $VERSION = 0.1.0;# VERSION
# ABSTRACT: my abstract
1;
END LIB0
...
```

Now that we've gotten that, all we have to do is compare the file that we expect dzil to output and the file that dzil
actually built. This is just standard [`Test::More`][tm]
```perl
...
is ( $lib_0, $elib_0, 'check DZT0.pm' );
done_testing;
```
Now let's take a look at it all together.
```perl
#!/usr/bin/perl
use strict;
use warnings;
use Test::More;
use Dist::Zilla::Tester;

my $tzil = Dist::Zilla::Tester->from_config({ dist_root => 'corpus/DZT' });

$tzil->build;

my $lib_0 = $tzil->slurp_file('build/lib/DZT0.pm');

# e short for expected files
my $elib_0 = <<'END LIB0';
use strict;
use warnings;
package DZT0;
our $VERSION = 0.1.0;# VERSION
# ABSTRACT: my abstract
1;
END LIB0

is ( $lib_0, $elib_0, 'check DZT0.pm' );

done_testing;
```
Pretty simple huh? Hope this means more `dzil` modules getting tested now. Including more of mine.

[test]: https://github.com/xenoterracide/Dist-Zilla-Plugin-OurPkgVersion/blob/4d1bc0270a3abb6dce68ae70b0d31290bb7d97ad/t/01-basic.t
[cjm]: https://metacpan.org/author/CJM
[OurPkgVersion]: https://metacpan.org/pod/Dist::Zilla::Plugin::OurPkgVersion
[tm]: https://perldoc.perl.org/Test/More.html
