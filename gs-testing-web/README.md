## Testing the Web Layer
This guide walks you through the process of creating a Spring application.

## What you’ll build
You’ll build a simple Spring application and test it with JUnit. You probably already know how to write and run unit tests of the individual classes in your application, so for this guide we will concentrate on using Spring Test and Spring Boot features to test the interactions between Spring and your code. You will start with a simple test that the application context loads successfully, and continue on to test just the web layer using Spring’s `MockMvc`.

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

* [Download](https://github.com/spring-guides/gs-testing-web/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-testing-web.git`
* cd into `gs-testing-web/initial`
* Jump ahead to [Create a simple application](#create-a-simple-application).

**When you’re finished**, you can check your results against the code in `gs-testing-web/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-testing-web/blob/master/initial/build.gradle).

`build.gradle`

```gradle
buildscript {
    repositories { mavenCentral() }
}

plugins { id "io.spring.dependency-management" version "1.0.4.RELEASE" }

ext { springBootVersion = '2.0.5.RELEASE' }

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'

bootJar {
    baseName = 'gs-testing-web'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    testCompile("org.springframework.boot:spring-boot-starter-test")
}

task wrapper(type: Wrapper) {
    gradleVersion = '4.6'
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
    <artifactId>gs-testing-web</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.5.RELEASE</version>
    </parent>

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

## Create a simple application
Create a new controller for your Spring application:

`src/main/java/hello/HomeController.java`

```java
package hello;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HomeController {

    @RequestMapping("/")
    public @ResponseBody String greeting() {
        return "Hello World";
    }

}
```

> The above example does not specify `GET` vs. `PUT`, `POST`, and so forth, because `@RequestMapping` maps all HTTP operations by default. Use `@RequestMapping(method=GET)` to narrow this mapping.

## Make the application executable
Although it is possible to package this service as a traditional [WAR](http://spring.io/understanding/WAR) file for deployment to an external application server, the simpler approach demonstrated below creates a standalone application. You package everything in a single, executable JAR file, driven by a good old Java `main()` method. Along the way, you use Spring’s support for embedding the [Tomcat](http://spring.io/understanding/Tomcat) servlet container as the HTTP runtime, instead of deploying to an external instance.

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
* `@ComponentScan` tells Spring to look for other components, configurations, and services in the the `hello` package, allowing it to find the `HelloController`.

The `main()` method uses Spring Boot’s `SpringApplication.run()` method to launch an application. Did you notice that there wasn’t a single line of XML? No **web.xml** file either. This web application is 100% pure Java and you didn’t have to deal with configuring any plumbing or infrastructure.

## Build an executable JAR
You can run the application from the command line with Gradle or Maven. Or you can build a single executable JAR file that contains all the necessary dependencies, classes, and resources, and run that. This makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.

If you are using Gradle, you can run the application using `./gradlew bootRun`. Or you can build the JAR file using `./gradlew build`. Then you can run the JAR file:

```
java -jar build/libs/gs-testing-web-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-testing-web-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/) instead.

Logging output is displayed. The service should be up and running within a few seconds.

## Test the application
Now that the application is running, you can test it. If it is running you can load the home page on http://localhost:8080. But to give yourself more confidence that the application is working when you make changes, you want to automate the testing.

The first thing you can do is write a simple sanity check test that will fail if the application context cannot start. First add Spring Test as a dependency to your pom.xml, in the test scope. If you are using Maven:

`pom.xml`

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

or if you are using Gradle:

`build.gradle`

```gradle
testCompile("org.springframework.boot:spring-boot-starter-test")
```

Then create a test case with the `@RunWith` and `@SpringBootTest` annotations and an empty test method:

`src/test/java/hello/ApplicationTest.java`

```java
package hello;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
public class ApplicationTest {

    @Test
    public void contextLoads() throws Exception {
    }

}
```

The `@SpringBootTest` annotation tells Spring Boot to go and look for a main configuration class (one with `@SpringBootApplication` for instance), and use that to start a Spring application context. You can run this test in your IDE or on the command line (`mvn test` or `gradle test`) and it should pass. To convince yourself that the context is creating your controller you could add an assertion:

`src/test/java/hello/SmokeTest.java`

```java
package hello;

import static org.assertj.core.api.Assertions.assertThat;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
public class SmokeTest {

    @Autowired
    private HomeController controller;

    @Test
    public void contexLoads() throws Exception {
        assertThat(controller).isNotNull();
    }
}
```

The `@Autowired` annotation is interpreted by the Spring and the controller is injected before the test methods are run. We are using [AssertJ](https://joel-costigliola.github.io/assertj/) (`assertThat()` etc.) to express the test assertions.

> A nice feature of the Spring Test support is that the application context is cached in between tests, so if you have multiple methods in a test case, or multiple test cases with the same configuration, they only incur the cost of starting the application once. You can control the cache using the `@DirtiesContext` annotation.

It’s nice to have a sanity check like that, but we should also write some tests that assert the behaviour of our application. To do that we could start the application up and listen for a connection like it would do in production, and then send an HTTP request and assert the response.

`src/test/java/hello/HttpRequestTest.java`

```java
package hello;

import org.junit.Test;
import org.junit.runner.RunWith;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.context.SpringBootTest.WebEnvironment;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.boot.web.server.LocalServerPort;
import org.springframework.test.context.junit4.SpringRunner;

import static org.assertj.core.api.Assertions.assertThat;

@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
public class HttpRequestTest {

    @LocalServerPort
    private int port;

    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    public void greetingShouldReturnDefaultMessage() throws Exception {
        assertThat(this.restTemplate.getForObject("http://localhost:" + port + "/",
                String.class)).contains("Hello World");
    }
}
```

Note the use of `webEnvironment=RANDOM_PORT` to start the server with a random port (useful to avoid conflicts in test environments), and the injection of the port with `@LocalServerPort`. Also note that Spring Boot has provided a `TestRestTemplate` for you automatically, and all you have to do is `@Autowired` it.

Another useful approach is to not start the server at all, but test only the layer below that, where Spring handles the incoming HTTP request and hands it off to your controller. That way, almost the full stack is used, and your code will be called exactly the same way as if it was processing a real HTTP request, but without the cost of starting the server. To do that we will use Spring’s `MockMvc`, and we can ask for that to be injected for us by using the `@AutoConfigureMockMvc` annotation on the test case:

`src/test/java/hello/ApplicationTest.java`

```java
package hello;

import static org.hamcrest.Matchers.containsString;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;

@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
public class ApplicationTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    public void shouldReturnDefaultMessage() throws Exception {
        this.mockMvc.perform(get("/")).andDo(print()).andExpect(status().isOk())
                .andExpect(content().string(containsString("Hello World")));
    }
}
```

In this test, the full Spring application context is started, but without the server. We can narrow down the tests to just the web layer by using `@WebMvcTest`:

`src/test/java/hello/WebLayerTest.java`

```java
@RunWith(SpringRunner.class)
@WebMvcTest
public class WebLayerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    public void shouldReturnDefaultMessage() throws Exception {
        this.mockMvc.perform(get("/")).andDo(print()).andExpect(status().isOk())
                .andExpect(content().string(containsString("Hello World")));
    }
}
```

The test assertion is the same as in the previous case, but here Spring Boot is only instantiating the web layer, not the whole context. In an application with multiple controllers you can even ask for just one to be instantiated, using, for example `@WebMvcTest(HomeController.class)`

So far, our `HomeController` is very simple and has no dependencies. We could make it more realistic by introducing an extra component to store the greeting. E.g. in a new controller:

`src/main/java/hello/GreetingController.java`

```java
package hello;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;


@Controller
public class GreetingController {

    private final GreetingService service;

    public GreetingController(GreetingService service) {
        this.service = service;
    }

    @RequestMapping("/greeting")
    public @ResponseBody String greeting() {
        return service.greet();
    }

}
```

and then

`src/main/java/hello/GreetingService.java`

```java
package hello;

import org.springframework.stereotype.Service;

@Service
public class GreetingService {
    public String greet() {
        return "Hello World";
    }
}
```

The service dependency will be automatically injected by Spring into the controller (because of the constructor signature). To test this controller with `@WebMvcTest` you can do this

`src/test/java/hello/WebMockTest.java`

```java
package hello;

import static org.hamcrest.Matchers.containsString;
import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;

@RunWith(SpringRunner.class)
@WebMvcTest(GreetingController.class)
public class WebMockTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private GreetingService service;

    @Test
    public void greetingShouldReturnMessageFromService() throws Exception {
        when(service.greet()).thenReturn("Hello Mock");
        this.mockMvc.perform(get("/greeting")).andDo(print()).andExpect(status().isOk())
                .andExpect(content().string(containsString("Hello Mock")));
    }
}
```

We use `@MockBean` to create and inject a mock for the `GreetingService` (if you don’t do this the application context cannot start), and we set its expectations using `Mockito`.

## Summary
Congratulations! You’ve just developed a Spring application and tested it with JUnit and Spring `MockMvc` using Spring Boot to isolate the web layer and load a special application context.

## See Also
The following guides may also be helpful:

* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)
* [Serving Web Content with Spring MVC](https://spring.io/guides/gs/serving-web-content/)