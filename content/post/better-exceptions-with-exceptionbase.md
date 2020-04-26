+++
title = "Better Exceptions with Exception::Base"
date = 2012-05-21T06:00:00Z
updated = 2012-05-21T06:00:05Z
catalog = ["Development"]
tags = ["exceptions",  "perl"]
blogimport = true 
type = "post"
aliases = ["/2012/05/better-exceptions-with-exceptionbase.html"]
+++

So I've done some [complaining][die] and [explaining][exc] about what I'd like to see in regards to Exceptions in Perl.
I Mostly explained what I wanted for catching Exceptions, and a little on throwing Exception objects, but not really
how those objects should behave. I've looked at and tried various exception modules, including [croak, confess][carp],
and [Throwable][throwable].  I basically spent time one day reading the manuals of most of the exception objects on
CPAN. Most of them didn't allow me to easily for the exceptions I needed (meaning they required more work than I
thought I should do for one or two exceptions ). Among these modules I found [Exception::Base][EB] which appears to do
everything I need (though I still wish for something like it in core, with shinier syntax).  Of course wee need to be
able to throw simple Exceptions, preferably ones that can stringify, and are easily matched in a switch or if statement.
[Exception::Base][EB] can do all of that, and it even boolifies to true.

```perl
use 5.014;
use warnings;

use Try::Tiny;
use Exception::Base;

try {
    if ( 0 ) {
        Exception::Base->throw( message => 'this sucks', value => 0 );
    }

    if ( 1 ) {
        Exception::Base->throw( message => 'this really sucks', value => 1 );
    }
}
catch {
    given ( $_ ) {
        when ( $_ == 0 ) {
            say $_;
        }
        when ( $_ == 1 ) {
            say $_;
        }
    }
}
```

A really big thing I wanted was a class I could easily add attributes to without writing a whole new package/pm and
subclassing it there. I wanted this because I really wanted to be able to have 2 kinds of messages, one for
programmers, and one for users, but truthfully I only had one class where I needed this flexibility (at the time). It
is also occasionally useful to have attributes that describe something, e.g. would be really useful in moose attribute
exceptions, to be able to have an object where you could catch the exception and get the attribute name without
parsing. Fortunately [Exception::Base][EB] can do this too.

```perl
use 5.014;
use warnings;

use Try::Tiny;
use Exception::Base (
    'My::Exception' => {
        has => [ qw( usermsg logmsg attr ) ],
        string_attributes => [ qw( usermsg logmsg ) ],
    },
);

try {
        My::Exception->throw(
            value => 1,
            attr    => 'bad',
            logmsg  => 'really obscure and technical',
            usermsg => 'this message is useless'
        );
}
catch {
    warn $_;
    say $_->usermsg;    

    say 'this is really bad' if $_->attr eq 'bad';
}
```

You'll notice if you run this script that in the warn, both `usermsg` and `logmsg` are printed because they are both
`string_attributes`. You'll also notice that `attr` isn't printed at all, but that we can look at it directly to make
other decisions.  [Exception::Base][EB] has other features such as setting the `verbosity` so you can go from a 
`croak` level message to a full stacktrace. It also allows you to `ignore_package`s so that the exception does not 
appear to be thrown from that package. This is similar to `@CARP_NOT`.


[die]: /post/perl-core-syntax-wishlist-die-should-die/
[exc]: /post/perl-core-syntax-wishlist-an-exception-stack/
[carp]: https://perldoc.perl.org/Carp.html
[throwable]: https://metacpan.org/module/Throwable
[EB]: https://metacpan.org/module/Exception::Base
