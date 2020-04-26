+++
title = "Log (CRLF) Injection with SLF4J"
date = 2017-02-27T04:00:00Z
updated = 2017-02-22T16:36:07Z
tags = ["spring", "spring boot", "exploit", "java", "code", "tutorial", "injection"]
categories = ["Security", "Java"]
blogimport = true 
type = "post"
aliases = ["/2017/02/log-crlf-injection-with-slf4j.html"]
+++

At my job we have a CIO installed policy of remediating issues found by a static analysis tool  and what it finds are
 most targeted at finding security issues. Currently this tool  is Veracode, and I don't recommend it, it misses more
 problems than it finds, and what it finds, including this issue, are often false positives. Our most common issue, is
  CRLF (Carriage Return Line Feed) or other log injection, which we have mitigated in a custom log appender (which 
  Veracode doesn't recognize).

So in order to educate people, I've made a sample app to demo what you can do, here's the code, if you're familiar with
SLF4J it should be pretty obvious. We're logging the arguments in various ways passed to the program (note: there's a
`README.md` on how to build it in [repo][repo] )

```java
package com.xenoterracide.log_injection_example;

import java.util.Arrays;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.stereotype.Component;

@SpringBootApplication
public class Application {
    private static final Logger log = LoggerFactory.getLogger( Application.class );

    public static void main( final String... args ) throws Exception {
        log.info( "STARTING" );
        new Runner().run( args );
        SpringApplication.run( Application.class, args );
    }

    @Component
    static class Runner implements CommandLineRunner {

        @Override
        public void run( final String... args ) throws Exception {
            log.info( "running with args '{}'", args );
            log.debug( "running {}", args );
            try {
                throw new IllegalArgumentException( Arrays.toString( args ) );
            }
            catch ( Exception e ) {
                log.error( "", e );
            }
        }
    }
}
```

When you run the code, it has 2 outputs, one with default SLF4J settings, and the other with Spring Boot's default
SLF4J settings. If you run this application with <code>hello</code> as an argument you'd get the following output,
there's no injection here, this is obviously what the developer intends for it to do. 

```
15:06:16.266 [main] INFO com.xenoterracide.log_injection_example.Application - STARTING
15:06:16.271 [main] INFO com.xenoterracide.log_injection_example.Application - running with args: hello
15:06:16.273 [main] DEBUG com.xenoterracide.log_injection_example.Application - running 'hello'
15:06:16.276 [main] ERROR com.xenoterracide.log_injection_example.Application - 
java.lang.IllegalArgumentException: [hello]
 at com.xenoterracide.log_injection_example.Application$Runner.run(Application.java:31)
 at com.xenoterracide.log_injection_example.Application.main(Application.java:19)
 at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
 at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
 at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
 at java.lang.reflect.Method.invoke(Method.java:498)
 at org.springframework.boot.loader.MainMethodRunner.run(MainMethodRunner.java:48)
 at org.springframework.boot.loader.Launcher.launch(Launcher.java:87)
 at org.springframework.boot.loader.Launcher.launch(Launcher.java:50)
 at org.springframework.boot.loader.JarLauncher.main(JarLauncher.java:51)

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.5.1.RELEASE)

2017-02-22 15:06:17.138  INFO 41153 --- [           main] c.x.log_injection_example.Application    : Starting Application v1.0-SNAPSHOT on Calebs-MacBook-Pro.local with PID 41153 (/Users/calebcushing/IdeaProjects/loginjectionexample/target/log-injection-example-1.0-SNAPSHOT.jar started by calebcushing in /Users/calebcushing/IdeaProjects/loginjectionexample)
2017-02-22 15:06:17.139  INFO 41153 --- [           main] c.x.log_injection_example.Application    : No active profile set, falling back to default profiles: default
2017-02-22 15:06:17.208  INFO 41153 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@41906a77: startup date [Wed Feb 22 15:06:17 CST 2017]; root of context hierarchy
2017-02-22 15:06:17.848  INFO 41153 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2017-02-22 15:06:17.861  INFO 41153 --- [           main] c.x.log_injection_example.Application    : running with args: hello
2017-02-22 15:06:17.864 ERROR 41153 --- [           main] c.x.log_injection_example.Application    : 

java.lang.IllegalArgumentException: [hello]
 at com.xenoterracide.log_injection_example.Application$Runner.run(Application.java:31) ~[classes!/:1.0-SNAPSHOT]
 at org.springframework.boot.SpringApplication.callRunner(SpringApplication.java:776) [spring-boot-1.5.1.RELEASE.jar!/:1.5.1.RELEASE]
 at org.springframework.boot.SpringApplication.callRunners(SpringApplication.java:760) [spring-boot-1.5.1.RELEASE.jar!/:1.5.1.RELEASE]
 at org.springframework.boot.SpringApplication.afterRefresh(SpringApplication.java:747) [spring-boot-1.5.1.RELEASE.jar!/:1.5.1.RELEASE]
 at org.springframework.boot.SpringApplication.run(SpringApplication.java:315) [spring-boot-1.5.1.RELEASE.jar!/:1.5.1.RELEASE]
 at org.springframework.boot.SpringApplication.run(SpringApplication.java:1162) [spring-boot-1.5.1.RELEASE.jar!/:1.5.1.RELEASE]
 at org.springframework.boot.SpringApplication.run(SpringApplication.java:1151) [spring-boot-1.5.1.RELEASE.jar!/:1.5.1.RELEASE]
 at com.xenoterracide.log_injection_example.Application.main(Application.java:20) [classes!/:1.0-SNAPSHOT]
 at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:1.8.0_112]
 at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:1.8.0_112]
 at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:1.8.0_112]
 at java.lang.reflect.Method.invoke(Method.java:498) ~[na:1.8.0_112]
 at org.springframework.boot.loader.MainMethodRunner.run(MainMethodRunner.java:48) [log-injection-example-1.0-SNAPSHOT.jar:1.0-SNAPSHOT]
 at org.springframework.boot.loader.Launcher.launch(Launcher.java:87) [log-injection-example-1.0-SNAPSHOT.jar:1.0-SNAPSHOT]
 at org.springframework.boot.loader.Launcher.launch(Launcher.java:50) [log-injection-example-1.0-SNAPSHOT.jar:1.0-SNAPSHOT]
 at org.springframework.boot.loader.JarLauncher.main(JarLauncher.java:51) [log-injection-example-1.0-SNAPSHOT.jar:1.0-SNAPSHOT]

2017-02-22 15:06:17.868  INFO 41153 --- [           main] c.x.log_injection_example.Application    : Started Application in 1.422 seconds (JVM running for 1.839)
2017-02-22 15:06:17.869  INFO 41153 --- [       Thread-2] s.c.a.AnnotationConfigApplicationContext : Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@41906a77: startup date [Wed Feb 22 15:06:17 CST 2017]; root of context hierarchy
2017-02-22 15:06:17.870  INFO 41153 --- [       Thread-2] o.s.j.e.a.AnnotationMBeanExporter        : Unregistering JMX-exposed beans on shutdown
```

