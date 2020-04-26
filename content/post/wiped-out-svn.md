+++
title = "wiped out svn"
date = 2008-03-31T04:26:00Z
updated = 2008-04-03T15:15:25Z
blogimport = true 
type = "post"
tags = [ "git", "svn" ]
aliases = ["/2008/03/wiped-out-svn.html"]
+++

So I'm sitting at my computer running out of space on `/var` and I think ok I need to move my svn repositories to 
`/srv`  which is on ext3 and part of `/.` so I go to move them there... it says file exits. I `ls /srv/svn` sure
enough there they are. I've already moved them great `rm -r /var/svn` (gentoo doesn't enable `-i` by default) it
deletes everything. I shortly realize that `/srv/svn` was a symlink to `/var/svn`. all my history gone. good thing I
have a wonderful backup system (even though I didn't backup the repos just the files). Now I'm thinking about using
[`git`][git]

**Edit:** [Git for SVN users][gitsvn]

[git]: http://git.or.cz/
[gitsvn]: http://git.or.cz/course/svn.html
