+++
title = "Override DNS on a Linux system without root"
date = 2012-12-06T21:00:00Z
updated = 2012-12-06T21:00:02Z
tags = ["linux", "dns",  "networking"]
aliases = ["/2012/12/override-dns-on-linux-system-without.html"]
categories = ["Development"]
blogimport = true 
type = "post"
+++

I had this problem for a long time, and no one ever proposed a good solution. Recently I got a [new answer][usea] on
my, almost 2 year old, [Unix and Linux StackExchange question][useq]. This information seems very obscure and so I
thought I'd share it, if you too have had this problem and were unable to find this, or at least found finding it hard,
consider upvoting the answer.

## Problem

You're using a Linux system that you don't have root on, you need to override the DNS of the system. You usually want
to do this because you're testing a service (web site) that does not have a proper hostname, but needs one in order to
function properly. In the hosting world this comes up often enough.

## Solution

You can set the `HOSTALIASES` environment variable before running your client program. `HOSTALIASES` is an environment
variable that points to a file that is essentially alias value pairs.
```shell
$ echo "foo www.google.com" > ~/my_hosts
$ HOSTALIASES=~/my_hosts wget foo
```
See `hostname(7)`.

[usea]: http://unix.stackexchange.com/a/56288/29
[useq]: http://unix.stackexchange.com/q/6887/29
