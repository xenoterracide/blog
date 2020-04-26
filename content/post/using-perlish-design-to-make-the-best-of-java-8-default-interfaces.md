+++
title = "Using Perlish design to make the best of Java 8 Default interfaces"
date = 2014-03-28T19:29:00Z
updated = 2014-03-28T19:29:22Z
tags = ["object oriented design", "programming",  "Interfaces", "java"]
draft = true
blogimport = true 
type = "post"
+++

<p>Java has actual privacy, though after using it I feel that it is <a href="http://www.xenoterracide.com/2014/03/java-privacy-broken-by-design.html">broken by design</a> and that the reality is most things are public and that's probably ok. With Java 8 there's a new feature, default implementations in interfaces. This feature is similar to Perl Roles, or PHP Traits, basically you can share behavior between objects without using inheritance, even two or more completely different kinds of objects, which would usually have called for multiple inheritance. These compositional pieces work like a kind of like a zipper split in two, each piece is useless by itself, but when combined and zipped up it becomes a useful thing. The "problem" with this feature is that it doesn't allow you to access state, meaning no member access. But if you go the route of defining all member access in with methods and prefixing them with underscores, and define the accessor signatures with an interface, and now you can access state in a shared interface. </p><p>Here let's take three Repositories implemented without doing this.  </>
