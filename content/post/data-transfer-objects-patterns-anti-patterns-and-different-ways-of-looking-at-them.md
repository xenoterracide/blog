+++
title = "Data Transfer Objects patterns, anti-patterns, and different ways of looking at them"
date = 2015-01-31T13:19:00Z
updated = 2015-01-31T13:19:10Z
draft = true
blogimport = true 
type = "post"
+++

<p>First let's define what a <a href="http://martinfowler.com/eaaCatalog/dataTransferObject.html">Data Transfer Object (DTO)</a> is. The definition given by Fowler was <em>"An object that carries data between processes in order to reduce the number of method calls."</em>. That's a pretty good definition, but I think it's not the only reason that they get used. I would say that a DTO is an object that's data structure represents a different, or potentially different, view of the model to facilitate data transfer. Most of the time these are used when communicating to a remote API that has a different model than your model as part of an anti corruption layer. </p>
