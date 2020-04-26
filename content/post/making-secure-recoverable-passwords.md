+++
title = "Making secure recoverable passwords"
date = 2008-04-27T14:08:00Z
updated = 2009-08-14T16:39:19Z
categories = ["Security"]
tags = ["passwords"]
blogimport = true 
type = "post"
aliases = ["/2008/04/making-secure-recoverable-passwords.html"]
+++

**Update:** I would suggest using a password manager like [LastPass][lp], or a long passphrase before this method
now.

For the basic you need a calculator with a decimal to hex function. Your OS should have one built in (scientific mode),
many calculator's do to. For advanced you'll need special hashing software, suggestions for it can be found below.

* Windows: [HashCalc][hc]
* Macintosh [MacHash][mc]
* Linux/(*nix) [Gnu Coreutils][coreutils]

Now that we have the software we need. You'll need an 8 digit (minimum) number that you won't forget. Guess what 
numbers fit the bill perfectly. Dates. All date's can be expressed in 8 digits, but they require a year. The date we'll
use (for example) is Linux's Birthday. August 25, 1991 (I recommend something more personal like your anniversary or
birthday).<br /><br />So we are going to write down the date we picked for the password (note we aren't done yet).

something like `Linux BDay` 

Date's can be [formatted][date] in several ways, Just make sure to remember the numeric format you use. For our example
we'll use the [ISO 8601][8601] standard. Which leaves our example date in format, YYYYMMDD, looking like 19910825.

So let's make a note of the format (to throw people off use a diff format say DDMMYYYY).

`Linux Bday ISO`

whatever you do make sure *you* understand your note, but that it isn't easily understandable by *others*.

alright... now we're going to take our number and make it hex. So open your calculator enter 19910825, then use the
convert to hex function and you should have this 12FD0A9 (if you aren't using our example you may have a different
'number', or it may not be displayed exactly like this, display's vary between calculators). This is your first basic
password. you can use it as is, or you can make it a bit more secure by using one of the [hexadecimal notations][hex].
example: 0x12FD0A9. You can add a word or funny characters to either side of it to make it stronger too, just make sure
to note anything you add to it.

Due to a [Rainbow Table][rainbow] database you should make sure your password is at least 8 characters but I would
recommend no less than 9.

**But your hex number is less than 9 and what do the programs you listed have to do with this?** good point... let's
make something a bit more crypto and less predictable.

Take the same date and plug it in to your hash creator, I'm going to use an md5sum for my first example.

if you put 19910825 into md5sum you get `6f9822851dfc6c1045c6fef827e5d729` (for you nix people enter the number like this
`echo -n "19910825" | md5sum` on the cli otherwise you might end up with newline issues because different operating 
systems use different newlines).<br /><br />so lets say you need an 12 character password you could just use the
first 12 characters of your hash, example: 6f9822851dfc if you need an 8, example: 6f982285 ,etc etc.

if a stupid 'cracker' or black hat hacker get his hands on your actual password he might think he hasn't decrypted it
fully because it'll look like a hash or memory error. want to throw people off even more just remember you aren't using
the first 8 but the first 8 after the first 2 example 9822851d.

Or use a different has like sha1 or sha512.

Ultimately you could make your note to yourself like this `Sha1LinuxBDayISO` to a non IT person this will look like a
random password as is.

Also the cryptographic hashes take words too. So you could do your name. Caleb into an md5sum is 
`9ced73b8525de1db8e232ad575baa9dd` but it's different if I use caleb.

`2f0154d7db348840676529dd72f1c034` if you always enter them the same you will always get the same result back. I
guarantee after a couple days typing your new password you won't need to have it written down. If you work at a place
that requires things like monthly changes.. just put the date you changed it on in to create the hash. Just make sure
no one knows but the number/word(s) you used and the hash you used or they will be able to duplicate your password.
Of course you're smart and you added somthing to it right? like a symbol (!@#$%^*{}|:"<>?[]\;',./) will all work find
if the auth methods allow it.

have fun never having a hard time creating passwords again.

[lp]: https://lastpass.com
[hc]: http://www.slavasoft.com/hashcalc/index.htm
[mc]: http://sourceforge.net/projects/machash
[coreutils]: http://www.gnu.org/software/coreutils/
[date]: http://en.wikipedia.org/wiki/Calendar_date
[8601]: http://en.wikipedia.org/wiki/ISO_8601
[hex]: http://en.wikipedia.org/wiki/Hexadecimal
[rainbow]: http://en.wikipedia.org/wiki/Rainbow_table
