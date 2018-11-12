## Creating API Documentation with Restdocs
This guide walks you through the process of generating documention for the HTTP endpoints in a Spring application.

## What you’ll build
You’ll build a simple Spring application with some HTTP endpoints exposing an API. You will test just the web layer using JUnit and Spring’s `MockMvc`, and then you will use the same tests to generate documentation for the API using [Spring REST Docs](https://projects.spring.io/spring-restdocs).

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

* [Download](https://github.com/spring-guides/gs-testing-restdocs/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-testing-restdocs.git`
* cd into `gs-testing-restdocs/initial`
* Jump ahead to [Create a simple application](#create-a-simple-application).

**When you’re finished**, you can check your results against the code in `gs-testing-restdocs/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-testing-restdocs/blob/master/initial/build.gradle).

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

plugins {
    id "io.spring.dependency-management" version "1.0.5.RELEASE"
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

bootJar {
    baseName = 'gs-testing-restdocs'
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
    <artifactId>gs-testing-restdocs</artifactId>
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

import java.util.Collections;
import java.util.Map;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HomeController {

    @GetMapping("/")
    public Map<String, Object> greeting() {
        return Collections.singletonMap("message", "Hello World");
    }

}
```

## Make the application executable
Create a "main" class that you can use to launch the application:

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
java -jar build/libs/gs-testing-restdocs-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-testing-restdocs-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/) instead.

Logging output is displayed. The service should be up and running within a few seconds.

## Test the application
Now that the application is running, you can test it. If it is running you can load the home page on http://localhost:8080. But to give yourself more confidence that the application is working when you make changes, you want to automate the testing. You also want to publish documentation for the HTTP endpoint and you can generate the dynamic parts of that as part of the tests using Spring REST Docs.

The first thing you can do is write a simple sanity check test that will fail if the application context cannot start. First add Spring Test and Spring REST Docs as dependencies to your project, in the test scope. If you are using Maven:

`pom.xml`

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.restdocs</groupId>
            <artifactId>spring-restdocs-mockmvc</artifactId>
            <scope>test</scope>
        </dependency>
```

or if you are using Gradle:

`build.gradle`

```gradle
    testCompile("org.springframework.boot:spring-boot-starter-test")
    testCompile("org.springframework.restdocs:spring-restdocs-mockmvc")
```

> You have included the "mockmvc" flavour of REST Docs which uses Spring MockMvc to capture the HTTP content. If your own app is not using Spring MVC there is also a "restassured" flavour which works with full stack integration tests.

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

You can run this test in your IDE or on the command line (`mvn test` or `gradle test`) and it should pass.

It’s nice to have a sanity check like that, but you should also write some tests that assert the behaviour of our application. A useful approach is to test only the MVC layer, where Spring handles the incoming HTTP request and hands it off to your controller. To do that you can use Spring’s `MockMvc`, and ask for that to be injected for us by using the `@WebMvcTest` annotation on the test case:

`src/test/java/hello/WebLayerTest.java`

```java
@RunWith(SpringRunner.class)
@WebMvcTest(HomeController.class)
public class WebLayerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    public void shouldReturnDefaultMessage() throws Exception {
        this.mockMvc.perform(get("/"))
            .andExpect(status().isOk())
            .andExpect(content().string(containsString("Hello World")));
    }
}
```

## Generate Snippets for Documentation
The test above makes (mock) HTTP requests and asserts the responses. The HTTP API that you have created has dynamic content (at least in principle), so it would be really nice to be able to spy on the tests and syphon off the HTTP requests for use in the documentation. Spring REST Docs allows you to do this by generating "snippets". You can get this working really easily, just by adding an annotation to your test and an extra "assertion". Here’s the complete test:

`src/test/java/hello/WebLayerTest.java`

```java
package hello;

import org.junit.Test;
import org.junit.runner.RunWith;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.restdocs.AutoConfigureRestDocs;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;

import static org.hamcrest.Matchers.containsString;
import static org.springframework.restdocs.mockmvc.MockMvcRestDocumentation.document;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@RunWith(SpringRunner.class)
@WebMvcTest(HomeController.class)
@AutoConfigureRestDocs(outputDir = "target/snippets")
public class WebLayerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    public void shouldReturnDefaultMessage() throws Exception {
        this.mockMvc.perform(get("/")).andDo(print()).andExpect(status().isOk())
                .andExpect(content().string(containsString("Hello World")))
                .andDo(document("home"));
    }
}
```

The new annotation is `@AutoConfigureRestDocs` (from Spring Boot), which takes as an argument a directory location for the generated snippets. And the new assertion is `MockMvcRestDocumentation.document`, which takes as an argument a string identifier for the snippets.

> Gradle users might prefer to use `build` instead of `target` as an output directory, but really it doesn’t matter. It’s up to you to choose.

Run this test and then look in `target/snippets`. You should find a directory called `home` (the identifier) containing [Asciidoctor](http://asciidoctor.org/) snippets:

```
└── target
    └── snippets
        └── home
            └── httpie-request.adoc
            └── curl-request.adoc
            └── http-request.adoc
            └── http-response.adoc
