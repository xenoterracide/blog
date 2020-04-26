+++
tags = ["blogger", "blogging",  "hugo"]
categories = ["Editorial"]
date = "2017-03-17T08:41:41-05:00"
title = "Goodbye blogger, migrating this weekend"
type = "post"
aliases = ["/2017/03/goodbye-blogger-migrating-this-weekend.html"]
+++

Well it's the end of a decade of blogging with blogger for me. Though you've probably noticed I haven't really been
blogging for the last 2 years. I hope that's a combination of these 2 things, but no promises.

1. writing a tech blog with blogger is incredibly painful
1. when I switched from Perl to Java I wasn't an expert, and had little to contribute

So I'm sure you're wondering what I've switched to? Well I tried Jekyll, and Hexo, and neither of their importers
just worked. When trying to process what their importers emitted they crashed, failed to render anything or give me any
indication of what was causing them to fail. The 3rd thing I tried was [Hugo][hugo], it's Blogger importer is far from
perfect, and I did need to do some post import sed expressions to get things pretty. When it failed, with one
exception, it always gave me the line and column number, and continued processing other templates. This allowed me to
fix those few posts without outside support. After realizing github pages couldn't do custom domains with https, I went
with Amazon S3 + CloudFront (which was excruciatingly painful).

So I've yet to convert every single post, of the 200 posts to HTML-less Markdown, and add all the aliases. I'm sure I
could have written my own importer, or done some post import scripting to fix them, but meh. This gives me an excuse to
clean them up in ways that coding the solution wouldn't be able to. It would be nice if Hugo supported bulk aliasing,
[but it's not something they're willing to do][gh]. For the time being I've
converted the front page posts, and the most popular posts.

This weekend I'll be throwing the switch, which will likely mean you'll get posts you've seen in your feed readers,
and that I have to migrate comments to the new structure. If you're using the [feedburner feed][feedburner] you should
be fine, if you're following via the blogger feed that will no longer be used after this post.

If you find any issues with the new site, feel free to [let me know][bbi] or send me a [pull request][bbp]. If
necessary, you can visit [the old site][old], I will probably not take it down for a while.

[hugo]: https://gohugo.io/
[bbi]: https://bitbucket.org/xenoterracide/blog/issues?status=new&status=open
[bbp]: https://bitbucket.org/xenoterracide/blog/overview
[old]: https://xenoterracide.blogspot.com
[gh]: https://github.com/spf13/hugo/issues/3167
[feedburner]: https://feeds.feedburner.com/xenoterracide
