+++
title = "Adventures with SOAP using Perl: Part 1 ( SOAP::Lite )"
date = 2011-10-22T15:00:00Z
updated = 2012-03-04T04:23:19Z
tags = ["SOAP", "SOAP::Lite",  "perl"]
blogimport = true 
type = "post"
aliases = ["/2011/10/adventures-with-soap-using-perl-part-1.html"]
+++

The most prevalent of SOAP libraries for Perl is [SOAP::Lite][sl] it is the oldest and most documented. Though for all
of its documentation it can be quite painful to figure out how to use it.

First make sure you've read [Part 0][prev] to set up the server. Once that's done let's look at the most simple way to
interface with this server.

In our first example we need to send a request to `getCountries`, which is a method provided by the API. You can
determine this by reading `namesservice.wsdl` and looking for the `operation`s to see what's available. Essentially
this means we need to send SOAP request with a Body `<getCountries />`. First we need to import SOAP::Lite.
```perl
#!/usr/bin/perl
use strict;
use warnings;
use SOAP::Lite +trace => [ 'debug' ];
```
If you look at our SOAP::Lite import you'll notice that were are passing the arguments `+trace => [ 'debug' ]`.
There are various levels and options for +trace, but this turns on full debug printing which will be sent to stderr.
You don't normally want to have debug running in production code, but it will be useful to illustrate our examples and
the request they send and receive.

Now let's look at creating an actual SOAP::Lite request object.
```perl
#!/usr/bin/perl
use strict;
use warnings;
use SOAP::Lite +trace => [ 'debug' ];

my $req = SOAP::Lite->new(
    readable   => 1,
    proxy      => 'http://localhost:8877',
    ns         => 'http://namesservice.thomas_bayer.com/',
);
```
The first option we pass in is `readable => 1`, adds whitespace to the request sent so that it's easier to read when
you're looking at the debug output, you should not enable this in production, as it makes the request bigger, and I
believe it is not considered correct SOAP as I've been told something about extra whitespace in SOAP being considered
invalid. The second option is `proxy => 'http://localhost:8877'` This specifies the hostname and port that the HTTP
request is sent to.`ns => 'http://namesservice.thomas_bayer.com/'` is the namespace, which you can find by looking for
namespace in the `namesservice.wsdl`. 

Now we need to actually create and send an actual request.
```perl
#!/usr/bin/perl
use strict;
use warnings;
use SOAP::Lite +trace => [ 'debug' ];

my $req = SOAP::Lite->new(
    readable   => 1,
    proxy      => 'http://localhost:8877',
    ns         => 'http://namesservice.thomas_bayer.com/',
);

my $res = $req->call( 'getCountries' );
```
For this trivial request we simply need to call the method that we need on the remote server and then return the
object. You can see that SOAP::Lite is generating a namespace for your request to use with the XML 
`<namesp1:getCountries xsi:nil="true">`, which is just fine in this case.

Of course we want to do something with our response. Please note that I've modified the code to use 5.10, but if you
want to use print instead of say this code will work fine on 5.6 and up.
```perl
#!/usr/bin/perl
use 5.010;
use strict;
use warnings;
use SOAP::Lite +trace => [ 'debug' ];

local $Data::Dumper::Purity = 1;
my $req = SOAP::Lite->new(
    readable   => 1,
    proxy      => 'http://localhost:8877',
    ns         => 'http://namesservice.thomas_bayer.com/',
);

my $res = $req->call( 'getCountries' );

say '---';

say scalar $res->valueof('Body/getCountriesResponse/country');
say join ' ', $res->valueof('Body/getCountriesResponse/country');
```
`valueof`, which is documented in [SOAP::SOM][ss], returns the first element in scalar context, and an array in array
context. So in my code I've shown both. The syntax used in the parameters to `valueof` is [XPath][xpath], so an even
simpler way to call it in this case would be `$res-&gt;valueof(//country);` and it would do the same thing with this
XML.

Next let's look at the `getNameInfo` method, it's a bit more complex so let's look at the XML in the XSD. Here's the
snippet that is really important.
```xml
<xs:complexType name="getNameInfo">                                                                                                                 
  <xs:sequence>                                                                                                                                     
    <xs:element name="name" type="xs:string" minOccurs="0"/>                                                                                        
  </xs:sequence>                                                                                                                                    
</xs:complexType>
```
This means that we need to send a request with a body that looks like
```xml
<getNameInfo>
    <name>Mark</name>
</getNameInfo>
```
_( note: you can look at the sample data in `MyExampleData.pm` for other names. )_ Set let's take a stab at writing
some Perl.
```perl
#!/usr/bin/perl
use 5.010;
use strict;
use warnings;
use SOAP::Lite +trace => [ 'debug' ];

my $req = SOAP::Lite->new(
    readable   => 1,
    proxy      => 'http://localhost:8877',
    ns         => 'http://namesservice.thomas_bayer.com/',
);

my $res = $req->getNameInfo(
    \SOAP::Data->new(
        name => 'name',
        value => 'Mark',
    )
);

say '---';

unless ( defined $res->fault ) {
    say scalar $res->valueof('//country');
} else {
    say $res->fault->{faultstring};
    say $res->fault->{detail}{error};
}
```
There are some important differences to note from our previous script. You'll notice that I call `->getNameInfo()`
directly on the request object, instead of passing it as a parameter to `->call`. This functions basically the same as
`call` and it will end up making the first tag inside of body. We could have doen this in our first example as 
`$req->getCountries;` and that would have been it. Now that we've covered the slight differences in calls, let's go
over the completely new things.

[SOAP::Data][sd] objects are used to create any further data structures. Obviously the hash key of `name` defines the
element name, and `value` defines what you want to go into it, here I have hardcoded "Mark"

If you run this code you'll notice that it returns a `faultstring` (among other fault properties) 
`"operation getNameInfo for SOAP11 called with invalid data"`, and details the error as 
`"element 'c-gensym3' not processed at {http://namesservice.thomas_bayer.com/}getNameInfo"`. Now go back and look at
the request, you'll see a `c-gensym3` element, where did that come from? Well, SOAP::Lite will generate elements for
anonymous elements but we can fix this.
```perl
#!/usr/bin/perl
use 5.010;
use strict;
use warnings;
use SOAP::Lite +trace => [ 'debug' ];

my $req = SOAP::Lite->new(
    readable   => 1,
    proxy      => 'http://localhost:8877',
    ns         => 'http://namesservice.thomas_bayer.com/',
);

my $res = $req->getNameInfo(
    SOAP::Data->new(
        name => 'name',
        value => 'Mark',
    )
);

say '---';

unless ( defined $res->fault ) {
    say scalar $res->valueof('//country');
} else {
    say $res->fault->{faultstring};
    say $res->fault->{detail}{error};
}
```
The only difference between this and the previous code is that we aren't putting a `\` in front of `SOAP::Data`. I
wrote it the first way because I had seen examples of that all over the place, and could not find a solution to getting
rid of the gensyms until I asked [this question on stackoverflow][so].

Unfortunately this is the most complex example that our server API has implemented. As an exercise to the reader I
suggest Implementing a request for the method `getNamesInCountry`, which is no more complex but available.

[sl]: https://metacpan.org/module/SOAP::Lite
[ss]: https://metacpan.org/module/SOAP::SOM#ACCESSING-ELEMENT-VALUES
[sd]: https://metacpan.org/module/SOAP::Data
[xpath]: http://www.w3schools.com/xpath/xpath_syntax.asp
[prev]: /post/adventures-with-soap-using-perl-part-0
[so]: http://stackoverflow.com/q/6986679/206466
