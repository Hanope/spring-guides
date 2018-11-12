## Managing Transactions
This guide walks you through the process of wrapping database operations with non-intrusive transactions.

## What you’ll build
You’ll build a simple JDBC application wherein you make database operations transactional without having to write [specialized JDBC code](https://docs.oracle.com/javase/tutorial/jdbc/basics/transactions.html#commit_transactions).

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

* [Download](https://github.com/spring-guides/gs-managing-transactions/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-managing-transactions.git`
* cd into `gs-managing-transactions/initial`
* Jump ahead to [Create a booking service](#create-a-booking-service).

**When you’re finished**, you can check your results against the code in `gs-managing-transactions/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-managing-transactions/blob/master/initial/build.gradle).

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
    baseName = 'gs-managing-transactions'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-jdbc")
    runtime("com.h2database:h2")
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
    <artifactId>gs-managing-transactions</artifactId>
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
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>

        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
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

## Create a booking service
First, use the `BookingService` class to create a JDBC-based service that books people into the system by name.

`src/main/java/hello/BookingService.java`

```java
package hello;

import java.util.List;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Component;
import org.springframework.transaction.annotation.Transactional;

@Component
public class BookingService {

    private final static Logger logger = LoggerFactory.getLogger(BookingService.class);

    private final JdbcTemplate jdbcTemplate;

    public BookingService(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    @Transactional
    public void book(String... persons) {
        for (String person : persons) {
            logger.info("Booking " + person + " in a seat...");
            jdbcTemplate.update("insert into BOOKINGS(FIRST_NAME) values (?)", person);
        }
    }

    public List<String> findAllBookings() {
        return jdbcTemplate.query("select FIRST_NAME from BOOKINGS",
                (rs, rowNum) -> rs.getString("FIRST_NAME"));
    }

}
```

The code has an autowired `JdbcTemplate`, a handy template class that does all the database interactions needed by the code below.

You also have a `book` method aimed at booking multiple people. It loops through the list of people, and for each person, inserts them into the `BOOKINGS` table using the `JdbcTemplate`. This method is tagged with `@Transactional`, meaning that any failure causes the entire operation to roll back to its previous state, and to re-throw the original exception. This means that none of the people will be added to `BOOKINGS` if one person fails to be added.

You also have a `findAllBookings` method to query the database. Each row fetched from the database is converted into a `String` and then assembled into a `List`.

## Build an application

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
* `@ComponentScan` tells Spring to look for other components, configurations, and services in the `hello` package, allowing it to find the controllers.

The `main()` method uses Spring Boot’s `SpringApplication.run()` method to launch an application. Did you notice that there wasn’t a single line of XML? No **web.xml** file either. This web application is 100% pure Java and you didn’t have to deal with configuring any plumbing or infrastructure.

Your application has actually zero configuration. Spring Boot will detect `spring-jdbc` on the classpath and `h2` and will create a `DataSource` and a `JdbcTemplate` for you automatically. Because such infrastructure is now available and you have no dedicated configuration, a `DataSourceTransactionManager` will also be created for you: this is the component that intercepts the `@Transactional` annotated method (e.g. the `book` on `BookingService`). The `BookingService` is detected via classpath scanning.

Another Spring Boot feature demonstrated in this guide is [the ability to initialize the schema on startup](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#howto-initialize-a-database-using-spring-jdbc):

`src/main/resources/schema.sql`

```sql
drop table BOOKINGS if exists;
create table BOOKINGS(ID serial, FIRST_NAME varchar(5) NOT NULL);
```

There is also a `CommandLineRunner` that injects the `BookingService` and showcases various transactional use cases.

`src/main/java/hello/AppRunner.java`

```java
package hello;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;
import org.springframework.util.Assert;

@Component
class AppRunner implements CommandLineRunner {

    private final static Logger logger = LoggerFactory.getLogger(AppRunner.class);

    private final BookingService bookingService;

    public AppRunner(BookingService bookingService) {
        this.bookingService = bookingService;
    }

    @Override
    public void run(String... args) throws Exception {
        bookingService.book("Alice", "Bob", "Carol");
        Assert.isTrue(bookingService.findAllBookings().size() == 3,
                "First booking should work with no problem");
        logger.info("Alice, Bob and Carol have been booked");
        try {
            bookingService.book("Chris", "Samuel");
        } catch (RuntimeException e) {
            logger.info("v--- The following exception is expect because 'Samuel' is too " +
                    "big for the DB ---v");
            logger.error(e.getMessage());
        }

        for (String person : bookingService.findAllBookings()) {
            logger.info("So far, " + person + " is booked.");
        }
        logger.info("You shouldn't see Chris or Samuel. Samuel violated DB constraints, " +
                "and Chris was rolled back in the same TX");
        Assert.isTrue(bookingService.findAllBookings().size() == 3,
                "'Samuel' should have triggered a rollback");

        try {
            bookingService.book("Buddy", null);
        } catch (RuntimeException e) {
            logger.info("v--- The following exception is expect because null is not " +
                    "valid for the DB ---v");
            logger.error(e.getMessage());
        }

        for (String person : bookingService.findAllBookings()) {
            logger.info("So far, " + person + " is booked.");
        }
        logger.info("You shouldn't see Buddy or null. null violated DB constraints, and " +
                "Buddy was rolled back in the same TX");
        Assert.isTrue(bookingService.findAllBookings().size() == 3,
                "'null' should have triggered a rollback");
    }

}
```

You can run the application from the command line with Gradle or Maven. Or you can build a single executable JAR file that contains all the necessary dependencies, classes, and resources, and run that. This makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.

If you are using Gradle, you can run the application using `./gradlew bootRun`. Or you can build the JAR file using `./gradlew build`. Then you can run the JAR file:

```
java -jar build/libs/gs-managing-transactions-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-managing-transactions-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/) instead.

You should see the following output:

```
2016-09-01 09:47:55.031  INFO 16911 --- [           main] o.s.jdbc.datasource.init.ScriptUtils     : Executing SQL script from URL [file:/Users/foo/workspace/gs-managing-transactions/target/classes/schema.sql]
2016-09-01 09:47:55.052  INFO 16911 --- [           main] o.s.jdbc.datasource.init.ScriptUtils     : Executed SQL script from URL [file:/Users/foo/workspace/gs-managing-transactions/target/classes/schema.sql] in 21 ms.
2016-09-01 09:47:55.259  INFO 16911 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2016-09-01 09:47:55.282  INFO 16911 --- [           main] hello.BookingService                     : Booking Alice in a seat...
2016-09-01 09:47:55.292  INFO 16911 --- [           main] hello.BookingService                     : Booking Bob in a seat...
2016-09-01 09:47:55.292  INFO 16911 --- [           main] hello.BookingService                     : Booking Carol in a seat...
2016-09-01 09:47:55.306  INFO 16911 --- [           main] hello.AppRunner                          : Alice, Bob and Carol have been booked
2016-09-01 09:47:55.306  INFO 16911 --- [           main] hello.BookingService                     : Booking Chris in a seat...
2016-09-01 09:47:55.306  INFO 16911 --- [           main] hello.BookingService                     : Booking Samuel in a seat...
2016-09-01 09:47:55.312  INFO 16911 --- [           main] o.s.b.f.xml.XmlBeanDefinitionReader      : Loading XML bean definitions from class path resource [org/springframework/jdbc/support/sql-error-codes.xml]
2016-09-01 09:47:55.419  INFO 16911 --- [           main] o.s.jdbc.support.SQLErrorCodesFactory    : SQLErrorCodes loaded: [DB2, Derby, H2, HSQL, Informix, MS-SQL, MySQL, Oracle, PostgreSQL, Sybase, Hana]
2016-09-01 09:47:55.424  INFO 16911 --- [           main] hello.AppRunner                          : v--- The following exception is expect because 'Samuel' is too big for the DB ---v
2016-09-01 09:47:55.424 ERROR 16911 --- [           main] hello.AppRunner                          : PreparedStatementCallback; SQL [insert into BOOKINGS(FIRST_NAME) values (?)]; Value too long for column "FIRST_NAME VARCHAR(5) NOT NULL": "'Samuel' (6)"; SQL statement:
insert into BOOKINGS(FIRST_NAME) values (?) [22001-192]; nested exception is org.h2.jdbc.JdbcSQLException: Value too long for column "FIRST_NAME VARCHAR(5) NOT NULL": "'Samuel' (6)"; SQL statement:
insert into BOOKINGS(FIRST_NAME) values (?) [22001-192]
2016-09-01 09:47:55.424  INFO 16911 --- [           main] hello.AppRunner                          : So far, Alice is booked.
2016-09-01 09:47:55.424  INFO 16911 --- [           main] hello.AppRunner                          : So far, Bob is booked.
2016-09-01 09:47:55.424  INFO 16911 --- [           main] hello.AppRunner                          : So far, Carol is booked.
2016-09-01 09:47:55.424  INFO 16911 --- [           main] hello.AppRunner                          : You shouldn't see Chris or Samuel. Samuel violated DB constraints, and Chris was rolled back in the same TX
2016-09-01 09:47:55.425  INFO 16911 --- [           main] hello.BookingService                     : Booking Buddy in a seat...
2016-09-01 09:47:55.425  INFO 16911 --- [           main] hello.BookingService                     : Booking null in a seat...
2016-09-01 09:47:55.427  INFO 16911 --- [           main] hello.AppRunner                          : v--- The following exception is expect because null is not valid for the DB ---v
2016-09-01 09:47:55.427 ERROR 16911 --- [           main] hello.AppRunner                          : PreparedStatementCallback; SQL [insert into BOOKINGS(FIRST_NAME) values (?)]; NULL not allowed for column "FIRST_NAME"; SQL statement:
insert into BOOKINGS(FIRST_NAME) values (?) [23502-192]; nested exception is org.h2.jdbc.JdbcSQLException: NULL not allowed for column "FIRST_NAME"; SQL statement:
insert into BOOKINGS(FIRST_NAME) values (?) [23502-192]
2016-09-01 09:47:55.427  INFO 16911 --- [           main] hello.AppRunner                          : So far, Alice is booked.
2016-09-01 09:47:55.427  INFO 16911 --- [           main] hello.AppRunner                          : So far, Bob is booked.
2016-09-01 09:47:55.427  INFO 16911 --- [           main] hello.AppRunner                          : So far, Carol is booked.
2016-09-01 09:47:55.427  INFO 16911 --- [           main] hello.AppRunner                          : You shouldn't see Buddy or null. null violated DB constraints, and Buddy was rolled back in the same TX
```

The `BOOKINGS` table has two constraints on the **first_name** column:

* Names cannot be longer than five characters.
* Names cannot be null.

The first three names inserted are **Alice**, **Bob**, and **Carol**. The application asserts that three people were added to that table. If that had not worked, the application would have exited early.

Next, another booking is done for **Chris** and **Samuel**. Samuel’s name is deliberately too long, forcing an insert error. Transactional behavior stipulates that both Chris and Samuel; that is, this transaction, should be rolled back. Thus there should still be only three people in that table, which the assertion demonstrates.

Finally, **Buddy** and **null** are booked. As the output shows, null causes a rollback as well, leaving the same three people booked.

## Summary
Congratulations! You’ve just used Spring to develop a simple JDBC application wrapped with non-intrusive transactions.

## See Also
The following guides may also be helpful:

* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)
* [Accessing Data with JPA](https://spring.io/guides/gs/accessing-data-jpa/)
* [Accessing Data with MongoDB](https://spring.io/guides/gs/accessing-data-mongodb/)
* [Accessing data with MySQL](https://spring.io/guides/gs/accessing-data-mysql/)