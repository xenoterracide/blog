+++
title = "emerge-ng and vcs builds"
date = 2008-10-14T01:22:00Z
updated = 2008-10-14T01:22:59Z
tags = ["Regen2", "emerge-ng"]
blogimport = true 
type = "post"
+++

gentoo is a source based distribution, and this is a good thing. why then has the ability to have trunk builds (which has been added to many overlay's) seem like such a hack? all non-binary program ebuilds in the tree should have a 'trunk' version option. emerge --sync should then be able to find out whether trunk has been updated so you can correctly rebuild programs as needed with emerge --ask --verbose --update --deep --newuse world<div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>
