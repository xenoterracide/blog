+++
title = "Using Spring to create a full REST API in less than 60 lines of code"
date = 2014-09-02T22:42:00Z
updated = 2014-09-02T22:50:22Z
tags = [ "spring boot", "spring", "spring data", "java", "code", "hateoas"]
categories = ["Java"]
blogimport = true 
type = "post"
aliases = ["/2014/09/using-spring-to-create-full-rest-api-in.html"]
+++

Spring with Spring Data is awesome. Seriously, I've never been able to throw up a full HATEOAS REST web service this
fast. To start, I'll admit my headliner lie, I'm not counting the `pom.xml`. 

<!--more-->
```console
cloc .                                                                 slave-vi
       5 text files.
       5 unique files.
       2 files ignored.

http://cloc.sourceforge.net v 1.62  T=0.04 s (104.8 files/s, 3930.8 lines/s)
-------------------------------------------------------------------------------
Language                     files          blank        comment           code
-------------------------------------------------------------------------------
Maven                            1              6              7             65
Java                             3             15              0             57
-------------------------------------------------------------------------------
SUM:                             4             21              7            122
-------------------------------------------------------------------------------
```

The basics of the web service is we want to be able to create tasks, like those on a todo list, for now we want the
simplest tasks possible, in as little code possible. We should use UUID's so that our service can scale horizontally,
so that we can easily generate known test ID's and we know that no two entities will share an id if we ever wanted to
flatten things. We need to be able to perform basic CRUD on all of our entities as well as list them.

First let's create our `Task`.
```java
package com.lm.model;

import org.hibernate.annotations.GenericGenerator;
import org.hibernate.annotations.Type;

import javax.persistence.*;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;
import java.util.UUID;

@Entity
@Table(name = "tasks")
public class Task {

	@Id
	@NotNull
	@Type(type = "pg-uuid") // only works for postgres
	@GeneratedValue(generator = "uuid2")
	@GenericGenerator(name = "uuid2", strategy = "uuid2")
	@Column(name = "task_id", columnDefinition = "uuid")
	private UUID id;

	@NotNull
	@Size(min = 1, max = 100)
	private String description;

	public String getDescription() {
		return description;
	}

	public void setDescription(final String description) {
		this.description = description;
	}

	public UUID getId() {
		return id;
	}

	public void setId(final UUID id) {
		this.id = id;
	}
}
```
As you can see it's incredibly simple, we have our `UUID` identity, the `uuid` and `uuid2` basically are 
[telling Hibernate, H2, and PostgreSQL to use UUID's][dzone] You might ask why limit description to 100 characters,
well, since these are quick tasks, I might want to share them  in a tweet, and this allows enough room for a url
shortener plus the description. I think the rest is pretty self explanatory.

Now let's create our Repository.
```java
package com.lm.repository;

import com.lm.model.Task;
import org.springframework.data.repository.PagingAndSortingRepository;
import org.springframework.data.rest.core.annotation.RepositoryRestResource;

import java.util.UUID;

@RepositoryRestResource( collectionResourceRel = "task", path = "task")
public interface Tasks extends PagingAndSortingRepository<Task, UUID> {
}
```
Well that doesn't do anything... oh but it does, and although it doesn't show it, because this application doesn't need
it, there's a nifty method signature parser dsl that allows you to build queries just by writing a method signature.

Here's our `Application`.
```java
package com.lm;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
import org.springframework.data.rest.webmvc.config.RepositoryRestMvcConfiguration;

@Configuration
@EnableJpaRepositories
@Import(RepositoryRestMvcConfiguration.class)
@EnableAutoConfiguration
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
``` 
... and pom for dependencies and stuff.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.lm</groupId>
    <artifactId>lm</artifactId>
    <version>1.0-SNAPSHOT</version>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.1.5.RELEASE</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-rest</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <!--
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        -->
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
        </dependency>
    </dependencies>

    <properties>
        <!-- use UTF-8 for everything -->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <start-class>com.lm.Application</start-class>
    </properties>

    <build>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration><source>1.8</source><target>1.8</target></configuration>
            </plugin>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <repositories>
        <repository>
            <id>spring-releases</id>
            <url>http://repo.spring.io/libs-release</url>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>http://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>spring-releases</id>
            <url>http://repo.spring.io/libs-release</url>
        </pluginRepository>
    </pluginRepositories>
</project>
```

Here's the output of some curl commands I ran.
```http
HTTP/1.1 200 OK
Server: Apache-Coyote/1.1
Content-Type: application/hal+json
Transfer-Encoding: chunked
Date: Wed, 03 Sep 2014 02:21:49 GMT

{
  "_links" : {
    "task" : {
      "href" : "http://localhost:8080/task{?page,size,sort}",
      "templated" : true
    }
  }
}
```
```console
curl -i -X POST -H "Content-Type:application/json" -d'{  "description" : "first task" }' http://localhost:8080/task
```
```http
HTTP/1.1 201 Created
Server: Apache-Coyote/1.1
Location: http://localhost:8080/task/a599502c-dbb6-4bbc-9795-f7dd894a89e3
Content-Length: 0
Date: Wed, 03 Sep 2014 02:22:59 GMT
```
```console
curl -i http://localhost:8080/tasks
```
```http
HTTP/1.1 404 Not Found
Server: Apache-Coyote/1.1
Content-Type: application/json;charset=UTF-8
Transfer-Encoding: chunked
Date: Wed, 03 Sep 2014 02:23:17 GMT

{"timestamp":1409710997067,"status":404,"error":"Not Found","message":"","path":"/tasks"}
```
I created many first tasks before running this
```console
curl -i http://localhost:8080/task
```
```http
HTTP/1.1 200 OK
Server: Apache-Coyote/1.1
Content-Type: application/hal+json
Transfer-Encoding: chunked
Date: Wed, 03 Sep 2014 02:23:24 GMT

{
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/task{?page,size,sort}",
      "templated" : true
    }
  },
  "_embedded" : {
    "task" : [ {
      "description" : "first task",
      "_links" : {
        "self" : {
          "href" : "http://localhost:8080/task/f8eabf43-49be-477b-a119-4d61f2b84bba"
        }
      }
    }, {
      "description" : "first task",
      "_links" : {
        "self" : {
          "href" : "http://localhost:8080/task/61ab9de1-efac-4741-96f9-84c1a6cd059a"
        }
      }
    }, {
      "description" : "first task",
      "_links" : {
        "self" : {
          "href" : "http://localhost:8080/task/a599502c-dbb6-4bbc-9795-f7dd894a89e3"
        }
      }
    } ]
  },
  "page" : {
    "size" : 20,
    "totalElements" : 3,
    "totalPages" : 1,
    "number" : 0
  }
}
```
```console
curl -i http://localhost:8080/task/a599502c-dbb6-4bbc-9795-f7dd894a89e3
```
```http
HTTP/1.1 200 OK
Server: Apache-Coyote/1.1
Content-Type: application/hal+json
Transfer-Encoding: chunked
Date: Wed, 03 Sep 2014 02:25:41 GMT

