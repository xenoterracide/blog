+++
title = "What Open Source Operating System's still need"
date = 2008-03-07T15:25:00Z
updated = 2008-03-07T16:39:29Z
tags = ["Linux"]
blogimport = true 
type = "post"
+++

I'm not going to mention things that are beyond the control of open source such as drivers and multimedia. These things are the fault of the corporations that control them.<br /><br />The first thing I think these systems need is a unified password manager. Instead both gnome and kde have there own password storage system and so do a million other apps. I think OpenBSD should lead the pack on this one given there reputation for security. This would be for the storage of passwords use to access remote systems, on either the command line or gui. Of course the application designers would still probably want to build there own front end but hopefully we could be assured that the information was secure.<br /><br />The second thing I've been thinking of is a FHS standard for home directory dot files. God I've come to hate dot files in recent months. I recently wanted to put my $HOME directory on version control. Including the dot files. Then I realized that many of them were really only temporary files that were session specific, or they contained binary data etc, etc, basically things I can't shouldn't or don't want to version. I can't possibly keep track of all the dot files I'd like them to be organized in sub dot files now. with config files in .etc, var like data in  .var, and temporary data in .tmp etc.<br /><br />EDIT: a bug has already been filed on the FHS standard and can be found here <a href="http://bugs.linux-foundation.org/show_bug.cgi?id=75">dot etc, the users private etc directory proposal</a><div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>