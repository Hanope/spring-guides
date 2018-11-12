## Accessing Data in Pivotal GemFire
This guide walks you through the process of building an application with Pivotal GemFire’s data fabric.

## What you’ll build
You will use the powerful Spring Data for Pivotal GemFire library to store and retrieve [POJOs](http://spring.io/understanding/POJO).

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

* [Download](https://github.com/spring-guides/gs-accessing-data-gemfire/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-accessing-data-gemfire.git`
* cd into `gs-accessing-data-gemfire/initial`
* Jump ahead to [Define a simple entity](#define-a-simple-entity).

**When you’re finished**, you can check your results against the code in `gs-accessing-data-gemfire/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-accessing-data-gemfire/blob/master/initial/build.gradle).

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
    baseName = 'gs-accessing-data-gemfire'
    version = '0.1.0'
}

repositories {
    mavenCentral()
    maven { url "https://repo.spring.io/libs-release" }
}

dependencies {
    compile("org.springframework.boot:spring-boot-starter") {
        exclude group: "org.springframework.boot", module: "spring-boot-starter-logging"
    }
    compile("org.springframework.data:spring-data-gemfire")
    compile("org.projectlombok:lombok")
    runtime("org.springframework.shell:spring-shell:1.2.0.RELEASE")
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

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.5.RELEASE</version>
    </parent>

    <groupId>org.springframework</groupId>
    <artifactId>gs-accessing-data-gemfire</artifactId>
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
            <artifactId>spring-boot-starter</artifactId>
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
            <groupId>org.springframework.shell</groupId>
            <artifactId>spring-shell</artifactId>
            <version>${spring-shell.version}</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
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

## Define a simple entity
Pivotal GemFire is a In-Memory Data Grid (IMDG) that maps data to Regions. It is possible to configure distributed Regions that partition and replicate data across multiple nodes in a cluster. However, in this guide, you will be using a LOCAL Region so you don’t have to set up anything extra, such as an entire cluster of servers.

Pivotal GemFire is a Key/Value store and a Region implements the `java.util.concurrent.ConcurrentMap` interface. Though you can treat a Region like a `java.util.Map`, it is quite a bit more sophisticated than just a simple Java `Map` given data is distributed, replicated and generally managed inside the Region.

In this example, you store `Person` objects in Pivotal GemFire (a Region) using only a few annotations.

`src/main/java/hello/Person.java`

```java
package hello;

import java.io.Serializable;

import org.springframework.data.annotation.Id;
import org.springframework.data.annotation.PersistenceConstructor;
import org.springframework.data.gemfire.mapping.annotation.Region;

import lombok.Getter;

@Region(value = "People")
public class Person implements Serializable {

    @Id
    @Getter
    private final String name;

    @Getter
    private final int age;

    @PersistenceConstructor
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return String.format("%s is %d years old", getName(), getAge());
    }
}
```

Here you have a `Person` class with two fields, `name` and `age`. You also have a single, persistent constructor to populate the entities when creating a new instance. The class uses [Project Lombok](https://projectlombok.org/) to simplify the implementation.

Notice that this class is annotated with `@Region("People")`. When Pivotal GemFire stores an instance of this class, a new entry is created inside the "People" Region. This class also marks the `name` field with `@Id`. This signifies the identifier used to identify and track the `Person` data inside Pivotal GemFire. Essentially, the `@Id` annotated field (e.g. `name`) is the key and the `Person` instance is the value in the key/value entry. There is no automated key generation in Pivotal GemFire so you must set the id (i.e. `name`) prior to persisting the entity to Pivotal GemFire.

The next important piece is the person’s age. Later in this guide, you will use it to fashion some queries.

The overridden `toString()` method will print out the person’s name and age.

## Create simple queries
Spring Data for Pivotal GemFire focuses on storing and accessing data in Pivotal GemFire using Spring. It also inherits powerful functionality from the Spring Data Commons project, such as the ability to derive queries. Essentially, you don’t have to learn the query language of Pivotal GemFire (OQL); you can simply write a handful of methods and the queries are written for you by the framework.

To see how this works, create an interface that queries `Person` objects stored in Pivotal GemFire.

`src/main/java/hello/PersonRepository.java`

```java
package hello;

import org.springframework.data.gemfire.repository.query.annotation.Trace;
import org.springframework.data.repository.CrudRepository;

public interface PersonRepository extends CrudRepository<Person, String> {

    @Trace
    Person findByName(String name);

    @Trace
    Iterable<Person> findByAgeGreaterThan(int age);

    @Trace
    Iterable<Person> findByAgeLessThan(int age);

    @Trace
    Iterable<Person> findByAgeGreaterThanAndAgeLessThan(int greaterThanAge, int lessThanAge);

}
```

`PersonRepository` extends the `CrudRepository` interface from Spring Data Commons and specifies types for the generic type parameters for both the value and the id (key) that the Repository works with, i.e. `Person` and `String`, respectively. Out-of-the-box, this interface comes with many operations, including basic CRUD (CREATE, READ UPDATE, DELETE) and simple query (e.g. `findById(..)`) data access operations.

You can define other queries as needed by simply declaring their method signature. In this case, you add `findByName`, which essentially searches for objects of type `Person` and finds one that matches on `name`.

You also have:
* `findByAgeGreaterThan` to find people above a certain age
* `findByAgeLessThan` to find people below a certain age
* `findByAgeGreaterThanAndAgeLessThan` to find people in a certain age range

Let’s wire this up and see what it looks like!

## Create an application class
Here you create an Application class with all the components.

`src/main/java/hello/Application.java`

```java
package hello;

import static java.util.Arrays.asList;
import static java.util.stream.StreamSupport.stream;

import java.io.IOException;

import org.apache.geode.cache.client.ClientRegionShortcut;
import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.data.gemfire.config.annotation.ClientCacheApplication;
import org.springframework.data.gemfire.config.annotation.EnableEntityDefinedRegions;
import org.springframework.data.gemfire.repository.config.EnableGemfireRepositories;

@SpringBootApplication
@ClientCacheApplication(name = "AccessingDataGemFireApplication", logLevel = "error")
@EnableEntityDefinedRegions(basePackageClasses = Person.class,
  clientRegionShortcut = ClientRegionShortcut.LOCAL)
@EnableGemfireRepositories
public class Application {

    public static void main(String[] args) throws IOException {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    ApplicationRunner run(PersonRepository personRepository) {

        return args -> {

            Person alice = new Person("Adult Alice", 40);
            Person bob = new Person("Baby Bob", 1);
            Person carol = new Person("Teen Carol", 13);

            System.out.println("Before accessing data in Pivotal GemFire...");

            asList(alice, bob, carol).forEach(person -> System.out.println("\t" + person));

            System.out.println("Saving Alice, Bob and Carol to Pivotal GemFire...");

            personRepository.save(alice);
            personRepository.save(bob);
            personRepository.save(carol);

            System.out.println("Lookup each person by name...");

            asList(alice.getName(), bob.getName(), carol.getName())
              .forEach(name -> System.out.println("\t" + personRepository.findByName(name)));

            System.out.println("Query adults (over 18):");

            stream(personRepository.findByAgeGreaterThan(18).spliterator(), false)
              .forEach(person -> System.out.println("\t" + person));

            System.out.println("Query babies (less than 5):");

            stream(personRepository.findByAgeLessThan(5).spliterator(), false)
              .forEach(person -> System.out.println("\t" + person));

            System.out.println("Query teens (between 12 and 20):");

            stream(personRepository.findByAgeGreaterThanAndAgeLessThan(12, 20).spliterator(), false)
              .forEach(person -> System.out.println("\t" + person));
        };
    }
}
```

In the configuration, you need to add the `@EnableGemfireRepositories` annotation.

* By default, `@EnableGemfireRepositories` will scan the current package for any interfaces that extend one of Spring Data’s *Repository* interfaces. Use it’s `basePackageClasses = MyRepository.class` to safely tell *Spring Data* for *Pivotal GemFire* to scan a different root package by type for application-specific Repository extensions.

A Pivotal GemFire cache containing 1 or more Regions is required to store all the data. For that, you use 1 of *Spring Data for Pivotal GemFire’s* convenient configuration-based annotations: `@ClientCacheApplication`, `@PeerCacheApplication or '@CacheServerApplication`.

Pivotal GemFire supports different cache topologies like client/server, peer-to-peer (p2p) and even WAN arrangements. In p2p, a peer cache instance is embedded in the application and your application would have the ability to participate in a cluster as peer cache member. However, your application is subject to all the constraints of being a peer member in the cluster, so this is not as commonly used as, say, the client/server topology.

In our case, we will be using `@ClientCacheApplication` to create a "client" cache instance, which has the ability to connect to and communicate with a cluster of servers. However, to keep things simple, the client will just store data locally using a `LOCAL`, client Region, without the need to setup or run any servers.

> Spring recommends the production, enterprise edition that is [Pivotal GemFire](https://pivotal.io/pivotal-gemfire), where you can create distributed caches and Regions across multiple nodes in cluster. Alternatively, you can also use the open source version, Apache Geode, and get support from the [Apache Geode](https://geode.apache.org/) community.

Now, remember how you tagged `Person` to be stored in a Region called "People" using the SDG mapping annotation, `@Region("People")`? You define that Region here using the `ClientRegionFactoryBean<String, Person>` bean definition. You need to inject an instance of the cache you just defined while also naming it "People".

> A Pivotal GemFire cache instance (whether a peer or client) is just a container for Regions, which store your data. You can think of the cache as a schema in an RDBMS and Regions as the tables. However, a cache also performs other administrative functions to control and manage all your Regions.

> The types are `<String, Person>`, matching the key type (`String`) with the value type (`Person`).

The `public static void main` method uses Spring Boot’s `SpringApplication.run()` to launch the application and invoke the `ApplicationRunner` (another bean definition) that performs the data access operations on Pivotal GemFire using the application’s Spring Data Repository.

The application autowires an instance of `PersonRepository` that you just defined. Spring Data for Pivotal GemFire will dynamically create a concrete class that implements this interface and plug in the needed query code to meet the interface’s obligations. This Repository instance is the used by the `run()` method to demonstrate the functionality.

## Store and fetch data
In this guide, you are creating three local `Person` objects, **Alice**, **Baby Bob**, and **Teen Carol**. Initially, they only exist in memory. After creating them, you have to save them to Pivotal GemFire.

Now you run several queries. The first looks up everyone by name. Then you execute a handful of queries to find adults, babies, and teens, all using the age attribute. With the logging turned up, you can see the queries *Spring Data for Pivotal GemFire* writes on your behalf.

> change the `@ClientCacheApplication` annotation `logLevel` attribute to "config" to see the Pivotal GemFire OQL queries that are generated by SDG. Because the query methods (e.g. `findByName`) are annotated with SDG’s `@Trace` annotation, this turns on Pivotal GemFire’s OQL query tracing (query-level logging), which shows you the generated OQL, execution time, whether any Pivotal GemFire Indexes were used by the query to gather the results, and the number of rows returned by the query.

## Build an executable JAR
You can run the application from the command line with Gradle or Maven. Or you can build a single executable JAR file that contains all the necessary dependencies, classes, and resources, and run that. This makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.

If you are using Gradle, you can run the application using `./gradlew bootRun`. Or you can build the JAR file using `./gradlew build`. Then you can run the JAR file:

```
java -jar build/libs/gs-accessing-data-gemfire-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-accessing-data-gemfire-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/) instead.

You should see something like this (with other stuff like queries as well):

```
Before linking up with GemFire...
	Alice is 40 years old.
	Baby Bob is 1 years old.
	Teen Carol is 13 years old.
Lookup each person by name...
	Alice is 40 years old.
	Baby Bob is 1 years old.
	Teen Carol is 13 years old.
Adults (over 18):
	Alice is 40 years old.
Babies (less than 5):
	Baby Bob is 1 years old.
Teens (between 12 and 20):
	Teen Carol is 13 years old.
```

## Summary
Congratulations! You set up an Pivotal GemFire cache client, stored simple entities, and developed quick queries.

## See Also
The following guides may also be helpful:

* [Accessing Data with JPA](https://spring.io/guides/gs/accessing-data-jpa/)
* [Accessing Data with MongoDB](https://spring.io/guides/gs/accessing-data-mongodb/)
* [Accessing data with MySQL](https://spring.io/guides/gs/accessing-data-mysql/)
* [Accessing Data with Neo4j](https://spring.io/guides/gs/accessing-data-neo4j/)