+++
title = "~arch will be testing again not stable"
date = 2009-03-24T05:29:00Z
updated = 2009-03-24T06:26:37Z
tags = ["Regen2"]
blogimport = true 
type = "post"
+++

I think the idea of a meta distro on top of a stable base is the way to go, let people choose just what they want on bleeding edge. I like my kernel, gcc, and perl solid, sometimes I want firefox's beta's but that doesn't mean I want the rest of the system like that. At some point gentoo's ~arch became more stable that stable, this needs to end. Even if it means making stable a bit more unstable. I'd like to see more people running stable systems.<br /><br />~arch is going to be aimed more at things that are stable enough, and pending stable. It's going to become more unstable than it is in a sense because I'm not afraid to hardmask stuff that was ~arch and is found to be broken. I'm also not afraid to release slightly broken stuff into ~arch, for example I'll release a new git into ~arch without an update noperl patch which means using -perl won't work. I won't release completely broken or overtly buggy things into ~arch though, I'm just not afraid to release with a missing patch (set) as long as it'll build and work in most cases and update later.<br /><br />I've decided on a policy for stabilizing the kernel, first of regen2's kernel is vanilla-sources, if you're running another sources this doesn't apply. I will stabilize the previous kernel when the new major version is released so I will stabilize 2.6.28.x when 2.6.29 comes out, after that I will stabilize each new kernel a few days after it hits the tree (actually I may not wait), since the kernel's development model makes it highly unlikely that regressions will be introduced that late in the cycle. I may use a similar update scheme for other packages but it will be a package by package basis. But likely once a major version moves to stable it will be trivial to move new minor versions to stable.<br /><br />I want to know what people use? what should be stable in regen2. 2 things I won't stabilize right now are qt-4.5 and kde-4.2. They work well enough but have too many bugs upstream, imho (should a kde developer look at this feel free to look at my buglist on your bug tracker, prtscrn must work before I'll mark kde stable, but hey at least dpms works with screensaver now). Anything else goes what do you use/need that's in ~arch and has been there for a long time. feel free to mention in comments, the <a href="http://groups.google.com/group/regen2-dev">dev list</a> or the <a href="http://code.google.com/p/regen2/issues/list">Bug tracker</a"><div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>