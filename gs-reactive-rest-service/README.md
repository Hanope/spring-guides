## Building a Reactive RESTful Web Service
This guide walks you through the process of creating a "Hello, Spring!" RESTful web service with Spring WebFlux (new as of version 5) and then consumes that service with a WebClient (also new as of version 5).

> This guide shows the functional way of using Spring WebFlux. You can also [use annotations with WebFlux](http://alinktosomething/).

## What You’ll Build
You’ll build a RESTful web service with Spring Webflux and a WebClient consumer of that service. You’ll be able to see output in both System.out and at:

```
http://localhost:8080/hello
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

* [Download](https://github.com/spring-guides/gs-reactive-rest-service/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-reactive-rest-service.git`
* cd into `gs-reactive-rest-service/initial`
* Jump ahead to [Create a WebFlux Handler](#create-a-webflux-handler).

**When you’re finished**, you can check your results against the code in `gs-reactive-rest-service/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-reactive-rest-service/blob/master/initial/build.gradle).

`build.gradle`

```gradle
buildscript {
    ext {
        springBootVersion = '2.0.5.RELEASE'
    }
    repositories {
        mavenCentral()
        maven { url "https://repo.spring.io/snapshot" }
        maven { url "https://repo.spring.io/milestone" }
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

bootJar {
    baseName = 'gs-reactive-rest-service'
    version =  '0.1.0'
}

sourceCompatibility = 1.8

repositories {
    mavenCentral()
    maven { url "https://repo.spring.io/snapshot" }
    maven { url "https://repo.spring.io/milestone" }
}


dependencies {
    compile('org.springframework.boot:spring-boot-starter-webflux')
    testCompile('org.springframework.boot:spring-boot-starter-test')
    testCompile('io.projectreactor:reactor-test')
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
    <artifactId>gs-reactive-rest-service</artifactId>
    <version>0.1.0</version>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.5.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-webflux</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>

		<dependency>
			<groupId>io.projectreactor</groupId>
			<artifactId>reactor-test</artifactId>
			<scope>test</scope>
		</dependency>
    </dependencies>

    <properties>
        <java.version>1.8</java.version>
    </properties>

	<repositories>
		<repository>
			<id>spring-snapshots</id>
			<name>Spring Snapshots</name>
			<url>https://repo.spring.io/snapshot</url>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</repository>
		<repository>
			<id>spring-milestones</id>
			<name>Spring Milestones</name>
			<url>https://repo.spring.io/milestone</url>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</repository>
	</repositories>

	<pluginRepositories>
		<pluginRepository>
			<id>spring-snapshots</id>
			<name>Spring Snapshots</name>
			<url>https://repo.spring.io/snapshot</url>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</pluginRepository>
		<pluginRepository>
			<id>spring-milestones</id>
			<name>Spring Milestones</name>
			<url>https://repo.spring.io/milestone</url>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</pluginRepository>
	</pluginRepositories>

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

## Create a WebFlux Handler
In the Spring Reactive approach, we use a handler to handle the request and create a response, as shown in the following example:

`src/main/java/hello/GreetingHandler.java`

```java
package hello;

import org.springframework.http.MediaType;
import org.springframework.stereotype.Component;
import org.springframework.web.reactive.function.BodyInserters;
import org.springframework.web.reactive.function.server.ServerRequest;
import org.springframework.web.reactive.function.server.ServerResponse;

import reactor.core.publisher.Mono;

@Component
public class GreetingHandler {

	public Mono<ServerResponse> hello(ServerRequest request) {
		return ServerResponse.ok().contentType(MediaType.TEXT_PLAIN)
			.body(BodyInserters.fromObject("Hello, Spring!"));
	}
}
```

This simple reactive class always returns "Hello, Spring!" It could return many other things, including a stream of items from a database, a stream of items that were generated by calculations, and so on. Note the reactive code: a `Mono` object that holds a `ServerResponse` body.

## Create a Router
In this application, we use a router to handle the only route we expose ("/hello"), as shown in the following example:

`src/main/java/hello/GreetingRouter.java`

```java
package hello;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.MediaType;
import org.springframework.web.reactive.function.server.RequestPredicates;
import org.springframework.web.reactive.function.server.RouterFunction;
import org.springframework.web.reactive.function.server.RouterFunctions;
import org.springframework.web.reactive.function.server.ServerResponse;

@Configuration
public class GreetingRouter {

	@Bean
	public RouterFunction<ServerResponse> route(GreetingHandler greetingHandler) {

		return RouterFunctions
			.route(RequestPredicates.GET("/hello").and(RequestPredicates.accept(MediaType.TEXT_PLAIN)), greetingHandler::hello);
	}
}
```

The router listens for traffic on the `/hello` path and returns the value provided by our reactive handler class.

## Create a WebClient
The Spring MVC RestTemplate class is, by nature, blocking. Consequently, we don’t want to use it in a reactive application. For reactive applications, Spring offers the WebClient class, which is non-blocking. We’ll use a WebClient implementation to consume our RESTful service:

`src/main/java/hello/GreetingWebClient.java`

```java
package hello;

import org.springframework.http.MediaType;
import org.springframework.web.reactive.function.client.ClientResponse;
import org.springframework.web.reactive.function.client.WebClient;

import reactor.core.publisher.Mono;

public class GreetingWebClient {
	private WebClient client = WebClient.create("http://localhost:8080");

	private Mono<ClientResponse> result = client.get()
			.uri("/hello")
			.accept(MediaType.TEXT_PLAIN)
			.exchange();

	public String getResult() {
		return ">> result = " + result.flatMap(res -> res.bodyToMono(String.class)).block();
	}
}
```

The WebClient uses reactive features, in the form of a Mono to hold the content of the URI we specify and a function (in the `getResult` method) to turn that content into a string. If we had different requirements, we might turn it into something other than a string. Since we’re going to put the result into System.out, a string will do here.

> WebClient can be used to communicate with non-reactive, blocking services, too.

## Make the application executable
Although it is possible to package this service as a traditional [WAR](http://spring.io/understanding/WAR) file for deployment to an external application server, the simpler approach demonstrated below creates a standalone application. You package everything in a single, executable JAR file, driven by a Java `main()` method. Also, you use Spring’s support for embedding the [Tomcat](http://spring.io/understanding/Tomcat) servlet container as the HTTP runtime, instead of deploying to an external instance.

`src/main/java/hello/Application.java`

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);

		GreetingWebClient gwc = new GreetingWebClient();
		System.out.println(gwc.getResult());
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
java -jar build/libs/gs-reactive-rest-service-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-reactive-rest-service-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/) instead.

Logging output is displayed. The service should be up and running within a few seconds.

Once the service has started, you’ll see a line that reads:

`>> result = Hello, Spring!`

That line comes from the reactive content being consumed by the WebClient. Naturally, you can find something more interesting to do with your output than put it in System.out.

## Test the Application
Now that the application is running, you can test it. To start with, you can open a browser and go to http://localhost:8080/hello and see, "Hello, Spring!" For this guide, we also created a test class to get you started on testing with the WebTestClient class.

`src/test/java/hello/GreetingRouterTest.java`

```java
package hello;


import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.reactive.server.WebTestClient;

import org.junit.Test;
import org.junit.runner.RunWith;

@RunWith(SpringRunner.class)
//  We create a `@SpringBootTest`, starting an actual server on a `RANDOM_PORT`
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class GreetingRouterTest {

	// Spring Boot will create a `WebTestClient` for you,
	// already configure and ready to issue requests against "localhost:RANDOM_PORT"
	@Autowired
	private WebTestClient webTestClient;

	@Test
	public void testHello() {
		webTestClient
			// Create a GET request to test an endpoint
			.get().uri("/hello")
			.accept(MediaType.TEXT_PLAIN)
			.exchange()
			// and use the dedicated DSL to test assertions against the response
			.expectStatus().isOk()
			.expectBody(String.class).isEqualTo("Hello, Spring!");
	}
}
```

## Summary
Congratulations! You have developed a Reactive Spring application that includes a WebClient to consume a RESTful service!