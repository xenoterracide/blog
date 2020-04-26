+++
title = "More than 3 levels of stable"
date = 2008-10-24T11:17:00Z
updated = 2008-10-24T11:18:00Z
tags = ["Regen2", "pms"]
blogimport = true 
type = "post"
+++

Currently Gentoo has arch, ~arch, and ~M ( - if not available ).<br /><br />One of the main things people want are security patches only updates. I tend to agree with this, although they should be between arch and ~arch, maybe +arch. Technically a security patch can break things which is why they shouldn't be considered stable.<br /><br />Regen2 should also have strict rules about what's what. Alpha products should always be ~M, beta's and rc's should be ~arch. The wouldn't be listed as those upstream if they were stable, and if upstream says they are stable then tell upstream to move them to stable.<br /><br />We should add an unmaintained level akin to ~M, perhaps ~U, this is so just because the current maintainer doesn't want it in the tree, it doesn't go completely away (in the event someone wants to pick it up and fix it).<br /><br />we should have some way of setting up the vcs packages by arch.<div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>
