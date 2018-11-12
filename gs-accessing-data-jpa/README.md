## Accessing Data with JPA
This guide walks you through the process of building an application that uses Spring Data JPA to store and retrieve data in a relational database.

## What you’ll build
You’ll build an application that stores `Customer` [POJOs](http://spring.io/understanding/POJO) in a memory-based database.

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

* [Download](https://github.com/spring-guides/gs-accessing-data-jpa/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-accessing-data-jpa.git`
* cd into `gs-accessing-data-jpa/initial`
* Jump ahead to [Define a simple entity](#define-a-simple-entity).

**When you’re finished**, you can check your results against the code in `gs-accessing-data-jpa/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-accessing-data-jpa/blob/master/initial/build.gradle).

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
    baseName = 'gs-accessing-data-jpa'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
    maven { url "https://repository.jboss.org/nexus/content/repositories/releases" }
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-data-jpa")
    compile("com.h2database:h2")
    testCompile("junit:junit")
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
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-accessing-data-jpa</artifactId>
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
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
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

    <repositories>
        <repository>
            <id>spring-releases</id>
            <name>Spring Releases</name>
            <url>https://repo.spring.io/libs-release</url>
        </repository>
        <repository>
            <id>org.jboss.repository.releases</id>
            <name>JBoss Maven Release Repository</name>
            <url>https://repository.jboss.org/nexus/content/repositories/releases</url>
        </repository>
    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>spring-releases</id>
            <name>Spring Releases</name>
            <url>https://repo.spring.io/libs-release</url>
        </pluginRepository>
    </pluginRepositories>

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
In this example, you store Customer objects, annotated as a JPA entity.

`src/main/java/hello/Customer.java`

```java
package hello;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Customer {

    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Long id;
    private String firstName;
    private String lastName;

    protected Customer() {}

    public Customer(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }

    @Override
    public String toString() {
        return String.format(
                "Customer[id=%d, firstName='%s', lastName='%s']",
                id, firstName, lastName);
    }

}
```

Here you have a `Customer` class with three attributes, the `id`, the `firstName`, and the lastName. You also have two constructors. The default constructor only exists for the sake of JPA. You won’t use it directly, so it is designated as `protected`. The other constructor is the one you’ll use to create instances of `Customer` to be saved to the database.

> In this guide, the typical getters and setters have been left out for brevity.

The `Customer` class is annotated with `@Entity`, indicating that it is a JPA entity. For lack of a `@Table` annotation, it is assumed that this entity will be mapped to a table named `Customer`.

The `Customer`’s `id` property is annotated with `@Id` so that JPA will recognize it as the object’s ID. The `id` property is also annotated with `@GeneratedValue` to indicate that the ID should be generated automatically.

The other two properties, `firstName` and `lastName` are left unannotated. It is assumed that they’ll be mapped to columns that share the same name as the properties themselves.

The convenient `toString()` method will print out the customer’s properties.

## Create simple queries
Spring Data JPA focuses on using JPA to store data in a relational database. Its most compelling feature is the ability to create repository implementations automatically, at runtime, from a repository interface.

To see how this works, create a repository interface that works with `Customer` entities:

`src/main/java/hello/CustomerRepository.java`

```java
package hello;

import java.util.List;

import org.springframework.data.repository.CrudRepository;

public interface CustomerRepository extends CrudRepository<Customer, Long> {

    List<Customer> findByLastName(String lastName);
}
```

`CustomerRepository` extends the `CrudRepository` interface. The type of entity and ID that it works with,`Customer` and `Long`, are specified in the generic parameters on `CrudRepository`. By extending `CrudRepository`, `CustomerRepository` inherits several methods for working with `Customer` persistence, including methods for saving, deleting, and finding `Customer` entities.

Spring Data JPA also allows you to define other query methods by simply declaring their method signature. In the case of `CustomerRepository`, this is shown with a `findByLastName()` method.

In a typical Java application, you’d expect to write a class that implements `CustomerRepository`. But that’s what makes Spring Data JPA so powerful: You don’t have to write an implementation of the repository interface. Spring Data JPA creates an implementation on the fly when you run the application.

Let’s wire this up and see what it looks like!

## Create an Application class
Here you create an Application class with all the components.

`src/main/java/hello/Application.java`

```java
package hello;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class Application {

	private static final Logger log = LoggerFactory.getLogger(Application.class);

	public static void main(String[] args) {
		SpringApplication.run(Application.class);
	}

	@Bean
	public CommandLineRunner demo(CustomerRepository repository) {
		return (args) -> {
			// save a couple of customers
			repository.save(new Customer("Jack", "Bauer"));
			repository.save(new Customer("Chloe", "O'Brian"));
			repository.save(new Customer("Kim", "Bauer"));
			repository.save(new Customer("David", "Palmer"));
			repository.save(new Customer("Michelle", "Dessler"));

			// fetch all customers
			log.info("Customers found with findAll():");
			log.info("-------------------------------");
			for (Customer customer : repository.findAll()) {
				log.info(customer.toString());
			}
			log.info("");

			// fetch an individual customer by ID
			repository.findById(1L)
				.ifPresent(customer -> {
					log.info("Customer found with findById(1L):");
					log.info("--------------------------------");
					log.info(customer.toString());
					log.info("");
				});

			// fetch customers by last name
			log.info("Customer found with findByLastName('Bauer'):");
			log.info("--------------------------------------------");
			repository.findByLastName("Bauer").forEach(bauer -> {
				log.info(bauer.toString());
			});
			// for (Customer bauer : repository.findByLastName("Bauer")) {
			// 	log.info(bauer.toString());
			// }
			log.info("");
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

`Application` includes a `main()` method that puts the `CustomerRepository` through a few tests. First, it fetches the `CustomerRepository` from the Spring application context. Then it saves a handful of `Customer` objects, demonstrating the `save()` method and setting up some data to work with. Next, it calls `findAll()` to fetch all `Customer` objects from the database. Then it calls `findOne()` to fetch a single `Customer` by its ID. Finally, it calls `findByLastName()` to find all customers whose last name is "Bauer".

> By default, Spring Boot will enable JPA repository support and look in the package (and its subpackages) where `@SpringBootApplication` is located. If your configuration has JPA repository interface definitions located in a package not visible, you can point out alternate packages using `@EnableJpaRepositories` and its type-safe `basePackageClasses=MyRepository.class` parameter.

## Build an executable JAR
You can run the application from the command line with Gradle or Maven. Or you can build a single executable JAR file that contains all the necessary dependencies, classes, and resources, and run that. This makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.

If you are using Gradle, you can run the application using `./gradlew bootRun`. Or you can build the JAR file using `./gradlew build`. Then you can run the JAR file:

```
java -jar build/libs/gs-accessing-data-jpa-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-accessing-data-jpa-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/) instead.

You should see something like this:

```
== Customers found with findAll():
Customer[id=1, firstName='Jack', lastName='Bauer']
Customer[id=2, firstName='Chloe', lastName='O'Brian']
Customer[id=3, firstName='Kim', lastName='Bauer']
Customer[id=4, firstName='David', lastName='Palmer']
Customer[id=5, firstName='Michelle', lastName='Dessler']

== Customer found with findOne(1L):
Customer[id=1, firstName='Jack', lastName='Bauer']

== Customer found with findByLastName('Bauer'):
Customer[id=1, firstName='Jack', lastName='Bauer']
Customer[id=3, firstName='Kim', lastName='Bauer']
```

## Summary
Congratulations! You’ve written a simple application that uses Spring Data JPA to save objects to a database and to fetch them — all without writing a concrete repository implementation.

> If you’re interested in exposing JPA repositories with a hypermedia-based RESTful front end with little effort, you might want to read [Accessing JPA Data with REST](http://spring.io/guides/gs/accessing-data-rest).

## See Also
The following guides may also be helpful:

* [Accessing Data with Gemfire](https://spring.io/guides/gs/accessing-data-gemfire/)
* [Accessing Data with MongoDB](https://spring.io/guides/gs/accessing-data-mongodb/)
* [Accessing data with MySQL](https://spring.io/guides/gs/accessing-data-mysql/)
* [Accessing Data with Neo4j](https://spring.io/guides/gs/accessing-data-neo4j/)