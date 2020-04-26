+++
title = "iptables browsing samba shares"
date = 2009-05-16T00:37:00Z
updated = 2009-05-16T00:53:11Z
tags = ["tutorial", "howto", "Linux", "security", "iptables", "samba"]
blogimport = true 
type = "post"
+++

NOTE: this assumes that you've read my previous post on <a href="http://xenoterracide.blogspot.com/2008/04/iptables-for-average-desktop-user.html">basic iptables setup on the desktop</a><br /><br />So I just spent the longest time trying to determine what ports I needed to browse and use samba shares. The sad answer is it's just one.<br /><br /><code><br />iptables -A INPUT -p udp --sport 137 -j ACCEPT<br /></code><br /><br />the catch with all the information I found with google was that most of it was for samba servers. I didn't want that. I just want to be able to browse my roommates network shares, and download from them. None of the information google provided suggested I need to allow the source port on the other end.<div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>
