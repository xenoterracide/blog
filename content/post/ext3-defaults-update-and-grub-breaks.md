+++
title = "ext3 defaults update and grub breaks"
date = 2008-05-01T17:20:00Z
updated = 2008-05-01T17:27:00Z
blogimport = true 
type = "post"
+++

<a href="http://www.linuxplanet.com/linuxplanet/tutorials/6480/1/">GRUB vs. the Inodes: Who Needs a Bootable System, Anyway?</a><br /><br />So apparently ext3 decided to bump the default inode size to 256 however grub 0.97 can't boot this and grub people aren't about to fix it because they are working on grub 2. Yeah that sounds about right... don't maintain your existing code base even though your next product isn't out yet.<br /><br />Amarok is doing this too, it's quite annoying, and very poor develompment practice. Features are one thing but maintenance is another. You know what's bad? Grub 2 was in development 2 years ago if I remember right. Maybe I should add grub to <a href="http://maintainance-required.blogspot.com">Maintenance Required</a> Since it's current maintainers don't want to fix system breaking bugs.<br /><br />And shame on the extents devs for changing it in the first place....<div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>
