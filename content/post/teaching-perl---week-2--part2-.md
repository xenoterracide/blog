+++
title = "Teaching Perl - Week 2 ( part2 )"
date = 2010-04-25T18:41:00Z
updated = 2010-04-25T19:01:32Z
tags = ["question",  "perl", "teaching perl", "curriculum"]
blogimport = true 
type = "post"
aliases = ["/2010/04/teaching-perl-week-2-part2.html"]
+++

This part2 was prompted by [Chromatic's post on state][chromatic]. I'd never heard of `state` before, and it's
[documentation][state] is poor. Let's take a look at how `state` could affect our game.
```perl
#!/usr/bin/env perl
# guess a number game
use strict;
use warnings;
use feature qw( say state switch );

say 'welcome';

# generate the winning number between 1 and 10
# see perlfaq4 for algorithm details
my $winning_num = 1 + int( rand( (10-1)+1 ) );

until ( $winning_num == state $guess ) {
    say 'Guess a number between 1 and 10: ';
    $guess = readline(*STDIN);

    # check to see if we have a winner, or the guess is to high, or low.
    given( $guess ) {
        when ( $_ > $winning_num ) {
            say 'Too high';
        }
        when ( $_ < $winning_num ) {
            say 'Too low';
        }
        when ( $winning_num ) {
            say 'You Win!';
        }
    }
}
say 'Game over!';
```
As you can see this allows us to make our $guess variable lexically scoped, meaning it's contained within the loop.
This however generates some warnings: `Use of uninitialized value $guess in numeric eq (==) at ./game.pl line 14.` and
if you enter non-numeric input `Argument "l\n" isn't numeric in numeric eq (==) at ./game.pl line 14, <stdin> line 1.`.
Both are completely fixable, but lets fix the uninitialized first.
```perl
#!/usr/bin/env perl
# guess a number game
use strict;
use warnings;
use feature qw( say state switch );

say 'welcome';

# generate the winning number between 1 and 10
# see perlfaq4 for algorithm details
my $winning_num = 1 + int( rand( (10-1)+1 ) );

until ( $winning_num
        == ( state $guess = 0 )
        ) {
    say 'Guess a number between 1 and 10: ';
    $guess = readline(*STDIN);

    # check to see if we have a winner, or the guess is to high, or low.
    given( $guess ) {
        when ( $_ > $winning_num ) {
            say 'Too high';
        }
        when ( $_ < $winning_num ) {
            say 'Too low';
        }
        when ( $winning_num ) {
            say 'You Win!';
        }
    }
}
say 'Game over!';
```
So all we've had to change is adding parenthesis around `state` and assign it to a value. If we had used a `my`
declaration it would reassign to 0 every time and we'd never match the `$winning_num`. But since `state` will only ever
initialize once we don't have that problem. We still have our problem if we enter a character though, let's sanitize
that input.
```perl
#!/usr/bin/env perl
# guess a number game
use strict;
use warnings;
use feature qw( say state switch );

say 'welcome';

# generate the winning number between 1 and 10
# see perlfaq4 for algorithm details
my $winning_num = 1 + int( rand( (10-1)+1 ) );

until ( $winning_num
        == ( state $guess = 0 )
        ) {
    say 'Guess a number between 1 and 10: ';
    $guess = readline(*STDIN);

    # check to see if we have a winner, or the guess is to high, or low.
    given( $guess ) {
        # sanitize input
        # this must be first otherwise it won't work right
        when ( $_ !~ /[[:digit:]]/ ) {
            say 'not a number';
            # set $guess to 0 so we don't have a numerical comparison warning
            $guess = 0;
        }
        when ( $_ > $winning_num ) {
            say 'Too high';
        }
        when ( $_ < $winning_num ) {
            say 'Too low';
        }
        when ( $winning_num ) {
            say 'You Win!';
        }
    }
}
say 'Game over!';
```

So in order to fix the 'not a numerical comparison' issue we check `$guess` to see if it's a digit with a regular
expression. If it isn't a digit we set it to 0 which is an invalid guess anyways, but fixes the warning. This actually
fixes two bugs. Before if you entered a letter it would tell you that it was too low (which I don't actually
understand, because aren't characters > numbers? if someone on Iron Man could explain it in a comment.)

[chromatic]: http://www.modernperlbooks.com/mt/2010/04/state-and-the-syntax-of-encapsulation.html
[state]: http://perldoc.perl.org/functions/state.html
