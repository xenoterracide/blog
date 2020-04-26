+++
title = "OpenWRT static DHCP"
date = 2009-07-06T02:47:00Z
updated = 2010-11-13T06:39:02Z
tags = ["dhcp", "router", "networking", "openwrt"]
blogimport = true 
type = "post"
+++

I use OpenWRT on my Linksys WRT54GL, all shell, no web interface. My basic problem is that both me and my roomate need ports forwarded from the internet to our systems. This means NAT, for nat you need to know the IP address that you're forwarding too. I could just 'static' the IP on our boxes and then set up the NAT. This is not the correct way to do things, as it would be much more difficult to keep track of who uses what IP and make sure that dnsmasq doesn't give out our static-ed IP's. Also when you static an IP on the client side that usually means setting dns, and remembering to unstatic it when you move. So we really want our dhcp server to give out the same IP address to a given mac address. to do this in OpenWRT you need to edit <code>/etc/ethers.local</code> the syntax of that file is basically <br /><code><br />mac ip<br />mac ip</code><br /><br />mine currently looks like this.<br /><br /><code>00:1e:8c:09:e7:13 192.168.1.2<br />00:21:9b:06:4c:c9 192.168.1.3</code><br /><br />These 2 computers will always get the same IP address while any other computer on the network may get a random IP address. After editing this file you need to run <code>/etc/init.d/luci_ethers start</code> which will then generate <code>/etc/ethers</code> which is what is actually used. You could also reboot the router but that is unnecessary.<br /><br /><i>update: it appears this has changed in OpenWRT 10.03 Backfire</i><div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>
