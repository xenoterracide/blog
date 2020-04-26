+++
title = "From Perl to Java"
date = "2017-03-30"
type = "post"
tags = ["perl", "java"]
categories = ["Editorial"]
+++

## What's next

This is my last post that targets [IronMan][ironman]. So if you want to keep reading my blog, which will probably 
argely be a mix of security, and [java][java] with occasional dev ops, please subscribe directly. If you have
suggestions for what categories I should collect things under fee free to comment. Currently the only for sure 
category is [Security][sec].

## Why not Perl?

This post is 3 years in the making. I started writing it in 2014 and just never published it. Now with my new blogging
platform, which is less painful to write in, I figured I'd update it and publish it. I haven't really touched Perl
since 2014, so my information may not be current. Sorry if I say something out of date.

1. Not enough job openings for developers in one city
1. Frameworks that better support the features, patterns, and paradigms I want
1. Perl was taking steps backwards in 2014
1. Java was outpacing Perl
1. Community and Culture issues

## Not enough job openings for developers in one city

I'm bad at job hunting, but after screwing up a telecommute job, yes it was my fault, but I don't think it was the right
place for me. I spent a few months soul searching and looking for Perl jobs. What did I find? nothing. At that time I
didn't want to risk relocation. 

After spending a few months learning how to write java to a novice level, I spent some time looking for a job doing it,
but I had no experience in it, so I had to find someone willing to take the risk. Outside of a brief hiatus, I've
been at that company since. I suspect I could land a job doing Java within a few weeks if I weren't fussy about it, as
there are Java jobs posted every day.

## Frameworks that better support the features, patterns, and paradigms I want

So a long time ago I learned a lot about architecture and design. I've done this to the point where I've rearchitected
our application, and redesigned procedural code to be flexible and polymorphic. This has eliminated hard to understand
code, prevented errors like circular dependencies, and reduced code. Spring and Hibernate have given me the flexibility
to do this, and Spring Boot has allowed me to do it in significantly less code than I would have written in Perl. Do I
like all Java? no I tried Java EE first and Wildfly (JBoss) is an absolutely terrible product that would make even a
masochist quit. Why don't people like Java? I think it's because EE has largely been terrible, they learned Java
without frameworks, or they learned Java pre Java 8, or XML.

So what features, paradigms? Java 8 has support for Object Oriented programming, missing only a handful of
features that Perl 6 supports. Java 8  also supports functional programming, perhaps not to the point of Perl 5, or 6
or a functional language, but it supports 95% of what I need to do. Java has strict typing, once you're using frameworks
this only rarely gets in the way. I've found that dynamic typing means I have to write more tests, and often it's only
useful in area's where user input is concerned (or tests).

For patterns, Java has Hibernate, a [Data Mapper][dm], last I checked Perl only had [Active Record][ar] style ORMs. 
[Spring Data][sd] make's making [Repositories][repo] easy, I once tried writing this in Perl before I saw in in Spring
Data.

[Spring][spring] is a [Dependency Injection][di] and can do string, annotation, and even annotation-less type 
based injection, Bread::Board couldn't hold a candle to even Springs Java Config (death to XML!). Spring also supplies,
[AOP][aop], and although there are AOP modules on CPAN, they all feel like a hack, and don't play well with other
modules. Flexible UI Design Patterns, at work we use a [Presentation Model][pm], and whichever child of MVC that is
an HTTP Controller. Spring MVC like Catalyst also supports decentralized routing, with Spring 5 they will support
centralized, though I think it's a mistake. You can even build full HATEOAS support, and it's easy with MVC, and Spring
Data. There's a well integrated [Security][sec] framework to prevent you from making easy mistakes when implementing
basic app security, along with supporting whatever flexibility you need. Lastly there's Spring Boot that cuts all the 
boilerplate of putting it together, adds support for microservices, with things like [12 Factor configuration][12f]
and instant on health check service.

Where is Perl better? Well I think Perl is better for writing tests, this is because Perl is a dynamic language,
and tests require a lot of setup, and Java makes it verbose. So use Groovy or Kotlin for tests if this is a complaint.

Missing all of these things in Perl lead me to frequently wanting to build my own framework, and lot's of utilies.

## Perl was taking steps backwards in 2014 and Java was outpacing Perl

