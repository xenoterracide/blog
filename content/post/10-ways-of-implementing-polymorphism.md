+++
title = "10 ways of implementing Polymorphism"
date = 2014-10-07T04:00:00Z
updated = 2014-10-07T11:37:20Z
categories = ["Software Design"]
tags = ["object oriented design",  "perl", "polymorphism", "java"]
blogimport = true 
type = "post"
aliases = ["/2014/10/10-ways-of-implementing-polymorphism.html"]
+++

Firstly what is Polymorphism and why is it so important? Polymorphism is the ability to have a many implementations of
a behavior that conform to a single interface. Put in perhaps slightly better, pragmatic terms, you have one
implementations of a caller, that can operate on many implementations of a "parameter", without conditionals, or
changing the callers code. For instance the following, pseudo?, Perl 6-ism `method handler( $obj ) { $obj.execute() }`.
As you can imagine `$obj` can be anything that has an execute method. For this Article I'll give you two
implementations and one caller, in either Perl 5 or 6, or Java 7 or 8, boilerplate will be excluded for brevity.

## Inheritance
### Single Inheritance

Single inheritance is the most simple and well understood form of Polymorphism.
```java
// this is a bad idea, use slf4j

class SimpleLogger {
  void log( String message ) {
    System.out.println(getClass() + message );
  }
}

class  InfoLogger extends SimpleLogger {
  Logger log = LoggerFactory.getLogger(LogOne.class);
  
  @Override
  void log( String message ) {
    log.info( message );
  }
}

class DebugLogger extends SimpleLogger {
  Logger log = LoggerFactory.getLogger(LogOne.class);
  
  @Override
  void log( String message ) {
    log.debug( message );
  }
}

class Logged { // logged will work with any of the above implementations
  void logHelloWorldWithSimpleLogger( SimpleLogger logger ) {
    logger.log("Hello, World");
  }
}
```
### Multiple Inheritance

Multiple inheritance is often considered dangerous, is unavailable in Java and suffers from the 
[The diamond problem][diamond]. You should really only
use this with a [C3 MRO][c3].
## Flat Composition
### Interfaces

Interfaces are probably the third most common form of Polymorhism, they are essentially codified contracts. 

```java
interface Logger {
    void log(String message );
}

class  InfoLogger implements Logger {
  Logger log = LoggerFactory.getLogger(InfoLogger.class);
  
  @Override
  void log( String message ) {
    log.info( message );
  }
}
 
class DebugLogger implements Logger {
  Logger log = LoggerFactory.getLogger(DebugLogger.class);
  
  @Override
  void log( String message ) {
    log.debug( message );
  }
}
 
class Logged { // logged will work with any of the above implementations
  void logHelloWorldWithSimpleLogger( Logger logger ) {
    logger.log("Hello, World");
  }
}
```

### Traits

These are just the same as Interfaces in Java 8 you say? well yes, that's what Java 8 calls them, 
[Traits are a list of methods flattened into a class, but they cannot access state][trait]. This basically describes
what Java 8 is doing, as you can't access properties from within the interface, well.. at least not unless you do what
I show here, which is basically access state through getters and setters.

```java
interface Logger {
    default void log(String message ) {
        _logger().info( message );
    }
    
    Logger _logger();
}
 
class  InfoLogger implements Logger {
    Logger log = LoggerFactory.getLogger(InfoLogger.class);
    @Override
    Logger _logger() {
        return log;
    }
}
 
class DebugLogger implements Logger {
    Logger log = LoggerFactory.getLogger(DebugLogger.class);
    @Override
    Logger _logger() {
        return log;
    }
}
 
class Logged { // logged will work with any of the above implementations
  void logHelloWorldWithSimpleLogger( Logger logger ) {
    logger.log("Hello, World");
  }
}
```
 
### Mixins

Mixins are basically traits that can access state, though some mixins (AFAIK Ruby) are implemented sneakily as multiple
inheritance, rather than flat list composition. IMHO, Mixins should be implemented using flat list composition.

