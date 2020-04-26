+++
title = "How not to ask for help"
date = 2010-03-11T00:22:00Z
updated = 2010-03-11T00:27:05Z
tags = ["sql server", "postgresql", "sql"]
categories = ["Editorial"]
aliases = ["/2010/03/how-not-to-ask-for-help.html"]
blogimport = true 
type = "post"
+++

This is the story of my friends problem today.

> I got one for ya.
> Field like field 

so of course I'm like: "? is that a question? he had given me no prelude, no premise. response

> I tried field like '%' + field2 + '%'
> No good though

So I explained that % is a wildcard. He still hadn't explained what his problem is or what he was trying to do.

> it giving you a syntax error?

So here I am trying to guess at what his problem is. I've never used sql server but a quick glance at postgres docs
suggested the syntax was wrong. I know don't query other docs than the software being used... but I wanted to run a
test locally to see if I could figure out what he was doing. 

> Doesn't work

Oh! that's very helpful... way to give more information. I think I now know why he never asks questions in IRC or
forums. He never learned how to ask a question and they won't put up with him. "so it's not a syntax error it just
doesn't do what you want?"

> Right. The like command isn't finding it.

So finally I'm able to determine... his problem is he's got  `... WHERE ... LIKE ...` that isn't returning what he
expects. This is still completely worthless because I still haven't been able to figure out what he's trying to do...
or more correctly I have but not why it isn't working because his code is right. which means he's fallen prey to the
assumption that he knows what I need to know to solve his problem. He hadn't given me enough info. After I tried to
explain to him how to do what I thought he was trying to do and giving him a sample query and some data... he gives me
this.

> `Field1 = "4369"   field2 = "4311", "4369"`

Now anyone who's familiar with sets will probably realize the notation here is bad and so you can't tell if these are
sample columns or records. a more proper way to write this would have been 
`field1 = { '"4369"'} field2 = {'"4311", "4369"'}` I probably still would have been a little confused just because I
wouldn't have been sure he was writing it right. See the problem here is that field 2 is an array and therefore not
stored properly in a relational manner, it's why he has to do a much slower LIKE in the first place. After bantering
to figure out that this was in fact text data these were single records I eventually figured out that what he was 
rying to do was find field2 if it contaned field1. However, his code was asking for field1 if it contained field2.
basically he had his parameters reversed.

It took me 20 minutes of getting that data to realize they were reversed, which is shameful for me... but I was trying
to figure out things like VARCHAR or INTEGER... he of course said it's not an array, and I suppose it's not an ARRAY
type in sql.. but I guarantee that's why it looks like that... it's really a foreign key stored as an array in a
varchar. After I told him he lied and said it didn't work only to come back 10 minutes later and say... you were right.

so the final should have been `WHERE field2 LIKE '%' + field1 + '%'` or in postgres
`WHERE field2 LIKE '%' || field1 || '%'` because we use the standard `||` for string concatenation instead of the `+`.
