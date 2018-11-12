## Accessing data with MySQL
This guide walks you through the process of creating a Spring application connected with a MySQL Database, as opposed to an in-memory, embedded database, which all of the other guides and many sample apps use. It uses Spring Data JPA to access the database, but this is only one of many possible choices (e.g. you could use plain Spring JDBC).

## What you’ll build
You’ll create a MySQL database, build a Spring application and connect it with the newly created database.

> MySQL is licensed with the GPL, so any program binary that you distribute using it must use the GPL too. Refer to the [GNU General Public Licence](https://www.gnu.org/licenses/gpl.html).

## What you’ll need
* [MySQL](https://dev.mysql.com/downloads/) version 5.6 or better. If you have docker installed it might be useful to run the database as a [container](https://hub.docker.com/_/mysql/).
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

* [Download](https://github.com/spring-guides/gs-accessing-data-mysql/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-accessing-data-mysql.git`
* cd into `gs-accessing-data-mysql/initial`
* Jump ahead to [Create the database](#create-the-database).

**When you’re finished**, you can check your results against the code in `gs-accessing-data-mysql/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-accessing-data-mysql/blob/master/initial/build.gradle).

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
    baseName = 'gs-accessing-data-mysql'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")

    // JPA Data (We are going to use Repositories, Entities, Hibernate, etc...)
    compile 'org.springframework.boot:spring-boot-starter-data-jpa'

    // Use MySQL Connector-J
    compile 'mysql:mysql-connector-java'

    testCompile('org.springframework.boot:spring-boot-starter-test')
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
    <artifactId>gs-mysql-data</artifactId>
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

        <!-- JPA Data (We are going to use Repositories, Entities, Hibernate, etc...) -->

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <!-- Use MySQL Connector-J -->

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
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

## Create the database
Go to the terminal (command Prompt `cmd` in Microsoft Windows). Open MySQL client with a user that can create new users.

For example: On a Linux, use the command

```bash
$ sudo mysql --password
```

> This connects to MySQL as a root, this is **not the recommended way** for a production server.

Create a new database

```bash
mysql> create database db_example; -- Create the new database
mysql> create user 'springuser'@'localhost' identified by 'ThePassword'; -- Creates the user
mysql> grant all on db_example.* to 'springuser'@'localhost'; -- Gives all the privileges to the new user on the newly created database
```

## Create the `application.properties` file
Spring Boot gives you defaults on all things, the default in database is `H2`, so when you want to change this and use any other database you must define the connection attributes in the `application.properties` file.

In the sources folder, you create a resource file `src/main/resources/application.properties`

```properties
spring.jpa.hibernate.ddl-auto=create
spring.datasource.url=jdbc:mysql://localhost:3306/db_example
spring.datasource.username=springuser
spring.datasource.password=ThePassword
```

Here, `spring.jpa.hibernate.ddl-auto` can be `none`, `update`, `create`, `create-drop`, refer to the Hibernate documentation for details.

* `none` This is the default for `MySQL`, no change to the database structure.
* `update` Hibernate changes the database according to the given Entity structures.
* `create` Creates the database every time, but don’t drop it when close.
* `create-drop` Creates the database then drops it when the `SessionFactory` closes.

We here begin with `create` because we don’t have the database structure yet. After the first run, we could switch it to `update` or `none` according to program requirements. Use `update` when you want to make some change to the database structure.

The default for `H2` and other embedded databases is `create-drop`, but for others like `MySQL` is `none`

It is good security practice that after your database is in production state, you make this `none` and revoke all privileges from the MySQL user connected to the Spring application, then give him only SELECT, UPDATE, INSERT, DELETE.

This is coming in details in the end of this guide.

## Create the `@Entity` model

`src/main/java/hello/User.java`

```java
package hello;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity // This tells Hibernate to make a table out of this class
public class User {
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Integer id;

    private String name;

    private String email;

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}


}
```

This is the entity class which Hibernate will automatically translate into a table.

## Create the repository

`src/main/java/hello/UserRepository.java`

```java
package hello;

import org.springframework.data.repository.CrudRepository;

import hello.User;

// This will be AUTO IMPLEMENTED by Spring into a Bean called userRepository
// CRUD refers Create, Read, Update, Delete

public interface UserRepository extends CrudRepository<User, Integer> {

}
```

This is the repository interface, this will be automatically implemented by Spring in a bean with the same name with changing case The bean name will be `userRepository`

## Create a new controller for your Spring application

`src/main/java/hello/MainController.java`

```java
package hello;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

import hello.User;
import hello.UserRepository;

@Controller    // This means that this class is a Controller
@RequestMapping(path="/demo") // This means URL's start with /demo (after Application path)
public class MainController {
	@Autowired // This means to get the bean called userRepository
	           // Which is auto-generated by Spring, we will use it to handle the data
	private UserRepository userRepository;

	@GetMapping(path="/add") // Map ONLY GET Requests
	public @ResponseBody String addNewUser (@RequestParam String name
			, @RequestParam String email) {
		// @ResponseBody means the returned String is the response, not a view name
		// @RequestParam means it is a parameter from the GET or POST request

		User n = new User();
		n.setName(name);
		n.setEmail(email);
		userRepository.save(n);
		return "Saved";
	}

	@GetMapping(path="/all")
	public @ResponseBody Iterable<User> getAllUsers() {
		// This returns a JSON or XML with the users
		return userRepository.findAll();
	}
}
```

> The above example does not explicitly specify `GET` vs. `PUT`, `POST`, and so forth, because `@GetMapping` is a shortcut for `@RequestMapping(method=GET)`. `@RequestMapping` maps all HTTP operations by default. Use `@RequestMapping(method=GET)` or [other shortcut annotations](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/bind/annotation/package-summary.html) to narrow this mapping.

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

## Build an executable JAR
You can run the application from the command line with Gradle or Maven. Or you can build a single executable JAR file that contains all the necessary dependencies, classes, and resources, and run that. This makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.

If you are using Gradle, you can run the application using `./gradlew bootRun`. Or you can build the JAR file using `./gradlew build`. Then you can run the JAR file:

```
java -jar build/libs/gs-accessing-data-mysql-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-accessing-data-mysql-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/) instead.

Logging output is displayed. The service should be up and running within a few seconds.

## Test the application
Now that the application is running, you can test it.

Use `curl` for example. Now you have 2 REST Web Services you can test

`localhost:8080/demo/all` This gets all data `localhost:8080/demo/add` This adds one user to the data

```bash
$ curl 'localhost:8080/demo/add?name=First&email=someemail@someemailprovider.com'
```

The reply should be

```bash
Saved
```

```bash
$ curl 'localhost:8080/demo/all'
```

The reply should be

```json
[{"id":1,"name":"First","email":"someemail@someemailprovider.com"}]
```

## Make some security changes
Now when you are on production environment, you may be exposed to SQL injection attacks. A hacker may inject `DROP TABLE` or any other destructive SQL commands. So as a security practice, make those changes to your database before you expose the application to users.

```bash
mysql> revoke all on db_example.* from 'springuser'@'localhost';
```

This revokes ALL the priviliges from the user associated with the Spring application. Now the Spring application **cannot do** anything in the database. We don’t want that, so

```bash
mysql> grant select, insert, delete, update on db_example.* to 'springuser'@'localhost';
```

This gives your Spring application only the privileges necessary to make changes to **only** the data of the database and not the structure (schema).

Now make this change to your `src/main/resources/application.properties`

```properties
spring.jpa.hibernate.ddl-auto=none
```

This is instead of `create` which was on the first run for Hibernate to create the tables from your entities.

When you want to make changes on the database, regrant the permissions, change the `spring.jpa.hibernate.ddl-auto` to `update`, then re-run your applications, then repeat. Or, better, use a dedicated migration tool such as Flyway or Liquibase.

## Summary
Congratulations! You’ve just developed a Spring application which is bound to a MySQL database, Ready for production!

## See Also
The following guides may also be helpful:

* [Accessing Data with JPA](https://spring.io/guides/gs/accessing-data-jpa/)
* [Accessing Data with MongoDB](https://spring.io/guides/gs/accessing-data-mongodb/)
* [Accessing data with Gemfire](https://spring.io/guides/gs/accessing-data-gemfire/)