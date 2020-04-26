+++
title = "Adventures with SOAP using Perl: Part 0 ( prelude )"
date = 2011-10-15T15:00:00Z
updated = 2012-03-04T05:17:57Z
tags = ["Business::CyberSource", "SOAP",  "perl"]
blogimport = true 
type = "post"
aliases = ["/2011/10/adventures-with-soap-using-perl-part-0.html"]
+++

This is a prelude to a series on working with SOAP Requests using Perl.  For the past 3 months I have been working on a
[Perl API][bc] for [CyberSource's Simple Order API][soapi] which uses SOAP (I should note, that although I believe most
of the API is now stable some area's still need work, and thus I don't expect it to reach 1.0.0 anytime soon).

First I used [SOAP::Lite][soaplite] to do my requests, but I found it confusing to construct the requests that I needed
to make. I even discovered a bug that lead to the current ( 0.714 ) release of
SOAP::Lite.

Next I started using [SOAP::Data::Builder][sdb] to make it easier to build my SOAP::Lite requests. This was good, but
frustrating that I had to add my data in a specific order.
Finally I came upon [XML::Compile::SOAP][xcs]. A glance at it's API which used a hash to build requests seemed much
better. however, it took me a few weeks and some help from Mark Overmeer (the author) and an update to 
[XML::Compile::SOAP::WSS][xcsw] to get it to work.

If you're planning on starting a new project that requires SOAP I definitely recommend using
[XML::Compile::SOAP][xcs] if you have a `.wsdl` and a `.xsd` to work with.

I will be covering all 3 of these methods in Parts 1, 2 and 3 of the series.

To get started we'll need a SOAP Server since I haven't been able to find any reliable public services. To do this you
can install [XML::Compile::SOAP::Daemon][xcsd] (which probably could use some Plack/PSGI love ). You'll want to grab a
copy of the namesservice example that is in XML::Compile::SOAP::Daemon, I have provided a 
[patched copy in a gist][gist]. Once you've done that you can do `perl server.pl` . Now you should have a Server
running on http://localhost:8877/ which we can use for testing our client examples.

Please be advised, these tutorials will not be explain XML, XSD, WSDL, or SOAP, but simply the Perl interfaces. 

**Update:** Here is [Part 1 ( SOAP::Lite )][next]

[bc]: https://metacpan.org/module/Business::CyberSource
[soapi]: http://www.cybersource.com/developers/develop/integration_methods/simple_order_and_soap_toolkit_api/
[soaplite]: https://metacpan.org/module/SOAP::Lite
[sdb]: https://metacpan.org/module/SOAP::Data::Builder
[xcs]: https://metacpan.org/module/XML::Compile::SOAP
[xcsw]: https://metacpan.org/module/XML::Compile::SOAP::WSS
[xcsd]: https://metacpan.org/module/XML::Compile::SOAP::Daemon
[gist]: https://gist.github.com/1971856
[next]: /post/adventures-with-soap-using-perl-part-1
