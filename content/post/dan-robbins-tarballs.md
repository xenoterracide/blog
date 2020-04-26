+++
title = "Dan Robbins Tarballs"
date = 2008-10-21T11:17:00Z
updated = 2008-10-21T11:18:00Z
tags = ["Regen2"]
blogimport = true 
type = "post"
+++

I think having weekly tarballs is a great idea. But I'm less concerned about the release schedule of such things for regen2 and more concerned about the system packages that tarballs contain. A new tarball should be released, 1 week after a new glibc or gcc is updated, for sure, but any system package should be enough. Why 1 week? well once they stabilize they will hit a lot more people who are likely to have bugs, and that gives time for bugs to be fixed before tar-ing it. Why those 2 packages in particular? They are huge and take a long time to compile.<br /><br />I'd like to warn people about using Dan Robbins tarballs. I don't think they are stable. I was building 2 systems recently and one was built off of the last Gentoo i686 tarball the other was built off of Dans latest tarball (I think, I had really lost track after 2-3 weeks). I went to build mysql, it failed to build on the Dan Robbins tarball system, couldn't figure out any rhyme or reason why, other than maybe something on his tarball was weird. So I grabbed a gentoo tarball and started over (note both system's were chroot's at the time and had the exact same configs, just different hardware underneath). Everything went fine the second time I tried. So just a word of warning, his tarball's may have problems. No I haven't reported this to him, I don't have the error anymore either, I unfortunately didn't have a good way to get one to him at the time.<br /><br />EDIT: A more recent post from him suggests that this may have been cause by him using the 2007.1 profile which was never really released.<div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>