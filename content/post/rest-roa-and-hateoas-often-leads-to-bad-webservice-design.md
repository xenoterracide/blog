+++
title = "REST, ROA, and HATEOAS often leads to bad webservice design"
date = 2014-04-02T04:00:00Z
updated = 2015-08-23T21:38:27Z
tags = ["hateoas", "http", "web services", "rest", "performance"]
categories = ["Software Design", "Editorial"]
blogimport = true 
type = "post"
aliases = ["/2014/04/rest-roa-and-hateoas-often-leads-to-bad.html"]
+++

This is not to say that they are bad, but I find that all too frequently the resulting API's are poorly designed due
to forgetting one thing, RPC (Remote Procedure Call) is expensive. Now by RPC, I do not mean custom messaging formats
such as SOAP, or XML-RPC, I mean calling a method on a remote server. Do not think that just because you are using
HTTP as the message format with something like XML or JSON, that calling `GET /resource`, is significantly all that
different from calling get_resource in a SOAP call. The frequent idempotence also does not mean that you're not
actually doing RPC as often good method design server side also implies idempotence, e.g. adding an object to a Set
in Java will not result in the object being added twice if you add it twice. All calls to a remote is a form of RPC.
The most expensive part of RPC is creating a new connection, just how depends on the protocol. This is why web sockets,
for instance, is much cheaper than repeated calls (there are other reasons and expenses too, like maintaining many
connections).

I've worked with a few Resource Oriented Architecture (ROA) web services, and they each suffered from the same flawed
design, an excessive number of RPC calls was required to do seemingly simple tasks. This is caused by the, misguided,
belief that every single aggregate should be it's own resource and that components of the aggregate should also have
it's own resource, and that those should be the only access to the underlying aggregate. In one case working with an
ROA we had to do about 5 RPC calls for every single product we wanted to create, and we were bulk creating. This
problem was aggravated by the lack of an idempotent PUT for most resources.

The reality is, with a good API design we could have created all, of the objects we needed with a single API call to a
bulk interface. I'm talking the RESTful equivalent to a [Java `Collection.addAll( objs[] )`][collection]. In fact if
you use addAll on a Set, the result of multiple same calls is idempotent, the same object will not be added twice. It
would be really easy to write this given a good ORM, and a good interface so that you could do a `POST` or `PUT` to 
`/entities`.  this is a significant improvement to a design where you'd have to do a PUT or POST for every single item
you wanted to create. `DELETE` may be the only place where I'd consider not doing a bulk request, and it is generally
able to be completed asynchronously. You may of course consider limiting the number of entities acted on in a request,
so if you need to create 1000 entities, it might take 10 requests doing 100 at a time, this is still better for both
the client and the server than doing 1000 requests.

The choice between `PUT` and `POST` depends on whether you believe that the call to `GET` must return the exact same
view as `PUT`, meaning that a `PUT` would delete resources not included (for a single aggregate that's probably true),
or should the behavior be equivalent to addAll or replacing the reference to the collection with a new one. Remember 
`PUT` must be idempotent, this <em>only</em> means that subsequent calls using the exact same arguments should result
in the exact same result. You may want to consider using a different URI for manipulating your entity collections in
these ways.

Another problem that was encountered with a web service we encountered is it had sub resources, that had to exist prior
to creating the resource we needed to create, akin to tags. Not having a idempotent put to that resource meant we were
doing create on exception update. But given the simplicity of this resource it would have been even better to just
allow the api to take the final object representation of that resource, instead of requiring the id, and done a lookup
by name, or a create or update, under the hood. Doing this is more difficult logic wise, and impossible if there's no
natural key (because you can't look it up).

You probably are asking yourself, but how do I handle errors for these things. Well, the way I see it you have three
options. One requests are a transaction, so you wrap your database code with a transaction, and it either succeeds or
fails, you can return a 200 on success, ensure HATEOAS, with links to any new resources in the response. Two, you could
allow partial success, and return the successful objects. Three you could return a custom message envelope payload,
this isn't very RESTful because it's a protocol on top of HTTP (it's more like SOAP). <p>I'm currently working on
designing a new REST Web Service, and I've decided that no page load, or "single conceptual action" should take more
than 6 API requests. This number is not arbitrary, it's the
[median concurrent connection amount, per host name, for consumer web browsers][bs]. Even that number is too many,
but I felt that I needed to alot more than one request allowed due to some completely different actions that may need
to occur on a page load.

Keep on with the Resource Oriented REST with HATEOAS, just try to think of how to
minify the number of calls you make by designing less granular resources.

[collection]: https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html
[bs]: http://www.browserscope.org/?category=network
