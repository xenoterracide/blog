+++
title = "Single Repository, one Aggregate"
date = 2015-07-02T02:00:00Z
updated = 2015-07-02T02:00:03Z
tags = ["spring boot", "spring data", "object oriented design",  "repository", "design patterns", "spring", "domain driven design", "java"]
categories = ["Java"]
blogimport = true 
type = "post"
aliases = ["/2015/07/single-repository-one-aggregate.html"]
+++

A [Repository][repository] as defined in [Domain Driven Design][ddd] manages a single [Aggregate][aggregate]. An
aggregate may contain many entities, and value objects, but will have a single object as its root. Many of the Dao and 
even now some of the Repository implementations I see do not follow this, they are more likely to have a Repository per
 entity, than a Repository per aggregate, and of course in some cases this is required for various reasons.
 
Ok, to start out we need our POM (or you can use Gradle), which configures our dependencies and plugins. We use a
starter for Spring Data JPA, which pulls in Spring Data JPA and all of it's suggested dependencies such as Hibernate.
We also need a database and a database driver so we configure H2. Since we are inheriting from the Spring Platform BOM
we don't need to specify versions as it can configure them for us. We of course want to use Java 8 and specify our 
Application class so we will be able to run `mvn spring-boot:run` at the end. 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://maven.apache.org/POM/4.0.0"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.xenoterracide</groupId>
  <artifactId>spring-data-aggregatepersist</artifactId>
  <version>1.0-SNAPSHOT</version>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
      <groupId>com.h2database</groupId>
      <artifactId>h2</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <properties>
    <java.version>1.8</java.version>
    <start-class>com.xenoterracide.Application</start-class>
  </properties>
  <parent>
    <groupId>io.spring.platform</groupId>
    <artifactId>platform-bom</artifactId>
    <version>1.1.3.RELEASE</version>
  </parent>
</project>
``` 

Next Let's configure our application to show the SQL that it is running, this isn't required. You need to put
`application.properties` in `src/main/resources`.
```properties
spring.jpa.show-sql: true
```
Now we need to create our Entities, let's start at the entity Bar that is the deepest part of the Aggregate root. It
extends [AbstractPersistable][ap] so that we get an Auto Incrementing or Sequenced id. We also use AbstractPersistable
because for our task we require that our entities implement [Persistable][persistable], as it changes the behavior of
save on the repository if your objects are new.
```java
package com.xenoterracide;


import org.springframework.data.jpa.domain.AbstractPersistable;

import javax.persistence.Column;
import javax.persistence.Entity;

@Entity
public class Bar extends AbstractPersistable<Long> {

    @Column
    private String baz;

    Bar() {
    }

    public Bar( final String baz ) {
        this.baz = baz;
    }

    public String getBaz() {
        return baz;
    }
}
```

Next let's create `Foo`, it is much the same, but you'll notice the `@OneToOne` that specifies `CascadeType.ALL`.
This is important as without it persist and merge won't work.
```java
package com.xenoterracide;

import org.springframework.data.jpa.domain.AbstractPersistable;

import javax.persistence.CascadeType;
import javax.persistence.Entity;
import javax.persistence.JoinColumn;
import javax.persistence.OneToOne;

@Entity
public class Foo extends AbstractPersistable<Long> {

	@OneToOne( cascade = CascadeType.ALL )
	@JoinColumn( nullable = false )
	private Bar bar;

	Foo() {
	}

	public Foo( final Bar bar ) {
		this.bar = bar;
	}

	public Bar getBar() {
		return bar;
	}
}
```

Alright, let's put together our repository, we could just make a `CrudRepository`, but let's show off some paging too.
You'll notice that you have to pass the Entity and it's Primary Key identifier type to the PagingAndSorting interface,
the single method that we specify will find all the Foos by the nested baz property, using a `LIKE '%mystring%'` query.
Spring data will parse this interface and make an implementation for you automatically.

```java
package com.xenoterracide;

import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.PagingAndSortingRepository;

public interface FooRepo extends PagingAndSortingRepository<Foo, Long> {

    @Query( "select f from Foo f" )
    Page<Foo> findAllCustom( Pageable pageable );

    @Query( "from Foo f" )
    Page<Foo> findAllCustomShort( Pageable pageable );

    @Query( "from Foo f order by id DESC" )
    Page<Foo> findAllCustomOrdered( Pageable pageable );

    Page<Foo> findAllByBarBazContaining( String baz, Pageable pageable );
}
```

You can create other lastly we do our Application, which is not designed to be a web server (it will exit immediately).
The `@SpringBootApplication` makes Spring Boot able to start the app and scan for components appropriately. We also
Enable JPA repositories using the `@EnableJpaRepositories`. It's main method (not recommended to prepopulate data this
way), creates and save several `Foo`s with nested `Bar`s, then I demonstrate a way that you can page the saved objects 2
at a time whilst filtering by that like statement, only 3 of the 4 entities saved will return.
```java
package com.xenoterracide;


import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;

@EnableJpaRepositories
@SpringBootApplication
public class Application {
	private static final Logger log = LoggerFactory.getLogger(Application.class);
	public static void main( final String[] args ) {
		ConfigurableApplicationContext context = SpringApplication.run( Application.class );
		FooRepo repo = context.getBean( FooRepo.class );

		repo.save( new Foo( new Bar( "baz0" ) ) );
		repo.save( new Foo( new Bar( "baz1" ) ) );
		repo.save( new Foo( new Bar( "az0" ) ) );
		repo.save( new Foo( new Bar( "2baz2bfoo" ) ) );

		Page<Foo> foos;
		Pageable pageable =  new PageRequest( 0, 2 );
		do {
			 foos = repo.findAllByBarBazContaining( "baz", pageable );
			log.info( "Total: {}", foos.getTotalElements() );

			foos.forEach( ( foo ) -> {
				log.info( "{}", foo );
				log.info( "{}", foo.getBar() );
			} );

			pageable = foos.nextPageable();
		}
		while ( ! foos.isLast() );

        log.info( "count: {}", repo.findAllCustom( pageable ).getTotalElements() );

		context.close();
	}
}
```

The [full source is available here][source].

[repository]: http://www.martinfowler.com/eaaCatalog/repository.html
[ddd]: http://dddcommunity.org/book/evans_2003/
[aggregate]: http://martinfowler.com/bliki/DDD_Aggregate.html
[ap]: http://docs.spring.io/spring-data/jpa/docs/current/api/org/springframework/data/jpa/domain/AbstractPersistable.html
[persistable]: http://docs.spring.io/spring-data/data-commons/docs/current/api/org/springframework/data/domain/Persistable.html
[source]: https://bitbucket.org/xenoterracide/spring-data-aggregate-persist
