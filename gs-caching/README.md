## Caching Data with Spring
This guide walks you through the process of enabling caching on a Spring managed bean.

## What you’ll build
You’ll build an application that enables caching on a simple book repository.

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

* [Download](https://github.com/spring-guides/gs-caching/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-caching.git`
* cd into `gs-caching/initial`
* Jump ahead to [Create a book repository](#create-a-book-repository).

**When you’re finished**, you can check your results against the code in `gs-caching/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-caching/blob/master/initial/build.gradle).

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
    baseName = 'gs-caching'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-cache")
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
    <artifactId>gs-caching</artifactId>
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
            <artifactId>spring-boot-starter-cache</artifactId>
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

## Create a book repository
First, let’s create a very simple model for your book

`src/main/java/hello/Book.java`

```java
package hello;

public class Book {

    private String isbn;
    private String title;

    public Book(String isbn, String title) {
        this.isbn = isbn;
        this.title = title;
    }

    public String getIsbn() {
        return isbn;
    }

    public void setIsbn(String isbn) {
        this.isbn = isbn;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    @Override
    public String toString() {
        return "Book{" + "isbn='" + isbn + '\'' + ", title='" + title + '\'' + '}';
    }

}
```

And a repository for that model:

`src/main/java/hello/BookRepository.java`

```java
package hello;

public interface BookRepository {

    Book getByIsbn(String isbn);

}
```

You could have used [Spring Data](https://projects.spring.io/spring-data/) to provide an implementation of your repository over a wide range of SQL or NoSQL stores, but for the purpose of this guide, you will simply use a naive implementation that simulates some latency (network service, slow delay, etc).

`src/main/java/hello/SimpleBookRepository.java`

```java
package hello;

import org.springframework.stereotype.Component;

@Component
public class SimpleBookRepository implements BookRepository {

    @Override
    public Book getByIsbn(String isbn) {
        simulateSlowService();
        return new Book(isbn, "Some book");
    }

    // Don't do this at home
    private void simulateSlowService() {
        try {
            long time = 3000L;
            Thread.sleep(time);
        } catch (InterruptedException e) {
            throw new IllegalStateException(e);
        }
    }

}
```

`simulateSlowService` is deliberately inserting a three second delay into each `getByIsbn` call. This is an example that later on, you’ll speed up with caching.

## Using the repository
Next, wire up the repository and use it to access some books.

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

There is also a `CommandLineRunner` that injects the `BookRepository` and calls it several times with different arguments.

`src/main/java/hello/AppRunner.java`

```java
package hello;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class AppRunner implements CommandLineRunner {

    private static final Logger logger = LoggerFactory.getLogger(AppRunner.class);

    private final BookRepository bookRepository;

    public AppRunner(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }

    @Override
    public void run(String... args) throws Exception {
        logger.info(".... Fetching books");
        logger.info("isbn-1234 -->" + bookRepository.getByIsbn("isbn-1234"));
        logger.info("isbn-4567 -->" + bookRepository.getByIsbn("isbn-4567"));
        logger.info("isbn-1234 -->" + bookRepository.getByIsbn("isbn-1234"));
        logger.info("isbn-4567 -->" + bookRepository.getByIsbn("isbn-4567"));
        logger.info("isbn-1234 -->" + bookRepository.getByIsbn("isbn-1234"));
        logger.info("isbn-1234 -->" + bookRepository.getByIsbn("isbn-1234"));
    }

}
```

If you try to run the application at this point, you’ll notice it’s quite slow even though you are retrieving the exact same book several times.

```
2014-06-05 12:15:35.783  ... : .... Fetching books
2014-06-05 12:15:40.783  ... : isbn-1234 -->Book{isbn='isbn-1234', title='Some book'}
2014-06-05 12:15:43.784  ... : isbn-1234 -->Book{isbn='isbn-1234', title='Some book'}
2014-06-05 12:15:46.786  ... : isbn-1234 -->Book{isbn='isbn-1234', title='Some book'}
```

As can be seen by the timestamps, each book took about three seconds to retrieve, even though it’s the same title being repeatedly fetched.

## Enable caching
Let’s enable caching on your `SimpleBookRepository` so that the books are cached within the `books` cache.

`src/main/java/hello/SimpleBookRepository.java`

```java
package hello;

import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Component;

@Component
public class SimpleBookRepository implements BookRepository {

    @Override
    @Cacheable("books")
    public Book getByIsbn(String isbn) {
        simulateSlowService();
        return new Book(isbn, "Some book");
    }

    // Don't do this at home
    private void simulateSlowService() {
        try {
            long time = 3000L;
            Thread.sleep(time);
        } catch (InterruptedException e) {
            throw new IllegalStateException(e);
        }
    }

}
```

You now need to enable the processing of the caching annotations

`src/main/java/hello/Application.java`

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cache.annotation.EnableCaching;

@SpringBootApplication
@EnableCaching
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```

The `@EnableCaching` annotation triggers a post processor that inspects every Spring bean for the presence of caching annotations on public methods. If such an annotation is found, a proxy is automatically created to intercept the method call and handle the caching behavior accordingly.

The annotations that are managed by this post processor are `Cacheable`, `CachePut` and `CacheEvict`. You can refer to the javadocs and [the documentation](https://docs.spring.io/spring/docs/current/spring-framework-reference/html/cache.html) for more details.

Spring Boot automatically configures a suitable `CacheManager` to serve as a provider for the relevant cache. See [the Spring Boot documentation](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-caching.html) for more details.

Our sample does not use a specific caching library so our cache store is the simple fallback that uses `ConcurrentHashMap`. The caching abstraction supports a wide range of cache library and is fully compliant with JSR-107 (JCache).

## Build an executable JAR
You can run the application from the command line with Gradle or Maven. Or you can build a single executable JAR file that contains all the necessary dependencies, classes, and resources, and run that. This makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.

If you are using Gradle, you can run the application using `./gradlew bootRun`. Or you can build the JAR file using `./gradlew build`. Then you can run the JAR file:

```
java -jar build/libs/gs-caching-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-caching-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file instead](http://spring.io/guides/gs/convert-jar-to-war/).

## Test the application
Now that caching is enabled, you can execute it again and see the difference by adding additional calls with or without the same isbn. It should make a huge difference.

```
2016-09-01 11:12:47.033  .. : .... Fetching books
2016-09-01 11:12:50.039  .. : isbn-1234 -->Book{isbn='isbn-1234', title='Some book'}
2016-09-01 11:12:53.044  .. : isbn-4567 -->Book{isbn='isbn-4567', title='Some book'}
2016-09-01 11:12:53.045  .. : isbn-1234 -->Book{isbn='isbn-1234', title='Some book'}
2016-09-01 11:12:53.045  .. : isbn-4567 -->Book{isbn='isbn-4567', title='Some book'}
2016-09-01 11:12:53.045  .. : isbn-1234 -->Book{isbn='isbn-1234', title='Some book'}
2016-09-01 11:12:53.045  .. : isbn-1234 -->Book{isbn='isbn-1234', title='Some book'}
```

This excerpt from the console shows that the first time to fetch each title took three seconds, but each subsequent call was near instantaneous.

## Summary
Congratulations! You’ve just enabled caching on a Spring managed bean.