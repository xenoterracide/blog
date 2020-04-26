+++
title = "Adventures with SOAP using Perl: Part 2 ( SOAP::Data::Builder )"
date = 2012-03-11T16:59:00Z
updated = 2012-03-11T17:10:01Z
tags = ["SOAP", "SOAP::Lite",  "perl", "SOAP::Data::Builder"]
blogimport = true 
type = "post"
+++

<p>Start by reading the first 2 parts : <ol style="list-style: none;">  <li>Part 0 <a href="http://www.xenoterracide.com/2011/10/adventures-with-soap-using-perl-part-0.html">Prelude (setup server.pl)</a></li>  <li>Part 1 <a href="http://www.xenoterracide.com/2011/10/adventures-with-soap-using-perl-part-1.html">SOAP::Lite</a></li></ol></p><a href="https://metacpan.org/module/SOAP::Data::Builder">SOAP::Data::Builder</a> is simply a wrapper around SOAP::Data and SOAP::Serializer for <a href="https://metacpan.org/module/SOAP::Lite">SOAP::Lite</a>. I used it because it made my life easier building nested complicated SOAP objects. However for Part 2 I will simply be showing how to use it to do the same code as Part 1. Unfortunately since Part 1 is so Simple this actually makes <a href="https://metacpan.org/module/SOAP::Data::Builder">SOAP::Data::Builder</a> more complex than <a href="https://metacpan.org/module/SOAP::Lite">SOAP::Lite</a> would be for this. In a future installment I will attempt to show more complex examples, but I will explain them less. Now let's take a look at the code. <p><p><script src="https://gist.github.com/1971794.js"> </script><noscript><pre><code>#!/usr/bin/perl<br />use 5.010;<br />use strict;<br />use warnings;<br />use SOAP::Lite +trace => [ 'debug' ];<br />use SOAP::Data::Builder;<br /><br />my $req = SOAP::Lite->new(<br />    readable   => 1,<br />    proxy      => 'http://localhost:8877',<br />    ns         => 'http://namesservice.thomas_bayer.com/',<br />);<br /><br />my $sb = SOAP::Data::Builder->new;<br /><br />$sb->add_elem(<br />    name  => 'name',<br />    value => 'Mark',<br />);<br /><br />my $res = $req->getNameInfo( $sb->to_soap_data );<br /><br />say '-' x 3;<br /><br />unless ( defined $res->fault ) {<br />    say scalar $res->valueof('//country');<br />} else {<br />    say $res->fault->{faultstring};<br />    say $res->fault->{detail}{error};<br />}</code></pre></noscript></p><p>As you can see this is much the same as the final code in <a href="http://www.xenoterracide.com/2011/10/adventures-with-soap-using-perl-part-1.html">Part 1</a>, and if you run it, it does exactly the same thing. The first difference you'll notice is the self explanatory creation of the <a href="https://metacpan.org/module/SOAP::Data::Builder">SOAP::Data::Builder</a> object. After that comes the <code>->add_elem</code> method call, which will simply create an element with a given element name and a value for that element. </p><p>Once you've finished adding elements to your <code>$sb</code> object, then you can call pass <code>$sb->to_soap_data</code> to the method that you're calling on SOAP::Lite, this generates the structure that SOAP::Lite needs to be able to make your request. </p><p>The only thing I didn't like about SOAP::Data::Builder is if you don't pass the right parameters to <code>->add_elem</code> it will not croak or error in any way that will really tell you what went wrong. <a href="https://github.com/xenoterracide/SOAP-Data-Builder/commit/f16f5d693a366f2d9fd2ecca693e7622a3d24cae">Simple patches to this can fix it</a>.</p><div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>