## Creating a Multi Module Project
This guide shows you how to create a multi-module project with Spring Boot, in which there is a library jar and a main application that uses the library. You could also use it just to see how to build a library on its own (i.e. a jar file that is not an application).

## What you’ll build
You’ll setup a simple library jar that exposes a service for simple Hello World messages, and then include the service in a web application that uses the library as a dependency. You can use the guide to learn how to do the same thing with Maven and with Gradle, but for a real project you would probably choose one or the other.

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

To **start from scratch**, move on to [Create a Root Project](#create-a-root-project).

To **skip the basics**, do the following:

* [Download](https://github.com/spring-guides/draft-gs-multi-module/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/draft-gs-multi-module.git`
* cd into `draft-gs-multi-module/initial`
* Jump ahead to [Create a Library Project](#create-a-library-project).

**When you’re finished**, you can check your results against the code in `draft-gs-multi-module/complete`.

First you set up a basic build script. You can use any build system you like when building apps with Spring, but the code you need to work with [Gradle](http://gradle.org/) and [Maven](https://maven.apache.org/) is included here. If you’re not familiar with either, refer to [Building Java Projects with Gradle](http://spring.io/guides/gs/gradle) or [Building Java Projects with Maven](http://spring.io/guides/gs/maven).

## Create a Root Project

### Create the directory structure
In a project directory of your choosing, create the following subdirectory structure; for example, with `mkdir library application` on *nix systems:

```
└── library
└── application
```

In the root of the project you will need to set up a build system, and this guide shows you how to use Maven or Gradle.

## Gradle Configuration for Multi Module Project
We recommend using the [Gradle wrapper](https://docs.gradle.org/current/userguide/gradle_wrapper.html). So install the wrapper by copying it from an existing project, or by executing Gradle with the `wrapper` task (follow instructions in the Gradle documentation). That should get you a top level directory that looks like this:

```
└── library
└── application
└── gradlew
└── gradlew.bat
└── gradle
    └── wrapper
        └── gradle-wrapper.properties
        └── gradle-wrapper.jar
```

Windows (non-Cygwin) users execute the `gradlew.bat` script to build the project; everyone else uses the `gradlew` script. Then add a `settings.gradle` to the root directory to drive the build at the top level:

`settings.gradle`

```gradle
rootProject.name = 'gs-multi-module'

include 'library'
include 'application'
```

## Maven Configuration for Multi Module Project
We recommend using the [Maven wrapper](https://github.com/takari/maven-wrapper). So install the wrapper by copying it from an existing project, or by executing Maven with the `io.takari:maven:wrapper` goal (follow instructions in the wrapper documentation). That should get you a top level directory that looks like this:

```
└── library
└── application
└── mvnw
└── mvnw.cmd
└── .mvn
    └── wrapper
        └── maven-wrapper.properties
        └── maven-wrapper.jar
```

Windows (non-Cygwin) users execute the `mvnw.cmd` script to build the project; everyone else uses the `mvnw` script. Then add a `pom.xml` to the root directory to drive the build at the top level:

`pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-multi-module</artifactId>
    <version>0.1.0</version>
    <packaging>pom</packaging>

    <modules>
        <module>library</module>
        <module>application</module>
    </modules>

</project>
```

## Create a Library Project

### Create the directory structure
In a the "library" directory, create the following subdirectory structure; for example, with `mkdir -p src/main/java/hello/service` on *nix systems:

```
└── src
    └── main
        └── java
            └── hello
                └── service
```

Now we need to configure a build tool (Maven or Gradle). In both cases note that the Spring Boot plugin is **not** used in the library project at all. The main function of the plugin is to create an executable "über-jar" which we don’t need (and don’t want) for a library. To get you started quickly, here is the complete configuration:

## Gradle Configuration for Library

`library/build.gradle`

```gradle
buildscript {
    repositories { mavenCentral() }
}

plugins { id "io.spring.dependency-management" version "1.0.5.RELEASE" }

ext { springBootVersion = '2.0.5.RELEASE' }

apply plugin: 'java'
apply plugin: 'eclipse'

jar {
    baseName = 'gs-multi-module-library'
    version = '0.0.1-SNAPSHOT'
}
sourceCompatibility = 1.8

repositories { mavenCentral() }

dependencies {
    compile('org.springframework.boot:spring-boot-starter')
    testCompile('org.springframework.boot:spring-boot-starter-test')
}

dependencyManagement {
    imports { mavenBom("org.springframework.boot:spring-boot-dependencies:${springBootVersion}") }
}
```

Although the Spring Boot Gradle plugin is not being used, you do want to take advantage of Spring Boot dependency management, so that is configured using the `dependency-management` plugin and the `mavenBom` from Spring Boot.

## Maven Configuration for Library

`library/pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>gs-multi-module-library</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.5.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

</project>
```

Although the Spring Boot Maven plugin is not being used, you do want to take advantage of Spring Boot dependency management, so that is configured using the `spring-boot-starter-parent` from Spring Boot as a parent project. An alternative would be to import the dependency management as a Bill of Materials [(BOM)](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#Importing_Dependencies) in the `<dependencyManagement/>` section of the POM.

## Create a Service Component
The library will provide a `MyService` class that can be used by applications that depend on the library:

`library/src/main/java/hello/service/MyService.java`

```java
package hello.service;

import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.stereotype.Service;

@Service
@EnableConfigurationProperties(ServiceProperties.class)
public class MyService {

    private final ServiceProperties serviceProperties;

    public MyService(ServiceProperties serviceProperties) {
        this.serviceProperties = serviceProperties;
    }

    public String message() {
        return this.serviceProperties.getMessage();
    }
}
```

To make it configurable in a standard Spring Boot idiom (with `application.properties`) you can also add a `@ConfigurationProperties` class:

`library/src/main/java/hello/service/ServiceProperties.java`

```java
package hello.service;

import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties("service")
public class ServiceProperties {

    /**
     * A message for the service.
     */
    private String message;

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
}
```

You don’t have to do it this way: a library might just provide pure Java APIs, and no Spring features. In that case the application that consumes the library would need to provide the configuration itself.

## Testing the Service Component
You will want to write unit tests for your library components. If you are providing re-usable Spring configuration as part of the library, you might also want to write an integration test, to make sure that the configuration works. To do that you can use JUnit and the `@SpringBootTest` annotation. For example:

`library/src/test/java/hello/service/MyServiceTest.java`

```java
package hello.service;

import static org.assertj.core.api.Assertions.*;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest("service.message=Hello")
public class MyServiceTest {

    @Autowired
    private MyService myService;

    @Test
    public void contextLoads() {
        assertThat(myService.message()).isNotNull();
    }

    @SpringBootApplication
    static class TestConfiguration {
    }

}
```

> In the sample above we have configured the `service.message` for the test using the default attribute of the `@SpringBootTest` annotation. It is not advisable to put `application.properties` in a library because there might be a clash at runtime in the application that uses it (only one `application.properties` is ever loaded from the classpath). You could put `application.properties` in the test classpath, but not include it in the jar, for instance by placing it in `src/test/resources`.

## Create an Application Project

### Create the directory structure

In a the "application" directory, create the following subdirectory structure; for example, with `mkdir -p src/main/java/hello/app` on *nix systems:

```
└── src
    └── main
        └── java
            └── hello
                └── app
```

Do not use the same package as the library (or a parent of the library package) unless you want to include all Spring components in the library by `@ComponentScan` by default in the application. Now comes the build configuration:

## Gradle Configuration for Application

`application/build.gradle`

```gradle
buildscript {
    ext { springBootVersion = '2.0.5.RELEASE' }
    repositories { mavenCentral() }
    dependencies { classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}") }
}

plugins {
    id "io.spring.dependency-management" version "1.0.5.RELEASE"
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

bootJar {
    baseName = 'gs-multi-module-application'
    version = '0.0.1-SNAPSHOT'
}
sourceCompatibility = 1.8

repositories { mavenCentral() }

dependencies {
    compile('org.springframework.boot:spring-boot-starter-actuator')
    compile('org.springframework.boot:spring-boot-starter-web')
    compile project(':library')
    testCompile('org.springframework.boot:spring-boot-starter-test')
}
```

The [Spring Boot gradle plugin](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html) provides many convenient features:

* It collects all the jars on the classpath and builds a single, runnable "über-jar", which makes it more convenient to execute and transport your service.
* It searches for the `public static void main()` method to flag as a runnable class.
* It provides a built-in dependency resolver that sets the version number to match [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml). You can override any version you wish, but it will default to Boot’s chosen set of versions.

## Maven Configuration for Application

`application/pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>gs-multi-module-application</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.5.RELEASE</version>
        <relativePath /> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>com.example</groupId>
            <artifactId>gs-multi-module-library</artifactId>
            <version>${project.version}</version>
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

The [Spring Boot gradle plugin](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html) provides many convenient features:

* It collects all the jars on the classpath and builds a single, runnable "über-jar", which makes it more convenient to execute and transport your service.
* It searches for the `public static void main()` method to flag as a runnable class.
* It provides a built-in dependency resolver that sets the version number to match [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml). You can override any version you wish, but it will default to Boot’s chosen set of versions.

## Write the Application
The main class in the application can be a `@RestController` that uses the `Service` from the library to render a message. For example:

`application/src/main/java/hello/app/DemoApplication.java`

```java
package hello.app;

import hello.service.MyService;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication(scanBasePackages = "hello")
@RestController
public class DemoApplication {

    private final MyService myService;

    public DemoApplication(MyService myService) {
        this.myService = myService;
    }

    @GetMapping("/")
    public String home() {
        return myService.message();
    }

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

`@SpringBootApplication` is a convenience annotation that adds all of the following:

* `@Configuration` tags the class as a source of bean definitions for the application context.
* `@EnableAutoConfiguration` tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings.
* Normally you would add `@EnableWebMvc` for a Spring MVC app, but Spring Boot adds it automatically when it sees **spring-webmvc** on the classpath. This flags the application as a web application and activates key behaviors such as setting up a `DispatcherServlet`.
* `@ComponentScan` tells Spring to look for other components, configurations, and services in the hello package, allowing it to find the controllers.

The `main()` method uses Spring Boot’s `SpringApplication.run()` method to launch an application. Did you notice that there wasn’t a single line of XML? No **web.xml** file either. This web application is 100% pure Java and you didn’t have to deal with configuring any plumbing or infrastructure.

Because `DemoApplication` is inside a different package (`hello.app`) than `MyService` (`hello.service`), `@SpringBootApplication` won’t detect it initially. There are different ways to allow `MyService to be picked up:

* Import it directly with `@Import(MyService.class)`.
* Fetch everything from it’s package using `@SpringBootApplication(scanBasePackageClasses={…​})`.
* The method this guide uses—​specifying the parent package by name, `hello`.

## Create the `application.properties` file

We need to provide a message for the service in the library via `application.properties`. In the sources folder, you just need to create a file `src/main/resources/application.properties`:

```properties
service.message=Hello World
```

## Test the application
Test the end-to-end result by starting the application. You can start the application in your IDE quite easily, or follow the instructions below to use the command line. Visit the client app in the browser, http://localhost:8080/. There, you should see the String `Hello World` reflected in the response.

### Gradle Command Line to Run Application
Build the library first and then run the application. From the top level:

```bash
$ ./gradlew build && ./gradlew :application:bootRun
```

### Maven Command Line to Run Application
Build the library first and then run the application. From the top level:

```bash
$ ./mvnw install && ./mvnw spring-boot:run -pl application
```

## Summary
Congratulations! You’ve just used Spring Boot to create a re-usable library, and then used it to build an application.

## See Also
The following guides may also be helpful:

* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)
* [Centralized Configuration](https://spring.io/guides/gs/centralized-configuration/)