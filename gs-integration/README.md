## Integrating Data
This guide walks you through the process of using Spring Integration to create a simple application that retrieves data from an RSS Feed (Spring Blog), manipulates the data, and then writes it to a file. This guide uses traditional Spring Integration XML configuration; other guides exist showing the use of JavaConfig/DSL with and without JDK 8 Lambda expressions.

## What you’ll build
You’ll create a flow with Spring Integration using traditional XML configuration.

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

* [Download](https://github.com/spring-guides/gs-integration/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-integration.git`
* cd into `gs-integration/initial`
* Jump ahead to [Define an integration flow](#define-a-integration-flow).

**When you’re finished**, you can check your results against the code in `gs-integration/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-integration/blob/master/initial/build.gradle).

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
    baseName = 'gs-integration'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-integration")
    compile("org.springframework.integration:spring-integration-feed")
    compile("org.springframework.integration:spring-integration-file")
    testCompile("org.springframework.boot:spring-boot-starter-test")
    testCompile("junit:junit")
}

tasks.withType(JavaExec) {
    standardInput = System.in
}


eclipse {
    project {
        natures += 'org.springframework.ide.eclipse.core.springnature'
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

    <groupId>org.springframework</groupId>
    <artifactId>gs-integration</artifactId>
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
            <artifactId>spring-boot-starter-integration</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.integration</groupId>
            <artifactId>spring-integration-feed</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.integration</groupId>
            <artifactId>spring-integration-file</artifactId>
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
    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>spring-releases</id>
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

## Define an integration flow
For this guide’s sample application, you will define a Spring Integration flow that reads blog posts from Spring IO’s RSS feed, transforms them into an easily readable `String` consisting of the post title and the URL for the post, and appends that `String` to the end of a file `/tmp/si/SpringBlog`.

To define an integration flow, you simply create a Spring XML configuration with a handful of elements from Spring Integration’s XML namespaces. Specifically, for the desired integration flow, you work with elements from these Spring Integration namespaces: core, feed, and file.

The following XML configuration file defines the integration flow:

`src/main/resources/hello/integration.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:int="http://www.springframework.org/schema/integration"
	xmlns:file="http://www.springframework.org/schema/integration/file"
	xmlns:feed="http://www.springframework.org/schema/integration/feed"
	xsi:schemaLocation="http://www.springframework.org/schema/integration/feed http://www.springframework.org/schema/integration/feed/spring-integration-feed.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/integration/file http://www.springframework.org/schema/integration/file/spring-integration-file.xsd
		http://www.springframework.org/schema/integration http://www.springframework.org/schema/integration/spring-integration.xsd">

    <feed:inbound-channel-adapter id="news" url="http://spring.io/blog.atom" auto-startup="${auto.startup:true}">
        <int:poller fixed-rate="5000"/>
    </feed:inbound-channel-adapter>

    <int:transformer
            input-channel="news"
            expression="payload.title + ' @ ' + payload.link + '#{systemProperties['line.separator']}'"
            output-channel="file"/>

    <file:outbound-channel-adapter id="file"
            mode="APPEND"
            charset="UTF-8"
            directory="/tmp/si"
            filename-generator-expression="'${feed.file.name:SpringBlog}'"/>

</beans>
```

As you can see, three integration elements are in play here:

* `<feed:inbound-channel-adapter>`. An inbound adapter that retrieves the posts, one per poll. As configured here, it polls every 5 seconds. The posts are placed into a channel named "news" (corresponding with the adapter’s ID).
* `<int:transformer>`. Transforms entries (`com.rometools.rome.feed.synd.SyndEntry`) in the "news" channel, extracting the entry’s title (`payload.title`) and link (`payload.link`) and concatenating them into a readable `String` (adding a newline). The `String` is then sent to the output channel named "file".
* `<file:outbound-channel-adapter>`. An outbound channel adapter that writes content from its channel (here named "file") to a file. Specifically, as configured here, it will append anything in the "file" channel to a file at `/tmp/si/SpringBlog`.

This simple flow is illustrated like this:

![](http://spring.io/guides/gs/integration/images/blogToFile.png)

Ignore the `auto-startup` attribute for now; we’ll revisit that later when discussing testing; just notice that it will be `true` by default which means the posts will be fetched when the application starts. Also note the property placeholder in the `filename-generator-expression`; this means the default will be `SpringBlog` but can be overridden with a property

## Make the application executable
Although it is common to configure a Spring Integration flow within a larger application, perhaps even a web application, there’s no reason that it can’t be defined in a simpler standalone application. That’s what you do next, creating a main class that kicks off the integration flow and also declares a handful of beans to support the integration flow. You also build the application into a standalone executable JAR file. We use Spring Boot’s `SpringApplication` to create the application context. Since this guide uses an the XML namespace for the integration flow, notice that we use `@ImportResource` to load it into the application context.

`src/main/java/hello/Application.java`

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.annotation.ImportResource;

@SpringBootApplication
@ImportResource("/hello/integration.xml")
public class Application {
    public static void main(String[] args) throws Exception {
        ConfigurableApplicationContext ctx = new SpringApplication(Application.class).run(args);
        System.out.println("Hit Enter to terminate");
        System.in.read();
        ctx.close();
    }

}
```

## Build an executable JAR
You can run the application from the command line with Gradle or Maven. Or you can build a single executable JAR file that contains all the necessary dependencies, classes, and resources, and run that. This makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.

If you are using Gradle, you can run the application using `./gradlew bootRun`. Or you can build the JAR file using `./gradlew build`. Then you can run the JAR file:

```
java -jar build/libs/gs-integration-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-integration-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/) instead.

## Run the application
Now you can run the application from the jar:

```
java -jar build/libs/{project_id}-0.1.0.jar

... app starts up ...
```

Once the application starts up, it connects to the RSS feed and starts fetching blog posts. The application processes those posts through the integration flow you defined, ultimately appending the post information to a file at `/tmp/si/SpringBlog`.

After the application has been running for awhile, you should be able to view the file at `/tmp/si/SpringBlog` to see the data from a handful of posts. On a UNIX-based operating system, you can also choose to tail the file to see the results as they are written:

```
tail -f /tmp/si/SpringBlog
```

You should see something like this (the actual news will differ):

```
Spring Integration Java DSL 1.0 GA Released @ https://spring.io/blog/2014/11/24/spring-integration-java-dsl-1-0-ga-released
This Week in Spring - November 25th, 2014 @ https://spring.io/blog/2014/11/25/this-week-in-spring-november-25th-2014
Spring Integration Java DSL: Line by line tutorial @ https://spring.io/blog/2014/11/25/spring-integration-java-dsl-line-by-line-tutorial
Spring for Apache Hadoop 2.1.0.M2 Released @ https://spring.io/blog/2014/11/14/spring-for-apache-hadoop-2-1-0-m2-released
```

## Testing
Examine the `complete` project and you will see a test case.

`src/test/java/hello/FlowTests.java`

```java
package hello;

import static org.assertj.core.api.Assertions.assertThat;

import java.io.BufferedReader;
import java.io.File;
import java.io.FileReader;

import org.junit.Test;
import org.junit.runner.RunWith;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.integration.endpoint.SourcePollingChannelAdapter;
import org.springframework.integration.support.MessageBuilder;
import org.springframework.messaging.MessageChannel;
import org.springframework.test.context.junit4.SpringRunner;

import com.rometools.rome.feed.synd.SyndEntryImpl;

@RunWith(SpringRunner.class)
@SpringBootTest({ "auto.startup=false",      // we don't want to start the real feed
                  "feed.file.name=Test" })   // use a different file
public class FlowTests {

	@Autowired
	private SourcePollingChannelAdapter newsAdapter;

	@Autowired
	private MessageChannel news;

	@Test
	public void test() throws Exception {
		assertThat(this.newsAdapter.isRunning()).isFalse();
		SyndEntryImpl syndEntry = new SyndEntryImpl();
		syndEntry.setTitle("Test Title");
		syndEntry.setLink("http://foo/bar");
		File out = new File("/tmp/si/Test");
		out.delete();
		assertThat(out.exists()).isFalse();
		this.news.send(MessageBuilder.withPayload(syndEntry).build());
		assertThat(out.exists()).isTrue();
		BufferedReader br = new BufferedReader(new FileReader(out));
		String line = br.readLine();
		assertThat(line).isEqualTo("Test Title @ http://foo/bar");
		br.close();
		out.delete();
	}

}
```

This uses Spring Boot’s test support to set a property `auto.startup` to `false`. It is generally not a good idea to rely on a network connection for tests, especially in a CI environment. So, instead, we prevent the feed adapter from starting and inject a `SyndEntry` into the `news` channel for processing by the rest of the flow. The test also sets the `feed.file.name` so the test writes to a different file; then:

* verifies the adapter is stopped
* creates a test `SyndEntry`
* deletes the test output file (if it’s present)
* sends the message
* verifies the file exists
* reads the file and verifies that the data is as expected

## Summary
Congratulations! You have developed a simple application that uses Spring Integration to fetch blog posts from spring.io, process them, and write them to a file.

## See Also
The following guide may also be helpful:

* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)