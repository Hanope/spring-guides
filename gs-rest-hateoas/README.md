## Building a Hypermedia-Driven RESTful Web Service
This guide walks you through the process of creating a "hello world" [Hypermedia Driven REST web service](http://spring.io/understanding/HATEOAS) with Spring.

[Hypermedia](https://en.wikipedia.org/wiki/HATEOAS) is an important aspect of [REST](http://spring.io/understanding/REST). It allows you to build services that decouple client and server to a large extent and allow them to evolve independently. The representations returned for REST resources contain not only data, but links to related resources. Thus the design of the representations is crucial to the design of the overall service.

## What you’ll build
You’ll build a hypermedia-driven REST service with Spring HATEOAS, a library of APIs that you can use to easily create links pointing to Spring MVC controllers, build up resource representations, and control how they’re rendered into supported hypermedia formats such as HAL.

The service will accept HTTP GET requests at:

```
http://localhost:8080/greeting
```

and respond with a [JSON](http://spring.io/understanding/JSON) representation of a greeting enriched with the simplest possible hypermedia element, a link pointing to the resource itself:

```
{
  "content":"Hello, World!",
  "_links":{
    "self":{
      "href":"http://localhost:8080/greeting?name=World"
    }
  }
}
```

The response already indicates you can customize the greeting with an optional `name` parameter in the query string:

```json
http://localhost:8080/greeting?name=User
```

The `name` parameter value overrides the default value of "World" and is reflected in the response:

```json
{
  "content":"Hello, User!",
  "_links":{
    "self":{
      "href":"http://localhost:8080/greeting?name=User"
    }
  }
}
```

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

* [Download](https://github.com/spring-guides/gs-rest-hateoas/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-rest-hateoas.git`
* cd into `gs-rest-hateoas/initial`
* Jump ahead to [Create a resource representation class](#create-a-resource-representation-class).

**When you’re finished**, you can check your results against the code in `gs-rest-hateoas/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-rest-hateoas/blob/master/initial/build.gradle).

`build.gradle`

```gradle
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:2.0.5.RELEASE")
    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

bootJar {
    baseName = 'gs-rest-hateoas'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-hateoas")
    testCompile("com.jayway.jsonpath:json-path")
    testCompile("org.springframework.boot:spring-boot-starter-test")
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
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-rest-hateoas</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.5.RELEASE</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-hateoas</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.jayway.jsonpath</groupId>
            <artifactId>json-path</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <properties>
        <java.version>1.8</java.version>
    </properties>


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

## Create a resource representation class
Now that you’ve set up the project and build system, you can create your web service.

Begin the process by thinking about service interactions.

The service will expose a resource at `/greeting` to handle `GET` requests, optionally with a `name` parameter in the query string. The `GET` request should return a `200 OK` response with JSON in the body that represents a greeting.

Beyond that, the JSON representation of the resource will be enriched with a list of hypermedia elements in a `_links` property. The most rudimentary form of this is a link pointing to the resource itself. So the representation should look something like this:

```json
{
  "content":"Hello, World!",
  "_links":{
    "self":{
      "href":"http://localhost:8080/greeting?name=World"
    }
  }
}
```

The `content` is the textual representation of the greeting. The `_links` element contains a list of links, in this case exactly one with the relation type of `rel` and the `href` attribute pointing to the resource just accessed.

To model the greeting representation, you create a resource representation class. As the `_links` property is a fundamental property of the representation model, Spring HATEOAS ships with a base class `ResourceSupport` that allows you to add instances of `Link` and ensures that they are rendered as shown above.

So you simply create a plain old java object extending `ResourceSupport` and add the field and accessor for the content as well as a constructor:

`src/main/java/hello/Greeting.java`

```java
package hello;

import org.springframework.hateoas.ResourceSupport;

import com.fasterxml.jackson.annotation.JsonCreator;
import com.fasterxml.jackson.annotation.JsonProperty;

public class Greeting extends ResourceSupport {

    private final String content;

    @JsonCreator
    public Greeting(@JsonProperty("content") String content) {
        this.content = content;
    }

    public String getContent() {
        return content;
    }
}
```

* @JsonCreator - signal on how Jackson can create an instance of this [POJO](http://spring.io/understanding/POJO)
* @JsonProperty - clearly marks what field Jackson should put this constructor argument into

> As you’ll see in steps below, Spring will use the Jackson JSON library to automatically marshal instances of type `Greeting` into JSON.

Next you create the resource controller that will serve these greetings.

## Create a RestController
In Spring’s approach to building RESTful web services, HTTP requests are handled by a controller. The components are easily identified by the `@RestController` annotation, which combines the `@Controller` and `@ResponseBody` annotations. The `GreetingController` below handles `GET` requests for `/greeting` by returning a new instance of the `Greeting` class:

`src/main/java/hello/GreetingController.java`

```java
package hello;

import static org.springframework.hateoas.mvc.ControllerLinkBuilder.*;

import org.springframework.http.HttpEntity;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

@RestController
public class GreetingController {

    private static final String TEMPLATE = "Hello, %s!";

    @RequestMapping("/greeting")
    public HttpEntity<Greeting> greeting(
            @RequestParam(value = "name", required = false, defaultValue = "World") String name) {

        Greeting greeting = new Greeting(String.format(TEMPLATE, name));
        greeting.add(linkTo(methodOn(GreetingController.class).greeting(name)).withSelfRel());

        return new ResponseEntity<>(greeting, HttpStatus.OK);
    }
}
```

This controller is concise and simple, but there’s plenty going on. Let’s break it down step by step.

The `@RequestMapping` annotation ensures that HTTP requests to `/greeting` are mapped to the `greeting()` method.

> The above example does not specify `GET` vs. `PUT`, `POST`, and so forth, because `@RequestMapping` maps *all* HTTP operations by default. Use `@RequestMapping(path="/greeting", method=RequestMethod.GET)` to narrow this mapping. In that case you also want to `import org.springframework.web.bind.annotation.RequestMethod;`

`@RequestParam` binds the value of the query string parameter `name` into the `name` parameter of the `greeting()` method. This query string parameter is not `required`; if it is absent in the request, the `defaultValue` of "World" is used.

Because the `@RestController` annotation is present on the class, an implicit `@ResponseBody` annotation is being added onto the `greeting` method. This causes Spring MVC to render the returned `HttpEntity` and its payload, the `Greeting`, directly to the response.

The most interesting part of the method implementation is how you create the link pointing to the controller method and how you add it to the representation model. Both `linkTo(…)` and `methodOn(…)` are static methods on `ControllerLinkBuilder` that allow you to fake a method invocation on the controller. The `LinkBuilder` returned will have inspected the controller method’s mapping annotation to build up exactly the URI the method is mapped to.

> Spring HATEOAS respects various **X-FORWARDED-** headers. If you put a Spring HATEOAS service behind a proxy and properly configure it with **X-FORWARDED-HOST** headers, the resulting links will be properly formatted.

The call to `withSelfRel()` creates a `Link` instance that you add to the `Greeting` representation model.

## Make the application executable
Although it is possible to package this service as a traditional web application archive or [WAR](http://spring.io/understanding/WAR) file for deployment to an external application server, the simpler approach demonstrated below creates a standalone application. You package everything in a single, executable JAR file, driven by a good old Java `main()` method. And along the way, you use Spring’s support for embedding the [Tomcat](http://spring.io/understanding/Tomcat) servlet container as the HTTP runtime, instead of deploying to an external instance.

`src/main/java/hello/Application.java`

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
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

## Build an executable JAR
You can run the application from the command line with Gradle or Maven. Or you can build a single executable JAR file that contains all the necessary dependencies, classes, and resources, and run that. This makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.

If you are using Gradle, you can run the application using `./gradlew bootRun`. Or you can build the JAR file using `./gradlew build`. Then you can run the JAR file:

```
java -jar build/libs/gs-rest-hateoas-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-rest-hateoas-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/) instead.

Logging output is displayed. The service should be up and running within a few seconds.

## Test the service
Now that the service is up, visit http://localhost:8080/greeting, where you see:

```json
{
  "content":"Hello, World!",
  "_links":{
    "self":{
      "href":"http://localhost:8080/greeting?name=World"
    }
  }
}
```

Provide a `name` query string parameter with http://localhost:8080/greeting?name=User. Notice how the value of the `content` attribute changes from "Hello, World!" to "Hello, User!" and the `href` attribute of the `self` link reflects that change as well:

```json
{
  "content":"Hello, User!",
  "_links":{
    "self":{
      "href":"http://localhost:8080/greeting?name=User"
    }
  }
}
```

This change demonstrates that the `@RequestParam` arrangement in `GreetingController` is working as expected. The `name` parameter has been given a default value of "World", but can always be explicitly overridden through the query string.

## Summary
Congratulations! You’ve just developed a hypermedia-driven RESTful web service with Spring HATEOAS.

## See Also
The following guides may also be helpful:

* [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)
* [Accessing GemFire Data with REST](https://spring.io/guides/gs/accessing-gemfire-data-rest/)
* [Accessing MongoDB Data with REST](https://spring.io/guides/gs/accessing-mongodb-data-rest/)
* [Accessing data with MySQL](https://spring.io/guides/gs/accessing-data-mysql/)
* [Accessing JPA Data with REST](https://spring.io/guides/gs/accessing-data-rest/)
* [Accessing Neo4j Data with REST](https://spring.io/guides/gs/accessing-neo4j-data-rest/)
* [Consuming a RESTful Web Service](https://spring.io/guides/gs/consuming-rest/)
* [Consuming a RESTful Web Service with AngularJS](https://spring.io/guides/gs/consuming-rest-angularjs/)
* [Consuming a RESTful Web Service with jQuery](https://spring.io/guides/gs/consuming-rest-jquery/)
* [Consuming a RESTful Web Service with rest.js](https://spring.io/guides/gs/consuming-rest-restjs/)
* [Securing a Web Application](https://spring.io/guides/gs/securing-web/)
* [Building REST services with Spring](https://spring.io/guides/tutorials/bookmarks/)
* [React.js and Spring Data REST](https://spring.io/guides/tutorials/react-and-spring-data-rest/)
* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)
* [Creating API Documentation with Restdocs](https://spring.io/guides/gs/testing-restdocs/)
* [Enabling Cross Origin Requests for a RESTful Web Service](https://spring.io/guides/gs/rest-service-cors/)