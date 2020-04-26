+++
title = "Adding and Deleting subdomains with Plesk on the Command Line"
date = 2011-04-29T07:31:00Z
updated = 2011-04-29T07:31:13Z
tags = ["plesk", "dns", "commands"]
blogimport = true 
type = "post"
+++

Plesk has the disadvantage that everything is done through the<br />database. So we can't just modify Bind's Zone files.<br /><br />To add an A record of a subdomain you have to do the following:<br /><br /><code>/usr/local/psa/bin/dns -a domain.tld -a subdomain -ip 127.0.0.1</code><br /><br />the first -a is for add to zone which is why it must specify the top<br />level domen, the second is for A record, and must not contain the full<br />domain only the subdomain. If you use the full domain<br />subdomain.domain.tld you will end up with a record like<br />subdomain.domain.tld.domain.tld because plesk does not allow you to<br />terminate with a .. Thank you plesk for a shitty interface. The record<br />this create's looks like<br /><br /><code>subdomain.domain.tld. IN A 127.0.0.1</code><br /><br />To delete this record you would use the following<br /><br /><code>/usr/local/psa/bin/dns -d domain.tld -a subdomain -ip 127.0.0.1</code><br /><br />you'll note that it's the exact same thing except you replace the<br />first -a which is for add, with -d for delete.<div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>
