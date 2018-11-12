## Service Registration and Discovery
This guide walks you through the process of standing up and consuming the Netflix Eureka service registry.om

## What you’ll build
You’ll setup a [Netflix Eureka service registry](https://github.com/spring-cloud/spring-cloud-netflix) and then build a client that both registers itself with the registry and uses it to resolve its own host. A service registry is useful because it enables client-side load-balancing and decouples service providers from consumers without the need for DNS.

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

* [Download](https://github.com/spring-guides/gs-service-registration-and-discovery/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-service-registration-and-discovery.git`
* cd into `gs-service-registration-and-discovery/initial`
* Jump ahead to [Stand up a Eureka Service Registry](#stand-up-a-eureka-service-registry).

**When you’re finished**, you can check your results against the code in `gs-service-registration-and-discovery/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-service-registration-and-discovery/blob/master/initial/build.gradle).

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

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

bootJar {
	baseName = 'eureka-service'
	version = '0.0.1-SNAPSHOT'
}
sourceCompatibility = 1.8
targetCompatibility = 1.8

repositories {
	mavenCentral()
}


dependencyManagement {
	imports {
		mavenBom 'org.springframework.cloud:spring-cloud-dependencies:Finchley.SR1'
	}
}

repositories {
    maven {
        url 'https://repo.spring.io/libs-milestone'
    }
}

dependencies {
	compile('org.springframework.cloud:spring-cloud-starter-netflix-eureka-server')
	testCompile('org.springframework.boot:spring-boot-starter-test')
}


eclipse {
	classpath {
		 containers.remove('org.eclipse.jdt.launching.JRE_CONTAINER')
		 containers 'org.eclipse.jdt.launching.JRE_CONTAINER/org.eclipse.jdt.internal.debug.ui.launcher.StandardVMType/JavaSE-1.8'
	}
}
```

`eureka-client/build.gradle`

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

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

bootJar {
	baseName = 'eureka-client'
	version = '0.0.1-SNAPSHOT'
}
sourceCompatibility = 1.8
targetCompatibility = 1.8

repositories {
	mavenCentral()
}

dependencyManagement {
	imports {
		mavenBom 'org.springframework.cloud:spring-cloud-dependencies:Finchley.SR1'
	}
}

repositories {
    maven {
        url 'https://repo.spring.io/libs-milestone'
    }
}

dependencies {
	compile('org.springframework.cloud:spring-cloud-starter-netflix-eureka-client')
	compile('org.springframework.boot:spring-boot-starter-web')
	testCompile('org.springframework.boot:spring-boot-starter-test')
	testCompile('org.springframework.cloud:spring-cloud-starter-eureka-server')
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
	<artifactId>eureka-service</artifactId>
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
			<artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
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

`eureka-client/pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>eureka-client</artifactId>
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
			<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
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

The [Spring Boot Maven plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin) provides many convenient features:

* It collects all the jars on the classpath and builds a single, runnable "über-jar", which makes it more convenient to execute and transport your service.
* It searches for the `public static void main()` method to flag as a runnable class.
* It provides a built-in dependency resolver that sets the version number to match [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml). You can override any version you wish, but it will default to Boot’s chosen set of versions.

## Build with your IDE
* Read how to import this guide straight into [Spring Tool Suite](http://spring.io/guides/gs/sts/).
* Read how to work with this guide in [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea).

## Stand up a Eureka Service Registry
You’ll first need a Eureka Service registry. You can use Spring Cloud’s `@EnableEurekaServer` to standup a registry that other applications can talk to. This is a regular Spring Boot application with one annotation added to enable the service registry.

`eureka-service/src/main/java/hello/EurekaServiceApplication.java`

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@EnableEurekaServer
@SpringBootApplication
public class EurekaServiceApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaServiceApplication.class, args);
    }
}
```

When the registry starts up it will complain, with a stacktrace, that there are no replica nodes for the registry to connect to. In a production environment, you will want more than one instance of the registry. For our simple purposes, however, it sufficies to disable the relevant logging.

By default, the registry will also attempt to register itself, so you’ll need to disable that, as well.

It’s a good convention to put this registry on a separate port when using it locally.

Add some properties to your `eureka-service/src/main/resources/application.properties` to handle all of these requirements.

`eureka-service/src/main/resources/application.properties`

```properties
server.port=8761

eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false

logging.level.com.netflix.eureka=OFF
logging.level.com.netflix.discovery=OFF
```

## Talking to the Registry
Now that we’ve stood up a service registry, let’s stand up a client that both registers itself with the registry and uses the Spring Cloud `DiscoveryClient` abstraction to interrogate the registry for it’s own host and port. The `@EnableDiscoveryClient` activates the Netflix Eureka `DiscoveryClient` implementation. There are other implementations for other service registries like [Hashicorp’s Consul](https://www.consul.io/) or [Apache Zookeeper](https://zookeeper.apache.org/).

`eureka-client/src/main/java/hello/EurekaClientApplication.java`

```java
package hello;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@EnableDiscoveryClient
@SpringBootApplication
public class EurekaClientApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaClientApplication.class, args);
    }
}

@RestController
class ServiceInstanceRestController {

    @Autowired
    private DiscoveryClient discoveryClient;

    @RequestMapping("/service-instances/{applicationName}")
    public List<ServiceInstance> serviceInstancesByApplicationName(
            @PathVariable String applicationName) {
        return this.discoveryClient.getInstances(applicationName);
    }
}
```

Whatever implementation you choose, you’ll soon see the `eureka-client` registered under whatever name you specify in the `spring.application.name` property. This property is used a lot in Spring Cloud, often in the earliest phases of a service’s configuration. This property is used in service bootstrap and so by convention lives in `eureka-client/src/main/resources/bootstrap.properties` where it’s found before `src/main/resources/application.properties`.

`eureka-client/src/main/resources/bootstrap.properties`

```
spring.application.name=a-bootiful-client
```

The `eureka-client` defines a Spring MVC REST endpoint, `ServiceInstanceRestController`, that returns an enumeration of all the `ServiceInstance` instances registered in the registry at http://localhost:8080/service-instances/a-bootiful-client. Consult the [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/) guide to learn more about building REST services with Spring MVC and Spring Boot.

## Test the application
Test the end-to-end result by starting the `eureka-service` first and then, once loaded, starting the `eureka-client`. The `eureka-client` will take about a minute to register itself in the registry and to refresh its own list of registered instances from the registry. All of these thresholds are configurable. Visit the `eureka-client` in the browser, http://localhost:8080/service-instances/a-bootiful-client. There, you should see the `ServiceInstance` for the `eureka-client` reflected in the response.

## Summary
Congratulations! You’ve just used Spring to stand up a Netflix Eureka service registry and to use that registry in a client application.