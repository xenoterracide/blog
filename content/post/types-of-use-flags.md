+++
title = "types of use flags"
date = 2008-12-19T15:03:00Z
updated = 2008-12-19T15:14:38Z
tags = ["Regen2", "emerge-ng", "Portage"]
blogimport = true 
type = "post"
+++

Ok so I have yet another idea for portage and other portage like package managers. actually this is probably more aimed at EAPI. Different types of USE flags. The 2 I'm thinking of off the top of my head could be described as volatile and non-volatile. Basically a use flag marked volatile means a package needs to be recompiled if it gets changed one marked non-volatile most likely just pulls in another dependency or copies files such as documentation. I'm sure this feature could have lots of uses I'm not thinking of.<div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>
