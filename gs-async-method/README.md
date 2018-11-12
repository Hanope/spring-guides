## Creating Asynchronous Methods
This guide walks you through the steps to create asynchronous queries to GitHub. The focus is on the asynchronous part, a feature often used when scaling services.

## What you’ll build
You’ll build a lookup service that queries GitHub user information and retrieves data through GitHub’s API. One approach to scaling services is to run expensive jobs in the background and wait for the results using Java’s `CompletableFuture` interface. Java’s `CompletableFuture` is an evolution from the regular `Future`. It makes it easy to pipeline multiple asynchronous operations merging them into a single asynchronous computation.

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

* [Download](https://github.com/spring-guides/gs-async-method/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-async-method.git`
* cd into `gs-async-method/initial`
* Jump ahead to [Create a representation of a GitHub User](#create-a-representation-of-a-github-user).

**When you’re finished**, you can check your results against the code in `gs-async-method/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-async-method/blob/master/initial/build.gradle).

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
    baseName = 'gs-async-method'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter")
	compile("org.springframework:spring-web")
	compile("com.fasterxml.jackson.core:jackson-databind")
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
    <artifactId>gs-async-method</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.5.RELEASE</version>
    </parent>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
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

## Create a representation of a GitHub User
Before you can create a GitHub lookup service, you need to define a representation for the data you’ll retrieve through GitHub’s API.

To model the user representation, you create a resource representation class. Provide a plain old Java object with fields, constructors, and accessors:

`src/main/java/hello/User.java`

```java
package hello;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@JsonIgnoreProperties(ignoreUnknown=true)
public class User {

    private String name;
    private String blog;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getBlog() {
        return blog;
    }

    public void setBlog(String blog) {
        this.blog = blog;
    }

    @Override
    public String toString() {
        return "User [name=" + name + ", blog=" + blog + "]";
    }

}
```

Spring uses the [Jackson JSON](http://wiki.fasterxml.com/JacksonHome) library to convert GitHub’s JSON response into a `User` object. The `@JsonIgnoreProperties` annotation signals Spring to ignore any attributes not listed in the class. This makes it easy to make REST calls and produce domain objects.

In this guide, we are only grabbing the `name` and the `blog` URL for demonstration purposes.

## Create a GitHub lookup service
Next you need to create a service that queries GitHub to find user information.

`src/main/java/hello/GitHubLookupService.java`

```java
package hello;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.web.client.RestTemplateBuilder;
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

import java.util.concurrent.CompletableFuture;

@Service
public class GitHubLookupService {

    private static final Logger logger = LoggerFactory.getLogger(GitHubLookupService.class);

    private final RestTemplate restTemplate;

    public GitHubLookupService(RestTemplateBuilder restTemplateBuilder) {
        this.restTemplate = restTemplateBuilder.build();
    }

    @Async
    public CompletableFuture<User> findUser(String user) throws InterruptedException {
        logger.info("Looking up " + user);
        String url = String.format("https://api.github.com/users/%s", user);
        User results = restTemplate.getForObject(url, User.class);
        // Artificial delay of 1s for demonstration purposes
        Thread.sleep(1000L);
        return CompletableFuture.completedFuture(results);
    }

}
```

The `GitHubLookupService` class uses Spring’s `RestTemplate` to invoke a remote REST point (api.github.com/users/), and then convert the answer into a `User` object. Spring Boot automatically provides a `RestTemplateBuilder` that customizes the defaults with any auto-configuration bits (i.e. `MessageConverter`).

The class is marked with the `@Service` annotation, making it a candidate for Spring’s component scanning to detect it and add it to the [application context](http://spring.io/understanding/application-context).

The `findUser` method is flagged with Spring’s `@Async` annotation, indicating it will run on a separate thread. The method’s return type is `CompletableFuture<User>` instead of `User`, a requirement for any asynchronous service. This code uses the `completedFuture` method to return a `CompletableFuture` instance which is already completed with result of the GitHub query.

> Creating a local instance of the `GitHubLookupService` class does NOT allow the `findUser` method to run asynchronously. It must be created inside a `@Configuration` class or picked up by `@ComponentScan`.

The timing for GitHub’s API can vary. To demonstrate the benefits later in this guide, an extra delay of one second has been added to this service.

## Make the application executable
To run a sample, you can create an executable jar. Spring’s `@Async` annotation works with web apps, but you don’t need all the extra steps of setting up a web container to see its benefits.

`src/main/java/hello/Application.java`

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.scheduling.annotation.EnableAsync;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;

import java.util.concurrent.Executor;

@SpringBootApplication
@EnableAsync
public class Application {

    public static void main(String[] args) {
        // close the application context to shut down the custom ExecutorService
        SpringApplication.run(Application.class, args).close();
    }

    @Bean
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(2);
        executor.setMaxPoolSize(2);
        executor.setQueueCapacity(500);
        executor.setThreadNamePrefix("GithubLookup-");
        executor.initialize();
        return executor;
    }


}
```

`@SpringBootApplication` is a convenience annotation that adds all of the following:

* `@Configuration` tags the class as a source of bean definitions for the application context.
* `@EnableAutoConfiguration` tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings.
* Normally you would add `@EnableWebMvc` for a Spring MVC app, but Spring Boot adds it automatically when it sees **spring-webmvc** on the classpath. This flags the application as a web application and activates key behaviors such as setting up a `DispatcherServlet`.
* `@ComponentScan` tells Spring to look for other components, configurations, and services in the `hello` package, allowing it to find the controllers.

The `main()` method uses Spring Boot’s `SpringApplication.run()` method to launch an application. Did you notice that there wasn’t a single line of XML? No **web.xml** file either. This web application is 100% pure Java and you didn’t have to deal with configuring any plumbing or infrastructure.

The `@EnableAsync` annotation switches on Spring’s ability to run `@Async` methods in a background thread pool. This class also customizes the used `Executor` by defining a new bean. Here the method is named `taskExecutor` since this is the [specific method name Spring will search for](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/annotation/EnableAsync.html). In our case, we want to limit the number of concurrent threads to 2 and limit the size of the queue to 500. There are [many more things you can tune](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/integration.html#scheduling-task-executor). By default, a `SimpleAsyncTaskExecutor` is used.

There is also a `CommandLineRunner` that injects the `GitHubLookupService` and calls that service 3 times to demonstrate the method is executed asynchronously.

`src/main/java/hello/AppRunner.java`

```java
package hello;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

import java.util.concurrent.CompletableFuture;

@Component
public class AppRunner implements CommandLineRunner {

    private static final Logger logger = LoggerFactory.getLogger(AppRunner.class);

    private final GitHubLookupService gitHubLookupService;

    public AppRunner(GitHubLookupService gitHubLookupService) {
        this.gitHubLookupService = gitHubLookupService;
    }

    @Override
    public void run(String... args) throws Exception {
        // Start the clock
        long start = System.currentTimeMillis();

        // Kick of multiple, asynchronous lookups
        CompletableFuture<User> page1 = gitHubLookupService.findUser("PivotalSoftware");
        CompletableFuture<User> page2 = gitHubLookupService.findUser("CloudFoundry");
        CompletableFuture<User> page3 = gitHubLookupService.findUser("Spring-Projects");

        // Wait until they are all done
        CompletableFuture.allOf(page1,page2,page3).join();

        // Print results, including elapsed time
        logger.info("Elapsed time: " + (System.currentTimeMillis() - start));
        logger.info("--> " + page1.get());
        logger.info("--> " + page2.get());
        logger.info("--> " + page3.get());

    }

}
```

## Build an executable JAR
You can run the application from the command line with Gradle or Maven. Or you can build a single executable JAR file that contains all the necessary dependencies, classes, and resources, and run that. This makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.

If you are using Gradle, you can run the application using `./gradlew bootRun`. Or you can build the JAR file using `./gradlew build`. Then you can run the JAR file:

```
java -jar build/libs/gs-async-method-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-async-method-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/) instead.

Logging output is displayed, showing each query to GitHub. With the help of `allOf` factory method we create an array of `CompletableFutures` on which by calling the `join` method makes it possible to wait for the completion of all of the `CompletableFutures`.

```
2016-09-01 10:25:21.295  INFO 17893 --- [ GithubLookup-2] hello.GitHubLookupService                : Looking up CloudFoundry
2016-09-01 10:25:21.295  INFO 17893 --- [ GithubLookup-1] hello.GitHubLookupService                : Looking up PivotalSoftware
2016-09-01 10:25:23.142  INFO 17893 --- [ GithubLookup-1] hello.GitHubLookupService                : Looking up Spring-Projects
2016-09-01 10:25:24.281  INFO 17893 --- [           main] hello.AppRunner                          : Elapsed time: 2994
2016-09-01 10:25:24.282  INFO 17893 --- [           main] hello.AppRunner                          : --> User [name=Pivotal Software, Inc., blog=http://pivotal.io]
2016-09-01 10:25:24.282  INFO 17893 --- [           main] hello.AppRunner                          : --> User [name=Cloud Foundry, blog=https://www.cloudfoundry.org/]
2016-09-01 10:25:24.282  INFO 17893 --- [           main] hello.AppRunner                          : --> User [name=Spring, blog=http://spring.io/projects]
```

Note that the first two calls happen in separate threads (`GithubLookup-2`, `GithubLookup-1`) and the third one is parked until one of the two threads became available. To compare how long this takes without the asynchronous feature, try commenting out the `@Async` annotation and run the service again. The total elapsed time should increase noticeably because each query takes at least a second. You can also tune the `Executor` to increase the `corePoolSize` attribute for instance.

Essentially, the longer the task takes and the more tasks are invoked simultaneously, the more benefit you will see with making things asynchronous. The trade off is handling the `CompletableFuture` interface. It adds a layer of indirection because you are no longer dealing directly with the results.

## Summary
Congratulations! You’ve just developed an asynchronous service that lets you scale multiple calls at once.

## See Also
The following guides may also be helpful:

* [Serving Web Content with Spring MVC](https://spring.io/guides/gs/serving-web-content/)
* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)