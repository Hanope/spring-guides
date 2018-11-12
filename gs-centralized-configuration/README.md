## Centralized Configuration
This guide walks you through the process of standing up, and consuming configuration from, the [Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/spring-cloud-config.html)

## What you’ll build
You’ll setup a [Config Server](https://cloud.spring.io/spring-cloud-config/spring-cloud-config.html) and then build a client that consumes the configuration on startup and then refreshes the configuration without restarting the client.

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

* [Download](https://github.com/spring-guides/gs-centralized-configuration/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-centralized-configuration.git`
* cd into `gs-centralized-configuration/initial`
* Jump ahead to [Create a resource representation class](#create-a-resource-representation-class).

**When you’re finished**, you can check your results against the code in `gs-centralized-configuration/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-centralized-configuration/blob/master/initial/build.gradle).

`build.gradle`

```gradle
buildscript {
	ext {
		springBootVersion = '2.0.5.RELEASE'
	}
	repositories {
		mavenCentral()
	}
	dependencies {
		classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
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
	baseName = 'configuration-service'
	version = '0.0.1-SNAPSHOT'
}
sourceCompatibility = 1.8
targetCompatibility = 1.8

repositories {
	mavenCentral()
}

dependencies {
	compile('org.springframework.cloud:spring-cloud-config-server')
	testCompile('org.springframework.boot:spring-boot-starter-test')
}

dependencyManagement {
	imports {
		mavenBom "org.springframework.cloud:spring-cloud-dependencies:Finchley.SR1"
	}
}

repositories {
    maven {
        url 'https://repo.spring.io/libs-milestone'
    }
    maven {
        url 'https://repo.spring.io/libs-snapshot'
    }
}

eclipse {
	classpath {
		 containers.remove('org.eclipse.jdt.launching.JRE_CONTAINER')
		 containers 'org.eclipse.jdt.launching.JRE_CONTAINER/org.eclipse.jdt.internal.debug.ui.launcher.StandardVMType/JavaSE-1.8'
	}
}
```

`configuration-client/build.gradle`

```gradle
buildscript {
	ext {
		springBootVersion = '2.0.5.RELEASE'
	}
	repositories {
		mavenCentral()
	}
	dependencies {
		classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
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
	baseName = 'configuration-client'
	version = '0.0.1-SNAPSHOT'
}
sourceCompatibility = 1.8
targetCompatibility = 1.8

repositories {
	mavenCentral()
}

dependencies {
	compile('org.springframework.cloud:spring-cloud-starter-config')
	compile('org.springframework.boot:spring-boot-starter-actuator')
	compile('org.springframework.boot:spring-boot-starter-web')
}

dependencyManagement {
	imports {
		mavenBom "org.springframework.cloud:spring-cloud-dependencies:Finchley.SR1"
	}
}

repositories {
    maven {
        url 'https://repo.spring.io/libs-milestone'
    }
    maven {
        url 'https://repo.spring.io/libs-snapshot'
    }
}

eclipse {
	classpath {
		 containers.remove('org.eclipse.jdt.launching.JRE_CONTAINER')
		 containers 'org.eclipse.jdt.launching.JRE_CONTAINER/org.eclipse.jdt.internal.debug.ui.launcher.StandardVMType/JavaSE-1.8'
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
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>configuration-service</artifactId>
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
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-config-server</artifactId>
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
				<version>Finchley.SR1</version>
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

	<repositories>
	    <repository>
	        <id>spring-milestones</id>
	        <name>Spring Milestones</name>
	        <url>https://repo.spring.io/libs-milestone</url>
	        <snapshots>
	            <enabled>false</enabled>
	        </snapshots>
	    </repository>
	</repositories>

</project>
```

`configuration-client/pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>configuration-client</artifactId>
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
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-config</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
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
				<version>Finchley.SR1</version>
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

	<repositories>
	    <repository>
	        <id>spring-milestones</id>
	        <name>Spring Milestones</name>
	        <url>https://repo.spring.io/libs-milestone</url>
	        <snapshots>
	            <enabled>false</enabled>
	        </snapshots>
	    </repository>
	    <repository>
	        <id>spring-snapshots</id>
	        <name>Spring Snapshots</name>
	        <url>https://repo.spring.io/libs-snapshot</url>
	        <snapshots>
	            <enabled>true</enabled>
	        </snapshots>
	    </repository>
	</repositories>

</project>
```

The [Spring Boot Maven plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin) provides many convenient features:

* It collects all the jars on the classpath and builds a single, runnable "über-jar", which makes it more convenient to execute and transport your service.
* It searches for the `public static void main()` method to flag as a runnable class.
* It provides a built-in dependency resolver that sets the version number to match [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml). You can override any version you wish, but it will default to Boot’s chosen set of versions.

## Build with your IDE
* Read how to import this guide straight into [Spring Tool Suite](http://spring.io/guides/gs/sts/).
* Read how to work with this guide in [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea).

## Stand up a Config Server
You’ll first need a Config Service to act as a sort of intermediary between your Spring applications and a typically version-controlled repository of configuration files. You can use Spring Cloud’s `@EnableConfigServer` to standup a config server that other applications can talk to. This is a regular Spring Boot application with one annotation added to enable the config server.

`configuration-service/src/main/java/hello/ConfigServiceApplication.java`

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;

@EnableConfigServer
@SpringBootApplication
public class ConfigServiceApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConfigServiceApplication.class, args);
    }
}
```

The Config Server needs to know which repository to manage. There are several choices here, but we’ll use a Git-based filesystem repository. You could as easily point the Config Server to a Github or GitLab repository, as well. On the file system, create a new directory and `git init` it. Then add a file called `a-bootiful-client.properties` to the Git repository. Make sure to also `git commit` it, as well. Later, you will connect to the Config Server with a Spring Boot application whose `spring.application.name` property identifies it as `a-bootiful-client` to the Config Server. This is how the Config Server will know which set of configuration to send to a specific client. It will also send all the values from any file named `application.properties` or `application.yml` in the Git repository. Property keys in more specifically named files (like `a-bootiful-client.properties`) override those in `application.properties` or `application.yml`.

Add a simple property and value, `message = Hello world`, to the newly created `a-bootiful-client.properties` file and then `git commit` the change.

Specify the path to the Git repository by specifying the `spring.cloud.config.server.git.uri` property in `configuration-service/src/main/resources/application.properties`. Make sure to also specify a different `server.port` value to avoid port conflicts when you run both this server and another Spring Boot application on the same machine.

`configuration-service/src/main/resources/application.properties`

```properties
server.port=8888

spring.cloud.config.server.git.uri=${HOME}/Desktop/config
```

In this example we are using a file-based git repository at $`{HOME}/Desktop/config`. You can create one easily by making a new directory and git committing properties and YAML files to it. E.g.

```bash
$ cd ~/Desktop/config
$ find .
./.git
...
./application.yml
```

Or you could use a remote git repository, e.g. on github, if you change the configuration file in the application to point to that instead.

## Reading Configuration from the Config Server using the Config Client
Now that we’ve stood up a Config Server, let’s stand up a new Spring Boot application that uses the Config Server to load its own configuration and that refreshes its configuration to reflect changes to the Config Server on-demand, without restarting the JVM. Add the `org.springframework.cloud:spring-cloud-starter-config` dependency in order to connect to the Config Server. Spring will see the configuration property files just like it would any property file loaded from `application.properties` or `application.yml` or any other `PropertySource`.

The properties to configure the Config Client must necessarily be read in before the rest of the application’s configuration is read from the Config Server, during the bootstrap phase. Specify the client’s `spring.application.name` as `a-bootiful-client` and the location of the Config Server `spring.cloud.config.uri` in `configuration-client/src/main/resources/bootstrap.properties`, where it will be loaded earlier than any other configuration.

`configuration-client/src/main/resources/bootstrap.properties`

```properties
spring.application.name=a-bootiful-client
# N.B. this is the default:
spring.cloud.config.uri=http://localhost:8888
```

We also want to enable the `/refresh` endpoint so that we can demonstrate dynamic configuration changes:

`configuration-client/src/main/resources/application.properties`

```
management.endpoints.web.exposure.include=*
```

The client may access any value in the Config Server using the traditional mechanisms (e.g. `@ConfigurationProperties`, `@Value("${…​}")` or through the `Environment` abstraction). Create a Spring MVC REST controller that returns the resolved `message` property’s value. Consult the [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/) guide to learn more about building REST services with Spring MVC and Spring Boot.

By default, the configuration values are read on the client’s startup, and not again. You can force a bean to refresh its configuration - to pull updated values from the Config Server - by annotating the `MessageRestController` with the Spring Cloud Config `@RefreshScope` and then by triggering a refresh event.

`configuration-client/src/main/java/hello/ConfigClientApplication.java`

```java
package hello;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
public class ConfigClientApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConfigClientApplication.class, args);
    }
}

@RefreshScope
@RestController
class MessageRestController {

    @Value("${message:Hello default}")
    private String message;

    @RequestMapping("/message")
    String getMessage() {
        return this.message;
    }
}
```

## Test the application
Test the end-to-end result by starting the Config Service first and then, once loaded, starting the client. Visit the client app in the browser, http://localhost:8080/message. There, you should see the String `Hello world` reflected in the response.

Change the `message` key in the `a-bootiful-client.properties` file in the Git repository to something different (`Hello Spring!`, perhaps?). You can confirm that the Config Server sees the change by visiting http://localhost:8888/a-bootiful-client/default. You need to invoke the `refresh` Spring Boot Actuator endpoint in order to force the client to refresh itself and draw the new value in. Spring Boot’s Actuator exposes operational endpoints, like health checks and environment information, about an application. In order to use it you must add `org.springframework.boot:spring-boot-starter-actuator` to the client app’s CLASSPATH. You can invoke the `refresh` Actuator endpoint by sending an empty HTTP `POST` to the client’s `refresh` endpoint, http://localhost:8080/actuator/refresh, and then confirm it worked by reviewing the http://localhost:8080/message endpoint.

```bash
$ curl localhost:8080/actuator/refresh -d {} -H "Content-Type: application/json"
```

> we set `management.endpoints.web.exposure.include=*` in the client app to make this easy to test (by default since Spring Boot 2.0 the Actuator endpoints are not exposed by default). By default you can still access them over JMX if you don’t set the flag.

## Summary
Congratulations! You’ve just used Spring to centralize configuration for all your services by first standing up a and to then dynamically update configuration.

## See Also
The following guides may also be helpful:

* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)
* [Creating a Multi Module Project](https://spring.io/guides/gs/multi-module/)