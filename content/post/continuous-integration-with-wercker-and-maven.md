+++
title = "Continuous Integration with Wercker and Maven"
date = 2015-06-20T14:40:00Z
updated = 2015-06-20T14:48:50Z
tags = ["wercker", "continous integration", "maven", "java", "code"]
categories = ["Java"]
blogimport = true 
type = "post"
aliases = ["/2015/06/continuous-integration-with-wercker-and.html"]
+++

I'm going to walk you through getting mvn test running in wercker, on the new docker based api.

First let's talk about what [Wercker][wercker] is and why you'd want to use it. Wercker a continuous integration and
deployment web application. It will all you to run any language or stack. It currently is free for both private and
public repositories; I am hopeful that once it comes out of beta it will maintain reasonable pricing for small personal
private projects (Most CI's are ridiculously priced for hobby projects). It can deploy to any platform.

So where does it fall apart? well although it seems reasonably well documented I've only rarely gotten something right
on the first try using its documentation. This largely revolves around the fact that its YAML parser seems more
sensitive to whitespace than a standard YAML parser, and their examples aren't properly spaced, and the validator
doesn't report the right line/position of the error. That being said I'm going to tell you how to get up and running
fast. Also they seem to have a problem keeping their platform up to date, the 1.0 platform only offers an Ubuntu base
that is a few years old already. The 2.0 platform is based on docker, so it takes care of that, but has some problems
such as it can't process the Dockerfile itself.

All this said Wercker accomplishes my need of having a continuous integration environment that is cheap enough to work
for both my open source and my private hobby projects, no matter what language I might use.

To get started you're going to need a Bitbucket or Github account as well as a wercker account, and a git repo. Before
creating the "application" on Wercker let's actually create our application in a git repo. All you need is a git repo
from which you can run `mvn test` at the root. To make this simple, [here is a spring boot sample project][sbs], you
can clone that, and copy the directory I linked into a new project, and create a new git repo and upload it to your git
host. [Here's a fully configured sample project I made][example]

So what we're going to focus on is the `wercker.yml`, create that file at the root of your git repo. the first thing
the file will need is a box, the box is the docker image your code runs in, and will deploy. For this we'll choose the
[maven docker image][maven], all you need to do is put `box: maven:latest` at the top of the file. After that we need 
to add build steps. build steps are individual units of work you need to do to build your project. There are ways to
write script steps or complicated custom steps, but those are for another article. We're just going to add the 
[maven build step I wrote][wercker-step] that works for 1, and 2.0 Though it works better for 1 as it supports caching
dependendencies and I haven't figured out if that's possible in 2. Below is the completed wercker.yml, steps has a 2
space indentation, the step name has a 4 space indentation and a -, and goals has an 8 space indentation, as mentioned
spacing is very important.
```yaml
box: maven:latest
build:
  steps:
    - xenoterracide/maven:
        goals: test
```

That's it, now go log into Wercker, click create -> application and follow the instructions, when you're done you
should be able to push commits, and wercker will run mvn test for you. Here's what [our sample][application] looks like
when you're done. That's it, simple right? have fun wercking with continuous integration on your projects.

[wercker]: http://wercker.com/
[sbs]: https://github.com/spring-projects/spring-boot/tree/master/spring-boot-samples/spring-boot-sample-simple
[example]: https://bitbucket.org/xenoterracide/werker-maven-spring-boot-example/src
[maven]: https://registry.hub.docker.com/_/maven/
[wercker-step]: https://bitbucket.org/xenoterracide/wercker-step-maven
[application]: https://app.wercker.com/#applications/54befcb456767b4663000750
