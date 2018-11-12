## Routing and Filtering
This guide walks you through the process of routing and filtering requests to a microservice application using the Netflix Zuul edge service library.

## What you’ll build
You’ll write a simple microservice application and then build a reverse proxy application that uses [Netflix Zuul](https://github.com/spring-cloud/spring-cloud-netflix) to forward requests to the service application. You’ll also see how to use Zuul to filter requests made through the proxy service.

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

* [Download](https://github.com/spring-guides/gs-routing-and-filtering/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-routing-and-filtering.git`
* cd into `gs-routing-and-filtering/initial`
* Jump ahead to [Set up a microservice](#setup-a-microservice).

**When you’re finished**, you can check your results against the code in `gs-routing-and-filtering/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-routing-and-filtering/blob/master/initial/build.gradle).

`build.gradle`

```gradle
buildscript {
	ext {
		springBootVersion = '1.4.0.RELEASE'
	}
	repositories {
		mavenCentral()
	}
	dependencies {
		classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
	}
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'spring-boot'
apply plugin: 'io.spring.dependency-management'

jar {
	baseName = 'book'
	version = '0.0.1-SNAPSHOT'
}
sourceCompatibility = 1.8
targetCompatibility = 1.8

repositories {
	mavenCentral()
}


dependencies {
	compile('org.springframework.boot:spring-boot-starter-web')
	testCompile('org.springframework.boot:spring-boot-starter-test')
}


eclipse {
	classpath {
		 containers.remove('org.eclipse.jdt.launching.JRE_CONTAINER')
		 containers 'org.eclipse.jdt.launching.JRE_CONTAINER/org.eclipse.jdt.internal.debug.ui.launcher.StandardVMType/JavaSE-1.8'
	}
}

task wrapper(type: Wrapper) {
	gradleVersion = '2.9'
}
```

`gateway/build.gradle`

```gralde
buildscript {
	ext {
		springBootVersion = '1.4.0.RELEASE'
	}
	repositories {
		mavenCentral()
	}
	dependencies {
		classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
	}
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'spring-boot'
apply plugin: 'io.spring.dependency-management'

jar {
	baseName = 'gateway'
	version = '0.0.1-SNAPSHOT'
}
sourceCompatibility = 1.8
targetCompatibility = 1.8

repositories {
	mavenCentral()
}


dependencies {
	compile('org.springframework.cloud:spring-cloud-starter-zuul')
	compile('org.springframework.boot:spring-boot-starter-web')
	testCompile('org.springframework.boot:spring-boot-starter-test')
}

dependencyManagement {
	imports {
		mavenBom "org.springframework.cloud:spring-cloud-dependencies:Brixton.SR5"
	}
}


eclipse {
	classpath {
		 containers.remove('org.eclipse.jdt.launching.JRE_CONTAINER')
		 containers 'org.eclipse.jdt.launching.JRE_CONTAINER/org.eclipse.jdt.internal.debug.ui.launcher.StandardVMType/JavaSE-1.8'
	}
}

task wrapper(type: Wrapper) {
	gradleVersion = '2.9'
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

	<groupId>com.example</groupId>
	<artifactId>book</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>book</name>
	<description>Demo project for Spring Boot</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.4.0.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
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

`gateway/pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.example</groupId>
  <artifactId>gateway</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <packaging>jar</packaging>

  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.4.0.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
  </parent>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <java.version>1.8</java.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-zuul</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-dependencies</artifactId>
        <version>Brixton.SR5</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>

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

## Set up a microservice
The Book service will be super-simple. Edit `BookApplication.java` to look like this:

`book/src/main/java/hello/BookApplication.java`

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@SpringBootApplication
public class BookApplication {

  @RequestMapping(value = "/available")
  public String available() {
    return "Spring in Action";
  }

  @RequestMapping(value = "/checked-out")
  public String checkedOut() {
    return "Spring Boot in Action";
  }

  public static void main(String[] args) {
    SpringApplication.run(BookApplication.class, args);
  }
}
```

The `BookApplication` class is now a REST controller. `@RestController` marks the class as a controller class, and also ensures that return values from `@RequestMapping` methods in this class will be automatically converted appropriately and written directly to the HTTP response.

Speaking of `@RequestMapping` methods, we’ve added two: `available()` and `checkedOut()`. They handle requests to the paths `/available` and `/checked-out`, each simply returning the `String` name of a book.

Set the application name (`book`) in `src/main/resources/application.properties.`

`book/src/main/resources/application.properties`

```properties
spring.application.name=book

server.port=8090
```

We’re also setting `server.port` here so that it won’t conflict with our edge service when we get both services up and running locally.

## Create an edge service
Spring Cloud Netflix includes an embedded Zuul proxy, which we can enable with the `@EnableZuulProxy` annotation. This will turn the Gateway application into a reverse proxy that forwards relevant calls to other services---such as our Book service.

Open the Gateway application’s `GatewayApplication` class and add the annotation, like so:

`gateway/src/main/java/hello/GatewayApplication.java`

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@EnableZuulProxy
@SpringBootApplication
public class GatewayApplication {

  public static void main(String[] args) {
    SpringApplication.run(GatewayApplication.class, args);
  }

}
```

To forward requests from the Gateway application, we need to tell Zuul the routes that it should watch and the services to which to forward requests to those routes. We specify routes using properties under `zuul.routes`. Each of our microservices can have an entry under `zuul.routes.NAME`, where `NAME` is the application name (as stored in the `spring.application.name` property).

Add the `application.properties` file to a new directory, `src/main/resources`, in the Gateway application. It should look like this:

`gateway/src/main/resources/application.properties`

```properties
zuul.routes.books.url=http://localhost:8090

ribbon.eureka.enabled=false

server.port=8080
```

Spring Cloud Zuul will automatically set the path to the application name. In this sample because we set `zuul.routes.books.url`, so Zuul will proxy requests to `/books` to this URL.

Notice the second-to-last property in our file: Spring Cloud Netflix Zuul uses Netflix’s Ribbon to perform client-side load balancing, and by default, Ribbon would use Netflix Eureka for service discovery. For this simple example, we’re skipping service discovery, so we’ve set ribbon.eureka.enabled to false. Since Ribbon now can’t use Eureka to look up services, we must specify a url for the Book service.

## Add a filter
Now let’s see how we can filter requests through our proxy service. Zuul has four standard filter types:

* **pre** filters are executed before the request is routed,
* **route** filters can handle the actual routing of the request,
* **post** filters are executed after the request has been routed, and
* **error** filters execute if an error occurs in the course of handling the request.

We’re going to write a pre filter. Spring Cloud Netflix picks up, as a filter, any `@Bean` which extends `com.netflix.zuul.ZuulFilter` and is available in the application context. Create a new directory, `src/main/java/hello/filters/pre`, and within it, create the filter file, `SimpleFilter.java`:

`gateway/src/main/java/hello/filters/pre/SimpleFilter.java`

```java
package hello.filters.pre;

import javax.servlet.http.HttpServletRequest;
import com.netflix.zuul.context.RequestContext;
import com.netflix.zuul.ZuulFilter;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class SimpleFilter extends ZuulFilter {

  private static Logger log = LoggerFactory.getLogger(SimpleFilter.class);

  @Override
  public String filterType() {
    return "pre";
  }

  @Override
  public int filterOrder() {
    return 1;
  }

  @Override
  public boolean shouldFilter() {
    return true;
  }

  @Override
  public Object run() {
    RequestContext ctx = RequestContext.getCurrentContext();
    HttpServletRequest request = ctx.getRequest();

    log.info(String.format("%s request to %s", request.getMethod(), request.getRequestURL().toString()));

    return null;
  }

}
```

Filter classes implement four methods:

* `filterType()` returns a `String` that stands for the type of the filter---in this case, `pre`, or it could be `route` for a routing filter.
* `filterOrder()` gives the order in which this filter will be executed, relative to other filters.
* `shouldFilter()` contains the logic that determines when to execute this filter (this particular filter will always be executed).
* `run()` contains the functionality of the filter.

Zuul filters store request and state information in (and share it by means of) the `RequestContext`. We’re using that to get at the `HttpServletRequest`, and then we log the HTTP method and URL of the request before it is sent on its way.

The `GatewayApplication` class is annotated with `@SpringBootApplication`, which is equivalent to (among others) the `@Configuration` annotation that tells Spring to look in a given class for `@Bean` definitions. Add one for our `SimpleFilter` here:

`gateway/src/main/java/hello/GatewayApplication.java`

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;
import org.springframework.context.annotation.Bean;
import hello.filters.pre.SimpleFilter;

@EnableZuulProxy
@SpringBootApplication
public class GatewayApplication {

  public static void main(String[] args) {
    SpringApplication.run(GatewayApplication.class, args);
  }

  @Bean
  public SimpleFilter simpleFilter() {
    return new SimpleFilter();
  }

}
```

## Trying it out
Make sure that both applications are running. In a browser, visit one of the Book application’s endpoints via the Gateway application. If you’ve used the configuration shown in this guide, you can access the Book service directly at `localhost:8090` and via the Gateway service at `localhost:8080/books`.

Visit one of the Book service endpoints, as `localhost:8080/books/available`, and you should see your request’s method logged by the Gateway application before it’s handed on to the Book application:

```
2016-01-19 16:51:14.672  INFO 58807 --- [nio-8080-exec-6] hello.filters.pre.SimpleFilter           : GET request to http://localhost:8080/books/available
2016-01-19 16:51:14.672  INFO 58807 --- [nio-8080-exec-6] o.s.c.n.zuul.filters.ProxyRouteLocator   : Finding route for path: /books/available
```

## Summary
Congratulations! You’ve just used Spring to develop an edge service application that can proxy and filter requests for your microservices.

## See Also
The following guides may also be helpful:

* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)
* [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)