```perl6
# mixins are called roles in perl 6 and also can behave as traits and interfaces
class ALogger { method debug( Str:D $message ) {...}}

class BLogger { method debug( Str:D $message ) {...}}

role Logger {
   method log( Str:D $message ) { ... }
}
role LoggerInfo {
   has $!log = ALogger.new();
}
role LoggerDebug {
   has $!log = BLogger.new();
}

class MyLogger does Logger, LoggerDebug, LoggerInfo {
    method log( Str:D $message ) {
         $!log.debug($message);
     }
}

class Handler { # Logger and UberLogger are valid
        method logHelloWorld( Logger:D $log ) {
                $log.log("hello world");
                # could call error, can't call trace (or maybe you can but shouldn't)
        }
}
```

## Typeless
### Duck Typing

The `has $!log` in the Mixin is actually a pretty good example of duck typing, we don't check for debug we are just 
calling it. Java is basically incapable of doing this, except, you can treat everything as an Object (if that's all
you need).

### Function References

references to functions may or may not be allowed to have varied signatures depending on the language, but so long as
they have the same signature they are interchangeable, and thus polymorphic. So why aren't normal functions 
(procedures), for example, Polymorphic, the problem with procedures is that you have to import the implementation from
outside the file, where with polymorphic code, you can create your instance outside the file, pass it into code that's
in the file, without changing the code, pass in a different implementation, and it'll continue to work. To modify
procedural code, you'd have to modify at least the import, and in compiled code that means a rebuild. It's worth noting
these aren't so much typeless as their is only one type to be concerned with, a function.

```java
class Logged {
    public static void exec ( Function<String,Void> log ) {
        log.apply("hello world");
    }
}

class LogIt {
    void logit() {
        Logged.exec( s -> System.out.println(s) );
        Logged.exec( s -> log.info(s) );
    }
}
```
 
## Miscellaneous

I'm personally skeptical of whether these actually fit the definition of Polymorphism, but they sort of do, just in
completely different ways from the above.

### Method Overloading

Method overloading is [called ad hoc polymorphism][polymorphism] and is kind of weird in that what it's really doing is
hiding the type change from the programmer. Reality is you're kind of asking for different behavior, but you want to
hide that it's different in the caller. However since it means you wouldn't have to change the caller, it counts.
```java
class  InfoLogger {
  Logger log = LoggerFactory.getLogger(InfoLogger.class);
  
  @Override
  void log( String message ) {
    log.info( message );
  }
}
 
class DebugLogger {
  Logger log = LoggerFactory.getLogger(DebugLogger.class);
  
  @Override
  void log( String message ) {
    log.debug( message );
  }
}
 
class Logged {
  void logHelloWorldWithSimpleLogger( DebugLogger logger ) {
      logger.log("Hello World");
  }

  void logHelloWorldWithSimpleLogger( Info logger ) {
    logger.log("Hello, World");
  }
}
```

### Generics

I describe generics as class templates, because they remind me of having an HTML template, and then filling in the
blanks by passing in variables, the variable happens to be a Type. Perl doesn't have Generics, and I'm not aware of
plans for it in Perl 6.
```java
interface Entity<ID> {
    ID getId();
    setId( ID id );
}
   

class Repository<ID, ENTITY extends Entity<ID>> {
  Map<ID, ENTITY> identityMap = new HashMap<>();
  void add( ENTITY entity ) {
    identityMap.put( entity.getId(), entity );
  }
}

// ID could be an Integer, UUID, String, or anything, ENTITY could be anything that 
// implements the Entity interface, and it's all strict typed at compile time, so you can't
// add a UUID, User to a repository specified with Integer, Post
```

### Reflection

Reflection is sort of polymorphic in that you can essentially treat all objects the same, via a single standard API.
I don't know that I want to show the kind of Reflective code because it get's real complicated fast, but for example,
`@Inject` can be annotated in systems with CDI compliant injector, they will reflectivly treat all objects with this
the same, and then set the annotated property.

[trait]: https://en.wikipedia.org/wiki/Trait_(computer_programming)
[polymorphism]: https://en.wikipedia.org/wiki/Polymorphism_(computer_science)
[diamond]: https://en.wikipedia.org/wiki/Multiple_inheritance#The_diamond_problem
[c3]: https://en.wikipedia.org/wiki/C3_linearization
