+++
title = "really nice"
date = 2008-04-02T09:36:00Z
updated = 2010-04-04T15:51:36Z
tags = ["linux", "tips", "shell"]
aliases = ["/2008/04/really-nice.html"]
categories = ["Operations"]
blogimport = true 
type = "post"
+++

if you are using the cfq io scheduler in linux you can use ionice to nice your io. so to really nice something you
would do `nice -n 19 ionice -c 3 programname`. This may not be the best idea on all programs but it works. If a program
is beating your hd and cpu and you don't want it to run this when starting it.
