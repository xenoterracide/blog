+++
title = "Making Secure Recoverable Passwords ( part 3 )"
date = 2010-11-03T08:12:00Z
updated = 2010-11-03T08:12:11Z
tags = ["security", "passwords", "cryptography"]
blogimport = true 
type = "post"
+++

If you haven't read <a href="http://www.xenoterracide.com/2008/04/making-secure-recoverable-passwords.html">Part 1</a> please do so.<br /><br />Although the criticism of <a href="http://www.xenoterracide.com/2010/07/making-secure-recoverable-passwords.html">Part 2</a> should be taken with a grain of <a href="http://xkcd.com/538/">XKCD Salt</a>, and even the primary plaintiff admitted that it would take him 2 months to crack the final salted sha512. Usually your attacker shouldn't have your shadow file, and having stored them as anything less is just plain not secure. I will acknowledge it has some merit.<br /><br />Doing a base64 transformation on hex only digits is a bad, idea, and does not have nearly enough possible combinations. Nothing is going to be more secure than random, but random isn't really recoverable, if you lose it. That's why I do some kind of transformation.<br /><br />I believe that somewhere someone suggested that it would be better to convert from the binary digest into base64 as it would be more random than from hex. I believe this is accurate, but the method suggested was in Perl, which is kind of messy, and more importantly hard to remember. So <a href="http://unix.stackexchange.com/questions/3675/how-can-i-get-a-base64-encoded-shax-on-the-cli">I asked, on unix.stackexchange.com, how I could do this on the command line</a>. Here's the answer I decided to accept:<br /><br /><code>echo -n `date` | openssl dgst -binary -sha512 | base64</code><br /><br />Remember you should slightly modify the result in a way that you can remember in your head to make it random, and probably use something in place of the "date" command, since it's not reproduce-able.<br /><br />Again: this is not meant to be as secure as random passwords, just secure enough compared to <i>non random</i> alternatives.<div class="blogger-post-footer"><br />--<br />
This <span xmlns:dc="http://purl.org/dc/elements/1.1/" href="http://purl.org/dc/dcmitype/Text" rel="dc:type">work</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://www.xenoterracide.com" property="cc:attributionName" rel="cc:attributionURL">Caleb Cushing</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License</a>.</div>