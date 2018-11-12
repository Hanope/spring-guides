## Accessing Data in Pivotal GemFire with REST
This guide walks you through the process of creating an application that accesses Pivotal GemFire data through a [hypermedia-based RESTful front end](http://spring.io/understanding/REST).

## What you’ll build
You’ll build a Spring application that let’s you create and retrieve `Person` objects stored in a [MongoDB](https://www.mongodb.org/) [NoSQL](http://spring.io/understanding/NoSQL) database using Spring Data REST. Spring Data REST takes the features of [Spring HATEOAS](https://projects.spring.io/spring-hateoas) and [Spring Data MongoDB](https://projects.spring.io/spring-data-mongodb) and combines them together automatically.

> Spring Data REST also supports [Spring Data JPA](http://spring.io/guides/gs/accessing-data-rest), [Spring Data MongoDB](http://spring.io/guides/gs/accessing-mongodb-data-rest) and [Spring Data Neo4j](http://spring.io/guides/gs/accessing-neo4j-data-rest) as backend data stores, but those are not part of this guide.

> For more general knowledge of Pivotal GemFire concepts and accessing data from Pivotal GemFire, read through the guide, [Accessing Data with Pivotal GemFire](https://spring.io/guides/gs/accessing-data-gemfire/).

## What you’ll need
* About 15 minutes
* A favorite text editor or IDE
* [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) or later
* [Gradle 4+](http://www.gradle.org/downloads) or [Maven 3.2+](https://maven.apache.org/download.cgi)
* You can also import the code straight into your IDE:
  - [Spring Tool Suite (STS)](http://spring.io/guides/gs/sts)
  - [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea/)

## How to complete this guide
Like most Spring [Getting Started guides](http://spring.io/guides), you can start from scratch and complete each step, or you can bypass basic setup steps that are already familiar to you. Either way, you end up with working code.

To **start from scratch**, move on to [Build with Gradle](#build-with-gradle).

To **skip the basics**, do the following:

* [Download](https://github.com/spring-guides/gs-accessing-gemfire-data-rest/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-accessing-gemfire-data-rest.git`
* cd into `gs-accessing-gemfire-data-rest/initial`
* Jump ahead to [Create a domain object](#create-a-domain-object).

**When you’re finished**, you can check your results against the code in `gs-accessing-gemfire-data-rest/complete`.

## Build with Gradle
First you set up a basic build script. You can use any build system you like when building apps with Spring, but the code you need to work with [Gradle](http://gradle.org/) and [Maven](https://maven.apache.org/) is included here. If you’re not familiar with either, refer to [Building Java Projects with Gradle](http://spring.io/guides/gs/gradle) or [Building Java Projects with Maven](http://spring.io/guides/gs/maven).

### Create the directory structure
In a project directory of your choosing, create the following subdirectory structure; for example, with `mkdir -p src/main/java/hello` on *nix systems:

```
└── src
    └── main
        └── java
            └── hello
```

### Create a Gradle build file
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-accessing-gemfire-data-rest/blob/master/initial/build.gradle).

`build.gradle`

```gradle
buildscript {
    repositories {
        mavenCentral()
        maven { url "https://repo.spring.io/libs-release" }
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:2.0.5.RELEASE")
    }
}

plugins {
    id "io.spring.dependency-management" version "1.0.5.RELEASE"
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'

sourceCompatibility = 1.8
targetCompatibility = 1.8

bootJar {
    baseName = 'gs-accessing-gemfire-data-rest'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
    maven { url "https://repo.spring.io/libs-release" }
}

dependencies {

    compile("org.springframework.boot:spring-boot-starter-data-rest") {
        exclude group: "org.springframework.boot", module: "spring-boot-starter-logging"
    }
    compile("org.springframework.data:spring-data-gemfire")
    compile("org.projectlombok:lombok")

    runtime("org.springframework.shell:spring-shell:1.2.0.RELEASE")

    testCompile("org.springframework.boot:spring-boot-starter-test") {
        exclude group: "org.springframework.boot", module: "spring-boot-starter-logging"
    }
}
```

The [Spring Boot gradle plugin](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html) provides many convenient features:

* It collects all the jars on the classpath and builds a single, runnable "über-jar", which makes it more convenient to execute and transport your service.
* It searches for the `public static void main()` method to flag as a runnable class.
* It provides a built-in dependency resolver that sets the version number to match [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml). You can override any version you wish, but it will default to Boot’s chosen set of versions.

## Build with Maven
First you set up a basic build script. You can use any build system you like when building apps with Spring, but the code you need to work with [Maven](https://maven.apache.org/) is included here. If you’re not familiar with Maven, refer to [Building Java Projects with Maven](http://spring.io/guides/gs/maven).

### Create the directory structure
In a project directory of your choosing, create the following subdirectory structure; for example, with `mkdir -p src/main/java/hello` on *nix systems:

```
└── src
    └── main
        └── java
            └── hello
```

`pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.5.RELEASE</version>
    </parent>

    <groupId>org.springframework</groupId>
    <artifactId>gs-accessing-gemfire-data-rest</artifactId>
    <version>0.1.0</version>

    <properties>
        <spring-shell.version>1.2.0.RELEASE</spring-shell.version>
    </properties>

    <repositories>
        <repository>
            <id>spring-releases</id>
            <url>https://repo.spring.io/libs-release</url>
        </repository>
    </repositories>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-rest</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-gemfire</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.shell</groupId>
            <artifactId>spring-shell</artifactId>
            <version>${spring-shell.version}</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

The [Spring Boot Maven plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin) provides many convenient features:

* It collects all the jars on the classpath and builds a single, runnable "über-jar", which makes it more convenient to execute and transport your service.
* It searches for the `public static void main()` method to flag as a runnable class.
* It provides a built-in dependency resolver that sets the version number to match [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml). You can override any version you wish, but it will default to Boot’s chosen set of versions.

## Build with your IDE
* Read how to import this guide straight into [Spring Tool Suite](http://spring.io/guides/gs/sts/).
* Read how to work with this guide in [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea).

## Create a domain object
Create a new domain object to present a person.

`src/main/java/hello/Person.java`

```java
package hello;

import java.util.concurrent.atomic.AtomicLong;

import org.springframework.data.annotation.Id;
import org.springframework.data.annotation.PersistenceConstructor;
import org.springframework.data.gemfire.mapping.annotation.Region;

import lombok.Data;

@Data
@Region("People")
public class Person {

	private static AtomicLong COUNTER = new AtomicLong(0L);

	@Id
	private Long id;

	private String firstName;
	private String lastName;

	@PersistenceConstructor
	public Person() {
		this.id = COUNTER.incrementAndGet();
	}
}
```

The `Person` has a first and last name. Pivotal GemFire domain objects need an id, so an `AtomicLong` is being used to increment with each `Person` object creation.

## Create a Person repository
Next, you need to create a simple Repository to persist/access `Person` objects stored in Pivotal GemFire.

`src/main/java/hello/PersonRepository.java`

```java
package hello;

import java.util.List;

import org.springframework.data.repository.CrudRepository;
import org.springframework.data.repository.query.Param;
import org.springframework.data.rest.core.annotation.RepositoryRestResource;

@RepositoryRestResource(collectionResourceRel = "people", path = "people")
public interface PersonRepository extends CrudRepository<Person, Long> {

	List<Person> findByLastName(@Param("name") String name);

}
```

This *repository* is an interface and will allow you to perform various data access operations (e.g. basic CRUD and simple queries) involving `Person` objects. It gets these operations by extending `CrudRepository`.

At runtime, Spring Data for Pivotal GemFire will create an implementation of this interface automatically. Then, Spring Data REST will use the [@RepositoryRestResource](https://docs.spring.io/spring-data/rest/docs/current/api/org/springframework/data/rest/core/annotation/RepositoryRestResource.html) annotation to direct Spring MVC to create RESTful endpoints at `/people`.

> `@RepositoryRestResource` is not required for a repository to be exported. It is only used to change the export details, such as using `/people` instead of the default value of `/persons`.

Here you have also defined a custom query to retrieve a list of `Person` objects based on `lastName`. You’ll see how to invoke it further down in this guide.

## Make the application executable
Although it is possible to package this service as a traditional [WAR](http://spring.io/understanding/WAR) file for deployment to an external application server, the simpler approach demonstrated below creates a standalone application. You package everything in a single, executable JAR file, driven by a good old Java `main()` method. Along the way, you use Spring’s support for embedding the [Tomcat](http://spring.io/understanding/Tomcat) servlet container as the HTTP runtime, instead of deploying to an external instance.

`src/main/java/hello/Application.java`

```java
package hello;

import org.apache.geode.cache.client.ClientRegionShortcut;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.data.gemfire.config.annotation.ClientCacheApplication;
import org.springframework.data.gemfire.config.annotation.EnableEntityDefinedRegions;
import org.springframework.data.gemfire.repository.config.EnableGemfireRepositories;

@SpringBootApplication
@ClientCacheApplication(name = "AccessingGemFireDataRestApplication", logLevel = "error")
@EnableEntityDefinedRegions(basePackageClasses = Person.class,
	clientRegionShortcut = ClientRegionShortcut.LOCAL)
@EnableGemfireRepositories
@SuppressWarnings("unused")
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}
```

`@SpringBootApplication` is a convenience annotation that adds all of the following:

* `@Configuration` tags the class as a source of bean definitions for the application context.
* `@EnableAutoConfiguration` tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings.
* Normally you would add `@EnableWebMvc` for a Spring MVC app, but Spring Boot adds it automatically when it sees **spring-webmvc** on the classpath. This flags the application as a web application and activates key behaviors such as setting up a `DispatcherServlet`.
* `@ComponentScan` tells Spring to look for other components, configurations, and services in the `hello` package, allowing it to find the controllers.

The `main()` method uses Spring Boot’s `SpringApplication.run()` method to launch an application. Did you notice that there wasn’t a single line of XML? No **web.xml** file either. This web application is 100% pure Java and you didn’t have to deal with configuring any plumbing or infrastructure.

The `@EnableGemfireRepositories` annotation activates Spring Data for Pivotal GemFire Repositories. Spring Data for Pivotal GemFire will create a concrete implementation of the `PersonRepository` interface and configure it to talk to an embedded instance of Pivotal GemFire.

## Build an executable JAR
You can run the application from the command line with Gradle or Maven. Or you can build a single executable JAR file that contains all the necessary dependencies, classes, and resources, and run that. This makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.

If you are using Gradle, you can run the application using `./gradlew bootRun`. Or you can build the JAR file using `./gradlew build`. Then you can run the JAR file:

```
java -jar build/libs/gs-accessing-gemfire-data-rest-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-accessing-gemfire-data-rest-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file instead](http://spring.io/guides/gs/convert-jar-to-war/).

Logging output is displayed. The service should be up and running within a few seconds.

## Test the application
Now that the application is running, you can test it. You can use any REST client you wish. The following examples use the *nix tool `curl`.

First you want to see the top level service.

```bash
$ curl http://localhost:8080
{
  "_links" : {
    "people" : {
      "href" : "http://localhost:8080/people"
    }
  }
}
```

Here you get your first glimpse of what this server has to offer. There is a **people** link located at http://localhost:8080/people. Spring Data for Pivotal GemFire doesn’t support pagination like the other Spring Data REST guides so there are no extra navigational links.

> Spring Data REST uses the [HAL format](http://stateless.co/hal_specification.html) for JSON output. It is flexible and offers a convenient way to supply links adjacent to the data that is served.

```bash
$ curl http://localhost:8080/people
{
  "_links" : {
    "search" : {
      "href" : "http://localhost:8080/people/search"
    }
  }
}
```

Time to create a new `Person`!

```bash
$ curl -i -X POST -H "Content-Type:application/json" -d '{  "firstName" : "Frodo",  "lastName" : "Baggins" }' http://localhost:8080/people
HTTP/1.1 201 Created
Server: Apache-Coyote/1.1
Location: http://localhost:8080/people/1
Content-Length: 0
Date: Wed, 05 Mar 2014 20:16:11 GMT
```

* `-i` ensures you can see the response message including the headers. The URI of the newly created `Person` is shown
* `-X POST` signals this a POST used to create a new entry
* `-H "Content-Type:application/json"` sets the content type so the application knows the payload contains a JSON object
* `-d '{ "firstName" : "Frodo", "lastName" : "Baggins" }'` is the data being sent

> Notice how the previous `POST` operation includes a `Location` header. This contains the URI of the newly created resource. Spring Data REST also has two methods on `RepositoryRestConfiguration.setReturnBodyOnCreate(…)` and `setReturnBodyOnUpdate(…)` which you can use to configure the framework to immediately return the representation of the resource just created/updated.

From this you can query for all people:

```bash
$ curl http://localhost:8080/people
{
  "_links" : {
    "search" : {
      "href" : "http://localhost:8080/people/search"
    }
  },
  "_embedded" : {
    "persons" : [ {
      "firstName" : "Frodo",
      "lastName" : "Baggins",
      "_links" : {
        "self" : {
          "href" : "http://localhost:8080/people/1"
        }
      }
    } ]
  }
}
```

The **persons** collection resource contains a list with Frodo. Notice how it includes a **self** link. Spring Data REST also uses [Evo Inflector](http://www.atteo.org/2011/12/12/Evo-Inflector.html) to pluralize the name of the entity for groupings.

You can query directly for the individual record:

```bash
$ curl http://localhost:8080/people/1
{
  "firstName" : "Frodo",
  "lastName" : "Baggins",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/people/1"
    }
  }
}
```

> This might appear to be purely web based, but behind the scenes, it is talking to an embedded Pivotal GemFire database.

In this guide, there is only one domain object. With a more complex system where domain objects are related to each other, Spring Data REST will render additional links to help navigate to connected records.

Find all the custom queries:

```bash
$ curl http://localhost:8080/people/search
{
  "_links" : {
    "findByLastName" : {
      "href" : "http://localhost:8080/people/search/findByLastName{?name}",
      "templated" : true
    }
  }
}
```

You can see the URL for the query including the HTTP query parameter `name`. If you’ll notice, this matches the `@Param("name")` annotation embedded in the interface.

To use the `findByLastName` query, do this:

```bash
$ curl http://localhost:8080/people/search/findByLastName?name=Baggins
{
  "_embedded" : {
    "persons" : [ {
      "firstName" : "Frodo",
      "lastName" : "Baggins",
      "_links" : {
        "self" : {
          "href" : "http://localhost:8080/people/1"
        }
      }
    } ]
  }
}
```

Because you defined it to return `List<Person>` in the code, it will return all of the results. If you had defined it only return `Person`, it will pick one of the Person objects to return. Since this can be unpredictable, you probably don’t want to do that for queries that can return multiple entries.

You can also issue `PUT`, `PATCH`, and `DELETE` REST calls to either replace, update, or delete existing records.

```bash
$ curl -X PUT -H "Content-Type:application/json" -d '{ "firstName": "Bilbo", "lastName": "Baggins" }' http://localhost:8080/people/1
$ curl http://localhost:8080/people/1
{
  "firstName" : "Bilbo",
  "lastName" : "Baggins",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/people/1"
    }
  }
}
```

```bash
$ curl -X PATCH -H "Content-Type:application/json" -d '{ "firstName": "Bilbo Jr." }' http://localhost:8080/people/1
$ curl http://localhost:8080/people/1
{
  "firstName" : "Bilbo Jr.",
  "lastName" : "Baggins",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/people/1"
    }
  }
}
```

> `PUT` replaces an entire record. Fields not supplied will be replaced with `null`. `PATCH` can be used to update a subset of items.

You can delete records:

```bash
$ curl -X DELETE http://localhost:8080/people/1
$ curl http://localhost:8080/people
{
  "_links" : {
    "search" : {
      "href" : "http://localhost:8080/people/search"
    }
  }
}
```

A very convenient aspect of this [hypermedia-driven interface](http://spring.io/understanding/HATEOAS) is how you can discover all the RESTful endpoints using `curl` (or whatever REST client you are using). There is no need to exchange a formal contract or interface document with your customers.

## Summary
Congratulations! You’ve just developed an application with a [hypermedia-based RESTful](http://spring.io/guides/gs/rest-hateoas) frontend and a Pivotal GemFire-based backend.

## See Also
The following guides may also be helpful:

* [Accessing JPA Data with REST](https://spring.io/guides/gs/accessing-data-rest/)
* [Accessing MongoDB Data with REST](https://spring.io/guides/gs/accessing-mongodb-data-rest/)
* [Accessing data with MySQL](https://spring.io/guides/gs/accessing-data-mysql/)
* [Accessing Neo4j Data with REST](https://spring.io/guides/gs/accessing-neo4j-data-rest/)
* [Consuming a RESTful Web Service](https://spring.io/guides/gs/consuming-rest/)
* [Consuming a RESTful Web Service with AngularJS](https://spring.io/guides/gs/consuming-rest-angularjs/)
* [Consuming a RESTful Web Service with jQuery](https://spring.io/guides/gs/consuming-rest-jquery/)
* [Consuming a RESTful Web Service with rest.js](https://spring.io/guides/gs/consuming-rest-restjs/)
* [Securing a Web Application](https://spring.io/guides/gs/securing-web/)
* [Building REST services with Spring](https://spring.io/guides/tutorials/bookmarks/)
* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)
* [Creating API Documentation with Restdocs](https://spring.io/guides/gs/testing-restdocs/)
* [Enabling Cross Origin Requests for a RESTful Web Service](https://spring.io/guides/gs/rest-service-cors/)
* [Building a Hypermedia-Driven RESTful Web Service](https://spring.io/guides/gs/rest-hateoas/)