+++
title = "minimum permissions and privilegdges"
date = 2008-10-29T00:53:00Z
updated = 2008-10-29T00:53:51Z
tags = ["Regen2", "emerge-ng", "pms"]
blogimport = true 
type = "post"
+++

I like security... which means I should be able to run portage as portage (user) and have the umask be 077, or perhaps 027. Unfortunately the last time I checked portage could handle these restrictive permissions (I forget if it was these exactly) except for one set... java. In a perfect ebuild world all ebuilds would be able to be installed under a very restrictive umask.<br /><br />Should regen2 ever come to fruition this should be fixed.<div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>
