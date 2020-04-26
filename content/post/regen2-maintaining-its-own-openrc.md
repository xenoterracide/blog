+++
title = "regen2 maintaining it's own openrc"
date = 2009-02-22T20:50:00Z
updated = 2009-02-22T20:53:49Z
tags = ["Regen2", "tree"]
blogimport = true 
type = "post"
+++

So openrc was updated recently, and I noticed it, I decided it was time to find out why drobbins had created his own version. so I viewed them, ultimately I decided that drobbins had made too many changes but the gentoo ebuild had its own issues. So I modified the Gentoo ebuild to use EAPI 2, removed all of the live ebuild code that shouldn't be in it, updated the branding, and we're ready to rock.<div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>