```

The default snippets are in Asciidoctor format, for the HTTP request and response, plus command line examples for `curl` and `httpie` (two common and popular command line HTTP clients).

You can create additional snippets by adding arguments to the `document()` assertion in the test. For example, you can document each of the fields in a JSON response using the `PayloadDocumentation.responseFields()` snippet:

`src/test/java/hello/WebLayerTest.java`

```java
this.mockMvc.perform(get("/"))
    ...
    .andDo(document("home", responseFields(
        fieldWithPath("message").description("The welcome message for the user.")
    ));
```

If you try this and execute the test you should find an additional snippet file called "response-fields.adoc", containing a table of field anames and descriptions. If you omit a field or get its name wrong the test will fail - this is the power of REST Docs.

> You can create custom snippets, and also change the format of the snippets and customize things like the hostname. Check the documentation of [Spring REST Docs](https://docs.spring.io/spring-restdocs/docs/current/reference/html5/) for more detail.

## Using the Snippets
To use the generated snippets you would want to have some Asciidoctor content in the project, and then include the snippets at build time. To see this working, create a new file `src/main/asciidoc/index.adoc` and include the snippets as desired. For example

`src/main/asciidoc/index.adoc`

```adoc
= Getting Started With Spring REST Docs

This is an example output for a service running at http://localhost:8080:

.request
include::{snippets}/home/http-request.adoc[]

.response
include::{snippets}/home/http-response.adoc[]

As you can see the format is very simple, and in fact you always get the same message.
```

The main feature of this is that it includes 2 of the snippets, using the Asciidoctor `include` directive (the colons and the trailing brackets tell the parser to do something special on those lines). Notice that the path to the included snippets is expressed as a placeholder - an "attribute" in Asciidoctor - called `{snippets}`. The only other markup in this simple case is the "=" at the top, which is a level 1 section heading, and the "." before the captions ("request" and "response") on the snippets.

Then in the build configuration you would need to process this source file into your chosen documentation format. For example using Maven to generate HTML (`target/generated-docs` is generated when you do `mvnw package`):

`pom.xml`

```xml
<plugin>
    <groupId>org.asciidoctor</groupId>
    <artifactId>asciidoctor-maven-plugin</artifactId>
    <executions>
        <execution>
            <id>generate-docs</id>
            <phase>prepare-package</phase>
            <goals>
                <goal>process-asciidoc</goal>
            </goals>
            <configuration>
                <sourceDocumentName>index.adoc</sourceDocumentName>
                <backend>html</backend>
                <attributes>
                    <snippets>${project.build.directory}/snippets</snippets>
                </attributes>
            </configuration>
        </execution>
    </executions>
</plugin>
```

or if you are using Gradle (`build/asciidoc` is generated when you do `gradlew asciidoctor`):

`build.gradle`

```gradle
buildscript {
    repositories {
        ...
        jcenter()
    }
    ...
    dependencies {
        ...
        classpath("org.asciidoctor:asciidoctor-gradle-plugin:1.5.3")
    }
}

...
apply plugin: 'org.asciidoctor.convert'

asciidoctor {
    sourceDir 'src/main/asciidoc'
    attributes \
      'snippets': file('target/snippets')
}
```

> The Asciidoctor Gradle plugin is not in Maven Central, so you also have to add `jcenter()` to the buildscipt dependencies in Gradle.

> The default location for asciidoctor sources in Gradle is `src/doc/asciidoc`. We only need to set the `sourceDir` because we changed the location to match the default for Maven.

## Summary
Congratulations! You’ve just developed a Spring application and documented it using Spring Restdocs. You could publish the HTML documentation you created to a static website, or package it up and serve it from the application itself. Your documentation will always be up to date, and tests will fail your build if it is not.

## See Also
The following guides may also be helpful:

* [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)
* [Consuming a RESTful Web Service](https://spring.io/guides/gs/consuming-rest/)
* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)