Not much to say here, 
[5.18 was a boring release that marked features that had been stable since 5.10 as experimental][518].
This was about 1 year before Java 8 went final, which brought, Stream's, Lambda's, Defender Methods (Role's without
direct state access). 

Are there things I could do in Perl that I can't do in Java? Absolutely, the one I miss the most
is postfix if, e.g `$self->{var} if $var != undef`. Another is "key value" constructors, as java only supports array
based constructors, I think some of java's most verbose code is because of this, see getters, setters, Builders, and to
lesser extent Factories. Fortunately I can generate getter's and setters with my IDE, and Builders are only as needed.
If you really, *really* hate those, project [Lombok][lombok], and [Immutables][immutables] can cut the boiler plate,
because java natively supports code generation. Most of it I don't miss though, because I found that a lot of
things aren't necessary once you get types involved.

Recently, I've realized that I miss [Dist::Zilla][dz], [Maven][mvn], just isn't as good, and [Gradle][gradle] has 
[one too many bugs][fbug] for my taste (no seriously, I just need that fixed). Ok Gradle may have other issues,
ultimately I have no idea if it solves the problem that DZ solved, which was distribution "provisioning". I need more
than skeletons, and I'm frustrated to the point of considering starting my own.

## Community and Culture issues

Let's start with culture, specifically the corporate culture of Perl. Corporate Perl, from what I observed was largely
a CPAN-less mish mash of home grown frameworks and scripts. This code had terrible naming and structure.

The community of course was the opposite of this, but it was hard to see if any of this code was broadly used.

One of the good things the community has over Java, is [cpantesters][cpt]. Java has this problem of fixing versions to
known working versions, and then only testing on that, and only in the one configuration, and usually only on build. 
Whereas cpantesters throws a motley set of configuration at libraries, at completely arbitrary times. Sometimes in fact
individuals get a little out of hand and configure things that shouldn't be set and won't pass as is (like running
author tests). This problem in Java is part of Jar Hell, how do I find out the 20 versions that work together, and they
aren't usually the latest. The best solution I've seen to this is [Spring Platform][sp], which provides a large set
of fixed dependencies that are "generally" known to work together.

I did have a problem with specific community members though, the root cause of me to finally sign off on all of my
modules. This people prominent member of the community, would pester about issues that were by design, but
refused to listen to the arguments about why they were that way. They proceeded to show up pestering me about this
on abandoned modules even after. I noticed for at least up to a year after these individuals hadn't updated the modules
they'd whined so much about. They may have at this point, it is nice to see that some of my work lives on, maintained
by others.

Of course this should be taken with a grain of salt, not many people like me either, and I'm sure I haven't
been much better in certain cases. Karma maybe. I probably would have handled it differently too if I wasn't already 
checked out. Ultimately all communities that actually have multiple developers and users are going to have some of this,
and in fact I could say worse things about a community in Java I would say has actually taken my code without giving due
attribution.

## Conclusion

Java isn't perfect, neither is Perl, use what you enjoy and can get paid to use in an environment you enjoy.

[java]: /tags/java
[sec]: /categories/security

[12f]: https://12factor.net/
[ironman]: http://ironman.enlightenedperl.org/
[fbug]: https://discuss.gradle.org/t/no-console-output-option-for-findbugs-plugin/11974
[mvn]: https://maven.apache.org/
[gradle]: https://gradle.org/
[dz]: http://dzil.org/
[518]: http://perldoc.perl.org/perl5180delta.html#The-smartmatch-family-of-features-are-now-experimental
[lombok]: https://projectlombok.org/
[immutables]: https://immutables.github.io/
[cpt]: http://cpantesters.org/

[dm]: https://martinfowler.com/eaaCatalog/dataMapper.html
[ar]: https://martinfowler.com/eaaCatalog/activeRecord.html
[pm]: https://martinfowler.com/eaaDev/PresentationModel.html
[di]: https://martinfowler.com/articles/injection.html
[repo]: https://martinfowler.com/eaaCatalog/repository.html

[spring]: https://projects.spring.io/spring-framework/
[aop]: https://docs.spring.io/spring/docs/4.3.x/spring-framework-reference/htmlsingle/#aop-introduction
[sec]: https://projects.spring.io/spring-security/
[sp]: https://platform.spring.io/platform/
[sd]: http://projects.spring.io/spring-data/
