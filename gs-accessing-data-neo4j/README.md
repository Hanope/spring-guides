## Accessing Data with Neo4j
This guide walks you through the process of using [Spring Data Neo4j](https://projects.spring.io/spring-data-neo4j/) to build an application that stores data in and retrieves it from [Neo4j](http://www.neo4j.com/), a graph-based database.

## What you’ll build
You’ll use Neo4j’s [NoSQL](http://spring.io/understanding/NoSQL) graph-based data store to build an embedded Neo4j server, store entities and relationships, and develop queries.

## What you’ll need
* About 15 minutes
* A favorite text editor or IDE
* [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) or later
* [Gradle 4+](http://www.gradle.org/downloads) or [Maven 3.2+](https://maven.apache.org/download.cgi)
* You can also import the code straight into your IDE:
  - [Spring Tool Suite (STS)](http://spring.io/guides/gs/sts)
  - [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea/)
    - RabbitMQ server (installation instructions below)

## How to complete this guide
Like most Spring [Getting Started guides](http://spring.io/guides), you can start from scratch and complete each step, or you can bypass basic setup steps that are already familiar to you. Either way, you end up with working code.

To **start from scratch**, move on to [Build with Gradle](#build-with-gradle).

To **skip the basics**, do the following:

* [Download](https://github.com/spring-guides/gs-accessing-data-neo4j/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-accessing-data-neo4j.git`
* cd into `gs-accessing-data-neo4j/initial`
* Jump ahead to [Define a simple entity](#define-a-simple-entity).

**When you’re finished**, you can check your results against the code in `gs-accessing-data-neo4j/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-accessing-data-neo4j/blob/master/initial/build.gradle).

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
    baseName = 'gs-accessing-data-neo4j'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
    maven { url "https://repo.spring.io/libs-release" }
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-data-neo4j")
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
	<artifactId>gs-accessing-data-neo4j</artifactId>
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
			<artifactId>spring-boot-starter-data-neo4j</artifactId>
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

	<repositories>
		<repository>
			<id>spring-releases</id>
			<name>Spring Releases</name>
			<url>https://repo.spring.io/libs-release</url>
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

## Standing up a Neo4j server
Before you can build a this application, you need to set up a Neo4j server.

Neo4j has an open source server you can install for free:

On a Mac, just type:

```
$ brew install neo4j
```

For other options, visit https://neo4j.com/download/community-edition/

Once you installed, launch it with it’s default settings:

```
$ neo4j start
```

You should see a message like this:

```
Starting Neo4j.
Started neo4j (pid 96416). By default, it is available at http://localhost:7474/
There may be a short delay until the server is ready.
See /usr/local/Cellar/neo4j/3.0.6/libexec/logs/neo4j.log for current status.
```

By default, Neo4j has a username/password of neo4j/neo4j. However, it requires that the new account password be changed. To do so, execute the following command:

```
$ curl -v -u neo4j:neo4j -X POST localhost:7474/user/neo4j/password -H "Content-type:application/json" -d "{\"password\":\"secret\"}"
```

This changes the password from **neo4j** to **secret** (something to NOT DO in production!) With that completed, you should be ready to run this guide.

## Define a simple entity
Neo4j captures entities and their relationships, with both aspects being of equal importance. Imagine you are modeling a system where you store a record for each person. But you also want to track a person’s co-workers (`teammates` in this example). With Neo4j, you can capture all that with some simple annotations.

`src/main/java/hello/Person.java`

```java
package hello;

import java.util.Collections;
import java.util.HashSet;
import java.util.Optional;
import java.util.Set;
import java.util.stream.Collectors;

import org.neo4j.ogm.annotation.GeneratedValue;
import org.neo4j.ogm.annotation.Id;
import org.neo4j.ogm.annotation.NodeEntity;
import org.neo4j.ogm.annotation.Relationship;

@NodeEntity
public class Person {

	@Id @GeneratedValue private Long id;

	private String name;

	private Person() {
		// Empty constructor required as of Neo4j API 2.0.5
	};

	public Person(String name) {
		this.name = name;
	}

	/**
	 * Neo4j doesn't REALLY have bi-directional relationships. It just means when querying
	 * to ignore the direction of the relationship.
	 * https://dzone.com/articles/modelling-data-neo4j
	 */
	@Relationship(type = "TEAMMATE", direction = Relationship.UNDIRECTED)
	public Set<Person> teammates;

	public void worksWith(Person person) {
		if (teammates == null) {
			teammates = new HashSet<>();
		}
		teammates.add(person);
	}

	public String toString() {

		return this.name + "'s teammates => "
			+ Optional.ofNullable(this.teammates).orElse(
					Collections.emptySet()).stream()
						.map(Person::getName)
						.collect(Collectors.toList());
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}
}
```

Here you have a `Person` class that has only one attribute, the `name`.

> In this guide, the typical getters and setters are omitted for brevity.

The `Person` class is annotated `@NodeEntity`. When Neo4j stores it, it results in the creation of a new node. This class also has an `id` marked `@GraphId`. Neo4j uses `@GraphId` internally to track the data.

The next important piece is the set of `teammates`. It is a simple `Set<Person>`, but marked up as `@Relationship`. This means that every member of this set is expected to also exist as a separate `Person` node. Note how the direction is set to `UNDIRECTED`. This means that when you query the `TEAMMATE` relationship, Spring Data Neo4j will ignore the direction of the relationship.

With the `worksWith()` method, you can easily link people together.

Finally, you have a convenient `toString()` method to print out the person’s name and that person’s co-workers.

## Create simple queries
Spring Data Neo4j is focused on storing data in Neo4j. But it inherits functionality from the Spring Data Commons project, including the ability to derive queries. Essentially, you don’t have to learn the query language of Neo4j, but can simply write a handful of methods and the queries are written for you.

To see how this works, create an interface that queries `Person` nodes.

`src/main/java/hello/PersonRepository.java`

```java
package hello;

import org.springframework.data.repository.CrudRepository;

public interface PersonRepository extends CrudRepository<Person, Long> {

    Person findByName(String name);
}
```

`PersonRepository` extends the `GraphRepository` interface and plugs in the type it operates on: `Person`. Out-of-the-box, this interface comes with many operations, including standard CRUD (create-read-update-delete) operations.

But you can define other queries as needed by simply declaring their method signature. In this case, you added `findByName`, which seeks nodes of type `Person` and finds the one that matches on `name`. You also have `findByTeammatesName`, which looks for a `Person` node, drills into each entry of the `teammates` field, and matches based on the teammate’s `name`.

## Permissions to access Neo4j
Neo4j Community Edition requires credentials to access it. This can be configured with a couple properties.

```
spring.data.neo4j.username=neo4j
spring.data.neo4j.password=secret
```

This includes the default username `neo4j` and the newly set password `secret` we picked earlier.

> Do NOT store real credentials in your source repository. Instead, configure them in your runtime using [Spring Boot’s property overrides](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-external-config).

With this in place, let’s wire this up and see what it looks like!

## Create an Application class
Here you create an Application class with all the components.

`src/main/java/hello/Application.java`

```java
package hello;

import java.util.Arrays;
import java.util.List;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.data.neo4j.repository.config.EnableNeo4jRepositories;

@SpringBootApplication
@EnableNeo4jRepositories
public class Application {

	private final static Logger log = LoggerFactory.getLogger(Application.class);

	public static void main(String[] args) throws Exception {
		SpringApplication.run(Application.class, args);
	}

	@Bean
	CommandLineRunner demo(PersonRepository personRepository) {
		return args -> {

			personRepository.deleteAll();

			Person greg = new Person("Greg");
			Person roy = new Person("Roy");
			Person craig = new Person("Craig");

			List<Person> team = Arrays.asList(greg, roy, craig);

			log.info("Before linking up with Neo4j...");

			team.stream().forEach(person -> log.info("\t" + person.toString()));

			personRepository.save(greg);
			personRepository.save(roy);
			personRepository.save(craig);

			greg = personRepository.findByName(greg.getName());
			greg.worksWith(roy);
			greg.worksWith(craig);
			personRepository.save(greg);

			roy = personRepository.findByName(roy.getName());
			roy.worksWith(craig);
			// We already know that roy works with greg
			personRepository.save(roy);

			// We already know craig works with roy and greg

			log.info("Lookup each person by name...");
			team.stream().forEach(person -> log.info(
					"\t" + personRepository.findByName(person.getName()).toString()));
		};
	}

}
```

`@SpringBootApplication` is a convenience annotation that adds all of the following:

* `@Configuration` tags the class as a source of bean definitions for the application context.
* `@EnableAutoConfiguration` tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings.
* Normally you would add `@EnableWebMvc` for a Spring MVC app, but Spring Boot adds it automatically when it sees **spring-webmvc** on the classpath. This flags the application as a web application and activates key behaviors such as setting up a `DispatcherServlet`.
* `@ComponentScan` tells Spring to look for other components, configurations, and services in the `hello` package, allowing it to find the controllers.

The `main()` method uses Spring Boot’s `SpringApplication.run()` method to launch an application. Did you notice that there wasn’t a single line of XML? No **web.xml** file either. This web application is 100% pure Java and you didn’t have to deal with configuring any plumbing or infrastructure.

Spring Boot will handle those repositories automatically as long as they are included in the same package (or a sub-package) of your `@SpringBootApplication` class. For more control over the registration process, you can use the `@EnableNeo4jRepositories` annotation.

> By default, `@EnableNeo4jRepositories` will scan the current package for any interfaces that extend one of Spring Data’s repository interfaces. Use it’s `basePackageClasses=MyRepository.class` to safely tell Spring Data Neo4j to scan a different root package by type if your project layout has multiple projects and its not finding your repositories.

Logging output is displayed. The service should be up and running within a few seconds.

You autowire an instance of `PersonRepository` that you defined earlier. Spring Data Neo4j will dynamically implement that interface and will plug in the needed query code to meet the interface’s obligations.

The `public static void main` uses Spring Boot’s `SpringApplication.run()` to launch the application and invoke the `CommandLineRunner` that builds the relationships.

In this case, you create three local `Person` s, **Greg**, **Roy**, and **Craig**. Initially, they only exist in memory. It’s also important to note that no one is a teammate of anyone (yet).

At first, you find Greg and indicate that he works with Roy and Craig, then persist him again. Remember, the teammate relationship was marked as `UNDIRECTED`, that is, bidirectional. That means that Roy and Craig will have been updated as well.

That’s why when you need to update Roy, it’s critical that you fetch that record from Neo4j first. You need the latest status on Roy’s teammates before adding **Craig** to the list.

Why is there no code that fetches Craig and adds any relationships? Because you already have! **Greg** earlier tagged Craig as a teammate, and so did Roy. That means there is no need to update Craig’s relationships again. You can see it as you iterate over each team member and print their information to the console.

Finally, check out that other query where you look backwards, answering the question "who works with whom?"

## Build an executable JAR
You can run the application from the command line with Gradle or Maven. Or you can build a single executable JAR file that contains all the necessary dependencies, classes, and resources, and run that. This makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.

If you are using Gradle, you can run the application using `./gradlew bootRun`. Or you can build the JAR file using `./gradlew build`. Then you can run the JAR file:

```
java -jar build/libs/gs-accessing-data-neo4j-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-accessing-data-neo4j-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/) instead.

You should see something like this (with other stuff like queries as well):

```
Before linking up with Neo4j...
	Greg's teammates => []
	Roy's teammates => []
	Craig's teammates => []

Lookup each person by name...
	Greg's teammates => [Roy, Craig]
	Roy's teammates => [Greg, Craig]
	Craig's teammates => [Roy, Greg]
```

You can see from the output that initially no one is connected by any relationship. Then after adding people in, they are tied together. Finally, you can see the handy query that looks up people based on teammate.

## Summary
Congratulations! You just set up an embedded Neo4j server, stored some simple, related entities, and developed some quick queries.

> If you’re interesting in exposing Neo4j repositories with a hypermedia-based RESTful front end with little effort, you might want to read [Accessing Neo4j Data with REST](http://spring.io/guides/gs/accessing-neo4j-data-rest).

## See Also
The following guides may also be helpful:

* [Accessing Data with MySQL](https://spring.io/guides/gs/accessing-data-mysql/)
* [Accessing Data with JPA](https://spring.io/guides/gs/accessing-data-jpa/)
* [Accessing Data with MongoDB](https://spring.io/guides/gs/accessing-data-mongodb/)
* [Accessing data with Gemfire](https://spring.io/guides/gs/accessing-data-gemfire/)