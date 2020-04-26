+++
title = "SYNC_METHOD"
date = 2009-03-07T20:18:00Z
updated = 2009-03-07T20:26:54Z
tags = ["Regen2", "Portage"]
blogimport = true 
type = "post"
+++

I'm going to be pushing out an new feature to portage today, since testing has gone well.<br /><br />SYNC_METHOD is a new portage variable, I've also gone to the trouble of defining it in make.globals. The default for Regen2. is git, however, rsync and cvs have also been defined although not tested.<br /><br />the main benefit of SYNC_METHOD is to use one way of detecting the program you should use to detect the tree. previous variations were problematic, as rsync does not have a directory like .git and .git supports the rsync:// protocol.<br /><br />I've added these defaults to make.globals<br /><br /># Default sync method<br />SYNC_METHOD="git"    <br /><br /># Default sync mirror<br />SYNC="git://github.com/regen2/portage.git"<br /><br /># default git method specific settings<br />PORTAGE_GIT_METHOD="checkout"         <br />PORTAGE_GIT_REMOTE="origin/regen2.org"<br /><br />you may of course override them with make.conf.<br /><br />changes will officially be in portage-2.2_rc23-r3.<div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>
