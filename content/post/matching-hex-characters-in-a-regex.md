+++
title = "Matching Hex characters in a Regex"
date = 2014-03-13T15:41:00Z
updated = 2014-03-16T18:43:53Z
categories = ["Development"]
tags = ["perl", "regex", "java"]
blogimport = true 
type = "post"
aliases = ["/2014/03/matching-hex-characters-in-regex.html"]
+++

I've noticed a common problem with regular expressions and Hex Characters, so I thought I'd blog about it. The most
common way to regex a UUID, or SHA1 or some other hex encoded binary value is this (and I've seen this in Perl
libraries and StackOverflow answers).

`[a-f0-9]` or `[A-F0-9]`

Neither of these are correct as Hex is case insensitive and both of these regex's are. Hex is most commonly lowercase
(unless you're Data::UUID), but that's an aesthetic, not a requirement. The best way to match Hex is using a 
[POSIX character class][regex].

`[[:xdigit:]]` or`\x`

Which matches `[A-Fa-f0-9]` in a more readable manner, and intent driven manner

As a side note it's `"\\p{XDigit}"` in a regex string in Java

[regex]: http://en.wikipedia.org/wiki/Regular_expression
