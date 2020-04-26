+++
title = "Flyweights in the wild"
date = 2013-10-24T19:40:00Z
updated = 2013-10-24T19:40:23Z
draft = true
blogimport = true 
type = "post"
+++

<p>I think knowing design patterns, how to implement them, and what they're called is important. I think this because they will make you a better developer, you'll reach for more flexible and easy to maintain solutions than you would otherwise. Knowing the name of the pattern (in theory) should also help you communicate with other developers. Sadly I've found this second reason less true in the wild, it seems most developers are content to not learn things beyond what's covered in things specific to their language.</p><p>Recently I noticed that the  <a href="http://sourcemaking.com/design_patterns/flyweight">Flyweight pattern</a> was probably (I haven't read the code ) used to <a href="http://6guts.wordpress.com/2013/10/11/nqp-gets-moarvm-support-cursor-reduction-and-other-news/">resolve an issue with cursor allocations in MoarVM</a></p><p>The flyweight pattern should essentially be used whenever you have a large number of objects
