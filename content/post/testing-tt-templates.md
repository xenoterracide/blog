+++
title = "Testing TT Template's"
date = 2010-06-03T21:18:00Z
updated = 2010-06-03T21:18:03Z
tags = ["TDD", "test",  "perl", "TT", "Template Toolkit"]
blogimport = true 
type = "post"
+++

So the poorly made patch the other day converted my Test::More test to use <a href="http://search.cpan.org/dist/Template-Toolkit/lib/Template/Test.pm">Template::Test</a> which removed quite a bit of code from the test itself. I hadn't seen Template::Test beforehand.<br /><br />Here's a very simple example of a test you could write to make sure a template is being output ok.<script src="http://gist.github.com/424771.js"></script><noscript><code><pre>#!perl<br />use strict;<br />use warnings;<br />use Template::Test;<br /><br />my $tt = Template->new();<br /><br />my $vars = {<br />        var => 'world',<br />};<br /><br />test_expect(\*DATA, $tt, $vars);<br /><br />__DATA__<br />--test--<br />hello [% var %]<br />--expect--<br />hello world</pre></code></noscript><br /><br />If you want more examples I now have quite a few for <a href="http://search.cpan.org/dist/Template-ShowStartStop/lib/Template/ShowStartStop.pm">Template::ShowStartStop</a> which you can see in the <a href="http://github.com/xenoterracide/Template-ShowStartStop/tree/master/t/">Test directory of the master branch</a>.<div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>
