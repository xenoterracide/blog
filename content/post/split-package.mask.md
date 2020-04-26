+++
title = "split package.mask"
date = 2009-02-21T23:30:00Z
updated = 2009-02-21T23:39:34Z
tags = ["Regen2", "tree", "Portage"]
blogimport = true 
type = "post"
+++

There's been a problem with ${PORTDIR}/profiles/package.mask since I added sunrise, I didn't realize at the time the problem wouldn't be adding atoms, it would be keeping track of when they should be removed. I've decided that the best way to do it is to use portages 'directory concatenation' feature, I turned package.mask into a directory, it now contains a file for each overlay that has a package.mask, and one for gentoo.org and regen2.org. This will make it much easier to keep track of removals. They originals are being merged in from a package.mask 'overlay' on regen2's github account. Let me know if anything is now masked that shouldn't be or unmasked that shouldn't be.<div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>
