+++
title = "Regen2 0.9.1.0 Released"
date = 2009-04-22T00:45:00Z
updated = 2009-04-22T02:02:42Z
tags = ["regen2", "linux", "archive"]
categories = ["Development"]
blogimport = true 
type = "post"
+++

_Release Highlights_
* vanilla-sources-2.6.28.9
* gcc-4.3.3
* glibc-2.9_20081201-r2
* openrc-0.4.3-r1
* baselayout-2.0.0-r2
* git-1.6.2.3
* perl-5.10.0
* bash-4.0_p17

`app-sh/dash` is now included in the tarball, in the next major release I hope to make it `/bin/sh` please test and
report any bugs with doing this. `apache2` and `mysql` are known to have issues. `openrc` works fine, the average
desktop system should work.

_Known Issues_
`emerge --sync` will not clone the tree if it does not exist in portage 2.1

so for now you'll have to work around that issue. by manually doing the git clone.

_Download_

[torrents are here][mininova] and [here][gc] actual tracker is TPB. http downloads are [here][devangels] thanks to
is_null for the mirror. If you're interested in mirroring them let me know. I'll put up a proper mirror list at some
point.

**Edit** Thanks to theappleman for more hosting. [HTTP (Europe)][appleman] FSP (just for fun ^^, it'd take about 8-9hrs
to download the x86)

*some links removed because I'm sure none of them work anymore, and this page is archived*

[mininova]: http://www.mininova.org/user/xenoterracide
[gc]: http://code.google.com/p/regen2/downloads/list?q=label:0.9.1.0
[devangels]: http://regen2.devangels.org/release/
[appleman]:  http://g.applehq.eu/files/regen2/0.9.1.0/amd64/stage3-amd64-2009.04.18.tar.bz2