One thing to notice here is that Spring Boot doesn't log debug by default, so no help there. Ok, so now let's see what
happens if I inject a log by inputing. The newline, after the quote, below is very important.

```
java -jar target/log-injection-example-1.0-SNAPSHOT.jar "
11:45:21.873 [main] INFO com.xenoterracide.log_injection_example.Application - running with args: bar"
```

that will generate this output 

```
11:45:21.873 [main] INFO com.xenoterracide.log_injection_example.Application - running with args: bar"
15:13:31.162 [main] INFO com.xenoterracide.log_injection_example.Application - STARTING
15:13:31.167 [main] INFO com.xenoterracide.log_injection_example.Application - running with args: 
11:45:21.873 [main] INFO com.xenoterracide.log_injection_example.Application - running with args: bar
15:13:31.168 [main] DEBUG com.xenoterracide.log_injection_example.Application - running '
11:45:21.873 [main] INFO com.xenoterracide.log_injection_example.Application - running with args: bar'
15:13:31.170 [main] ERROR com.xenoterracide.log_injection_example.Application - 
java.lang.IllegalArgumentException: [
11:45:21.873 [main] INFO com.xenoterracide.log_injection_example.Application - running with args: bar]
 at com.xenoterracide.log_injection_example.Application$Runner.run(Application.java:31)
 at com.xenoterracide.log_injection_example.Application.main(Application.java:19)
 at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
 at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
 at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
 at java.lang.reflect.Method.invoke(Method.java:498)
 at org.springframework.boot.loader.MainMethodRunner.run(MainMethodRunner.java:48)
 at org.springframework.boot.loader.Launcher.launch(Launcher.java:87)
 at org.springframework.boot.loader.Launcher.launch(Launcher.java:50)
 at org.springframework.boot.loader.JarLauncher.main(JarLauncher.java:51)

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.5.1.RELEASE)

2017-02-22 15:13:31.782  INFO 41187 --- [           main] c.x.log_injection_example.Application    : Starting Application v1.0-SNAPSHOT on Calebs-MacBook-Pro.local with PID 41187 (/Users/calebcushing/IdeaProjects/loginjectionexample/target/log-injection-example-1.0-SNAPSHOT.jar started by calebcushing in /Users/calebcushing/IdeaProjects/loginjectionexample)
2017-02-22 15:13:31.783  INFO 41187 --- [           main] c.x.log_injection_example.Application    : No active profile set, falling back to default profiles: default
2017-02-22 15:13:31.863  INFO 41187 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@41906a77: startup date [Wed Feb 22 15:13:31 CST 2017]; root of context hierarchy
2017-02-22 15:13:32.504  INFO 41187 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2017-02-22 15:13:32.516  INFO 41187 --- [           main] c.x.log_injection_example.Application    : running with args: 
11:45:21.873 [main] INFO com.xenoterracide.log_injection_example.Application - running with args: bar
2017-02-22 15:13:32.520 ERROR 41187 --- [           main] c.x.log_injection_example.Application    : 

java.lang.IllegalArgumentException: [
11:45:21.873 [main] INFO com.xenoterracide.log_injection_example.Application - running with args: bar]
 at com.xenoterracide.log_injection_example.Application$Runner.run(Application.java:31) ~[classes!/:1.0-SNAPSHOT]
 at org.springframework.boot.SpringApplication.callRunner(SpringApplication.java:776) [spring-boot-1.5.1.RELEASE.jar!/:1.5.1.RELEASE]
 at org.springframework.boot.SpringApplication.callRunners(SpringApplication.java:760) [spring-boot-1.5.1.RELEASE.jar!/:1.5.1.RELEASE]
 at org.springframework.boot.SpringApplication.afterRefresh(SpringApplication.java:747) [spring-boot-1.5.1.RELEASE.jar!/:1.5.1.RELEASE]
 at org.springframework.boot.SpringApplication.run(SpringApplication.java:315) [spring-boot-1.5.1.RELEASE.jar!/:1.5.1.RELEASE]
 at org.springframework.boot.SpringApplication.run(SpringApplication.java:1162) [spring-boot-1.5.1.RELEASE.jar!/:1.5.1.RELEASE]
 at org.springframework.boot.SpringApplication.run(SpringApplication.java:1151) [spring-boot-1.5.1.RELEASE.jar!/:1.5.1.RELEASE]
 at com.xenoterracide.log_injection_example.Application.main(Application.java:20) [classes!/:1.0-SNAPSHOT]
 at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:1.8.0_112]
 at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:1.8.0_112]
 at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:1.8.0_112]
 at java.lang.reflect.Method.invoke(Method.java:498) ~[na:1.8.0_112]
 at org.springframework.boot.loader.MainMethodRunner.run(MainMethodRunner.java:48) [log-injection-example-1.0-SNAPSHOT.jar:1.0-SNAPSHOT]
 at org.springframework.boot.loader.Launcher.launch(Launcher.java:87) [log-injection-example-1.0-SNAPSHOT.jar:1.0-SNAPSHOT]
 at org.springframework.boot.loader.Launcher.launch(Launcher.java:50) [log-injection-example-1.0-SNAPSHOT.jar:1.0-SNAPSHOT]
 at org.springframework.boot.loader.JarLauncher.main(JarLauncher.java:51) [log-injection-example-1.0-SNAPSHOT.jar:1.0-SNAPSHOT]

2017-02-22 15:13:32.524  INFO 41187 --- [           main] c.x.log_injection_example.Application    : Started Application in 1.168 seconds (JVM running for 1.594)
2017-02-22 15:13:32.525  INFO 41187 --- [       Thread-2] s.c.a.AnnotationConfigApplicationContext : Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@41906a77: startup date [Wed Feb 22 15:13:31 CST 2017]; root of context hierarchy
2017-02-22 15:13:32.526  INFO 41187 --- [       Thread-2] o.s.j.e.a.AnnotationMBeanExporter        : Unregistering JMX-exposed beans on shutdown
```

