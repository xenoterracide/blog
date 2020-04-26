+++
title = "Adding a new group to an open shell without logging in and out"
date = 2009-08-23T01:00:00Z
updated = 2009-08-23T01:00:02Z
tags = ["tutorial", "Linux", "security", "tips", "unix", "shell", "commands"]
blogimport = true 
type = "post"
+++

Most Unix users believe it's impossible to add a group that was recently added to your user account without logging in and logging out. If you're running X and need to give your gui new permissions (like dolphin/konqueoror) this may be true, for the most part. I don't know how to change it for those processes. However, if you need to change it for a shell prompt it's easy. You merely execute the command <code>newgrp <newgroupname></code> in an open shell prompt and that prompt will now be loaded with the new group. I believe it also changes the default group that shell is running as. So if you run <code>newgrp http; touch test.txt</code> your file will be created with the group http instead of whatever your default is. You can change your group back to the default by using the <code>sg</code> command.<div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>