{
  "description" : "first task",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/task/a599502c-dbb6-4bbc-9795-f7dd894a89e3"
    }
  }
}
```

```console
curl -i -X PUT -H "Content-Type:application/json" -d'{  "description" : "better" }' http://localhost:8080/task/a599502c-dbb6-4bbc-9795-f7dd894a89e3
```

```http
HTTP/1.1 204 No Content
Server: Apache-Coyote/1.1
Location: http://localhost:8080/task/a599502c-dbb6-4bbc-9795-f7dd894a89e3
Date: Wed, 03 Sep 2014 02:31:57 GMT
```

```console
curl -i -X DELETE http://localhost:8080/task/a599502c-dbb6-4bbc-9795-f7dd894a89e3
```

```http
HTTP/1.1 204 No Content
Server: Apache-Coyote/1.1
Date: Wed, 03 Sep 2014 02:32:51 GMT
```

```console
curl -i http://localhost:8080/task/a599502c-dbb6-4bbc-9795-f7dd894a89e3
```

```http
HTTP/1.1 404 Not Found
Server: Apache-Coyote/1.1
Content-Length: 0
Date: Wed, 03 Sep 2014 02:33:04 GMT
```
For a slightly more in depth tutorial you can see the [official spring date rest getting started page][datarestguide].
In the future I'll try to write about how to actually connect to PostgreSQL and set up API Authentication and
Authorization

People are always telling me how verbose Java is, how much less typing their language (especially Perl is). I'd love
to see a Perl app that can do all this in fewer lines of Perl (restriction, no line may be longer than 120 characters,
and must be humanly readable), I personally don't think it can be done at this time (not with full HATEOAS and as many
response codes), but I'm waiting for the day it can, and can be structured this simply.

[dzone]: http://java.dzone.com/articles/hibernate-and-uuid-identifiers
[datarestguide]: http://spring.io/guides/gs/accessing-data-rest/