You can see that in the default SLF4J output we've created a convincing message, It looks like we have empty string
argument, and a second log statement. If you look at the debug message though, it has an unbalanced quote, which will
tip your hat to the idea that something strange has happened. This debug statement is not run in our "production"
spring boot. However if you look at the spring boot output the formatting is significantly different. Also this may
seem obvious in this contrived example, but would you really notice it if you were reading thousands of lines of logs?

Log injection gets dangerous in a few scenario's. The first would be if you're using a log analysis tool, it might not 
pick up the second log line as a fraud, and instead treat it as a real log and the former log as only having an empty
string. If they get the log format wrong (like with boot) then the analysis tool might not match the pattern and throw
it out. The next danger in log injection is XSS (or other types of injection). Take Docker Cloud (I don't know if it is
or isn't vulnerable) it allows you to view your containers logs in a web browser, now imagine I put javascript code
that pushes a malicious pdf (or something) to your computer "onload". When the system administrator goes in to view
these logs this pdf might download and then be opened in a vulnerable viewer. Now you have an admin with a trojan horse
on his computer. To be said other types of injection come in if you're saving logs to a database in an unsafe way, or
for some reason passing them to a parser that might interpret the content (like regex injection).

[repo]: https://bitbucket.org/xenoterracide/loginjectionexample
