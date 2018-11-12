## Messaging with Google Cloud Pub/Sub
This guide walks you through the process of exchanging messages between different parts of a program, or different programs, using [Spring Integration channel adapters](https://docs.spring.io/spring-integration/reference/htmlsingle/#overview-endpoints-channeladapter) and [Google Cloud Pub/Sub](https://cloud.google.com/pubsub/) as the underlying message exchange mechanism.

## What you’ll build
A [Spring Boot](https://spring.io/guides/gs/spring-boot/) web application that sends messages to itself and processes those messages.

## What you’ll need
* About 15 minutes
* A favorite text editor or IDE
* [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) or later
* [Gradle 4+](http://www.gradle.org/downloads) or [Maven 3.2+](https://maven.apache.org/download.cgi)
* You can also import the code straight into your IDE:
  - [Spring Tool Suite (STS)](http://spring.io/guides/gs/sts)
  - [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea/)

* [A Google Cloud Platform project with billing and Pub/Sub enabled](https://cloud.google.com/pubsub/docs/quickstart-console)
* [Google Cloud SDK](https://cloud.google.com/sdk/)

## How to complete this guide
Like most Spring [Getting Started guides](http://spring.io/guides), you can start from scratch and complete each step, or you can bypass basic setup steps that are already familiar to you. Either way, you end up with working code.

To **start from scratch**, move on to [Build with Gradle](#build-with-gradle).

To **skip the basics**, do the following:

* [Download](https://github.com/spring-guides/gs-messaging-gcp-pubsub/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-messaging-gcp-pubsub.git`
* cd into `gs-messaging-gcp-pubsub/initial`
* Jump ahead to [Add required dependencies](#add-required-dependencies).

**When you’re finished**, you can check your results against the code in `gs-messaging-gcp-pubsub/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-messaging-gcp-pubsub/blob/master/initial/build.gradle).

`build.gradle`

```gradle
buildscript {
    repositories {
        mavenCentral()
        maven {
            url "https://repo.spring.io/libs-milestone"
        }
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
    baseName = 'gs-spring-cloud-gcp'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    testCompile("org.springframework.boot:spring-boot-starter-test")
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
    <artifactId>gs-spring-cloud-gcp</artifactId>
    <version>0.1.0</version>

    <properties>
        <java.version>1.8</java.version>
        <spring-boot-release.version>2.0.5.RELEASE</spring-boot-release.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot-release.version}</version>
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
            <url>https://repo.spring.io/milestone</url>
        </repository>
    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
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

## Add required dependencies
At the time of writing, the Spring Cloud GCP libraries are in Beta stage. As such, they are published to Spring Milestones Maven repository.

Add the following to your ₩ file if you’re using Maven:

```xml
<dependencies>
    ...
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-gcp-starter-pubsub</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.integration</groupId>
        <artifactId>spring-integration-core</artifactId>
    </dependency>
    ...
</dependencies>

<repositories>
    ...
    <repository>
        <id>spring-milestones</id>
        <name>Spring Milestones</name>
        <url>https://repo.spring.io/libs-milestone</url>
    </repository>
    ...
</repositories>
```

Or, if you’re using Gradle:

```gradle
repositories {
    ...
    maven {
        url "http://repo.spring.io/libs-milestone"
    }
    ...
}

dependencies {
    ...
    compile("org.springframework.cloud:spring-cloud-gcp-starter-pubsub:1.0.0.M3")
    compile("org.springframework.integration:spring-integration-core")
    ...
}
```

If you’re using Maven, you are also strongly encouraged to use the Spring Cloud GCP bill of materials to control the versions of your dependencies:

```xml
<properties>
    ...
    <spring-cloud-gcp.version>1.0.0.M3</spring-cloud-gcp.version>
    ...
</properties>

<dependencyManagement>
    <dependencies>
       ...
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-gcp-dependencies</artifactId>
            <version>${spring-cloud-gcp.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        ...
    </dependencies>
</dependencyManagement>
```

## Set up Google Cloud Pub/Sub environment
You will need a topic and a subscription to send and receive messages from Google Cloud Pub/Sub. You can create them in the [Google Cloud Console](https://console.cloud.google.com/cloudpubsub) or, programatically, with the `PubSubAdmin` class.

For this exercise, create a topic called "testTopic" and a subscription for that topic called "testSubscription".

## Create application files
You’ll need a class to include the channel adapter and messaging configuration. Create a PubSubApplication class with the @SpringBootApplication header, as is typical with a Spring Boot application.

`src/main/java/hello/PubSubApplication.java`

```java
@SpringBootApplication
public class PubSubApplication {

  public static void main(String[] args) throws IOException {
    SpringApplication.run(PubSubApplication.class, args);
  }

}
```

Additionally, since you’re building a web application, create a WebAppController class to separate between the controller and configuration logic.

`src/main/java/hello/WebAppController.java`

```java
@RestController
public class WebAppController {
}
```

We’re still missing two files for HTML and properties.

`src/main/resources/static/index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Spring Integration GCP sample</title>
</head>
<body>
<div name="formDiv">
  <form action="/publishMessage" method="post">
    Publish message: <input type="text" name="message" /> <input type="submit" value="Publish!"/>
  </form>
</div>
</body>
</html>
```

`src/main/resources/application.properties`

```properties
#spring.cloud.gcp.project-id=[YOUR_GCP_PROJECT_ID_HERE]
#spring.cloud.gcp.credentials.location=file:[LOCAL_FS_CREDENTIALS_PATH]
```

The Spring Cloud GCP Core Boot starter can auto-configure these two properties and make them optional. Properties from the properties file always have precedence over the Spring Boot configuration. The Spring Cloud GCP Core Boot starter is bundled with the Spring Cloud GCP Pub/Sub Boot starter.

The GCP project ID is auto-configured from the `GOOGLE_CLOUD_PROJECT` environment variable, among [several other sources](https://github.com/GoogleCloudPlatform/google-cloud-java/blob/master/google-cloud-core/src/main/java/com/google/cloud/ServiceOptions.java#L287). The OAuth2 credentials are auto-configured from the [GOOGLE_APPLICATION_CREDENTIALS](https://developers.google.com/identity/protocols/application-default-credentials) environment variable. If the [Google Cloud SDK](https://cloud.google.com/sdk/) is installed, this environment variable is easily configured by running the `gcloud auth application-default login` command in the same process of the app, or a parent one.

## Create an inbound channel adapter
An inbound channel adapter listens to messages from a Google Cloud Pub/Sub subscription and sends them to a Spring channel in an application.

Instantiating an inbound channel adapter requires a `PubSubOperations` instance and the name of an existing subscription. `PubSubOperations` is Spring’s abstraction to subscribe to Google Cloud Pub/Sub topics. The Spring Cloud GCP Pub/Sub Boot starter provides an auto-configured `PubSubOperations` instance which you can simply inject as a method argument.

`src/main/java/hello/PubSubApplication.java`

```java
  @Bean
  public PubSubInboundChannelAdapter messageChannelAdapter(
      @Qualifier("pubsubInputChannel") MessageChannel inputChannel,
      PubSubOperations pubSubTemplate) {
    PubSubInboundChannelAdapter adapter =
        new PubSubInboundChannelAdapter(pubSubTemplate, "testSubscription");
    adapter.setOutputChannel(inputChannel);
    adapter.setAckMode(AckMode.MANUAL);

    return adapter;
  }
```

The message acknowledgement mode is set in the adapter to automatic, by default. This behaviour may be overridden, as shown in the example.

After the channel adapter is instantiated, an output channel where the adapter sends the received messages to must be configured.

`src/main/java/hello/PubSubApplication.java`

```java
  @Bean
  public MessageChannel pubsubInputChannel() {
    return new DirectChannel();
  }
```

Attached to an inbound channel is a service activator which is used to process incoming messages.

`src/main/java/hello/PubSubApplication.java`

```java
  @Bean
  @ServiceActivator(inputChannel = "pubsubInputChannel")
  public MessageHandler messageReceiver() {
    return message -> {
      LOGGER.info("Message arrived! Payload: " + message.getPayload());
      AckReplyConsumer consumer =
          (AckReplyConsumer) message.getHeaders().get(GcpPubSubHeaders.ACKNOWLEDGEMENT);
      consumer.ack();
    };
  }
```

The `ServiceActivator` input channel name (e.g., `"pubsubInputChannel"`) must match the input channel method name. Whenever a new message arrives to that channel, it is processed by the returned `MessageHandler`.

In this example, the message is processed simply by logging its body and acknowledging it. In manual acknowledgement, a message is acknowledged using the `AckReplyConsumer` object, which is sent in the message headers.

## Create an outbound channel adapter
An outbound channel adapter listens to new messages from a Spring channel and publishes them to a Google Cloud Pub/Sub topic.

Instantiating an outbound channel adapter requires a `PubSubOperations` and the name of an existing topic. `PubSubOperations` is Spring’s abstraction to publish messages to Google Cloud Pub/Sub topics. The Spring Cloud GCP Pub/Sub Boot starter provides an auto-configured `PubSubOperations` instance.

`src/main/java/hello/PubSubApplication.java`

```java
  @Bean
  @ServiceActivator(inputChannel = "pubsubOutputChannel")
  public MessageHandler messageSender(PubSubOperations pubsubTemplate) {
    return new PubSubMessageHandler(pubsubTemplate, "testTopic");
  }
```

You can use a `MessageGateway` to write messages to a channel and publish them to Google Cloud Pub/Sub.

`src/main/java/hello/PubSubApplication.java`

```java
  @MessagingGateway(defaultRequestChannel = "pubsubOutputChannel")
  public interface PubsubOutboundGateway {

    void sendToPubsub(String text);
  }
```

From this code, Spring auto-generates an object that can then be autowired into a private field in the application.

`src/main/java/hello/WebAppController.java`

```java
  @Autowired
  private PubsubOutboundGateway messagingGateway;
```

## Add controller logic
Add logic to your controller that lets you write to a Spring channel:

`src/main/java/hello/WebAppController.java`

```java
package hello;

import hello.PubSubApplication.PubsubOutboundGateway;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.view.RedirectView;

@RestController
public class WebAppController {

  // tag::autowireGateway[]
  @Autowired
  private PubsubOutboundGateway messagingGateway;
  // end::autowireGateway[]

  @PostMapping("/publishMessage")
  public RedirectView publishMessage(@RequestParam("message") String message) {
    messagingGateway.sendToPubsub(message);
    return new RedirectView("/");
  }
}
```

## Authentication
Your application must be authenticated either via the GOOGLE_APPLICATION_CREDENTIALS environment variable or the `spring.cloud.gcp.credentials.location` property.

If you have the [Google Cloud SDK](https://cloud.google.com/sdk/) installed, you can log in with your user account using the gcloud auth application-default login command.

Alternatively, you can download a service account credentials file from the [Google Cloud Console](https://cloud.google.com/console) and point the `spring.cloud.gcp.credentials.location` property in the `application.properties` file to it.

As a [Spring Resource](https://docs.spring.io/spring/docs/3.2.x/spring-framework-reference/html/resources.html), the `spring.cloud.gcp.credentials.location` can also be obtained from places other than the file system, like a URL, classpath, etc.

## Make the application executable
Although it is possible to package this service as a traditional [WAR](http://spring.io/understanding/WAR) file for deployment to an external application server, the simpler approach demonstrated below creates a standalone application. You package everything in a single, executable JAR file, driven by a Java `main()` method. Also, you use Spring’s support for embedding the [Tomcat](http://spring.io/understanding/Tomcat) servlet container as the HTTP runtime, instead of deploying to an external instance.

`@SpringBootApplication` is a convenience annotation that adds all of the following:

* `@Configuration` tags the class as a source of bean definitions for the application context.
* `@EnableAutoConfiguration` tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings.
* Normally you would add `@EnableWebMvc` for a Spring MVC app, but Spring Boot adds it automatically when it sees **spring-webmvc** on the classpath. This flags the application as a web application and activates key behaviors such as setting up a `DispatcherServlet`.
* `@ComponentScan` tells Spring to look for other components, configurations, and services in the `hello` package, allowing it to find the controllers.

The `main()` method uses Spring Boot’s `SpringApplication.run()` method to launch an application. Did you notice that there wasn’t a single line of XML? No **web.xml** file either. This web application is 100% pure Java and you didn’t have to deal with configuring any plumbing or infrastructure.

## Build an executable JAR
You can run the application from the command line with Gradle or Maven. Or you can build a single executable JAR file that contains all the necessary dependencies, classes, and resources, and run that. This makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.

If you are using Gradle, you can run the application using `./gradlew bootRun`. Or you can build the JAR file using `./gradlew build`. Then you can run the JAR file:

```
java -jar build/libs/gs-messaging-gcp-pubsub-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-messaging-gcp-pubsub-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/) instead.

Logging output is displayed. The service should be up and running within a few seconds.

## Test the application
Now that the application is running, you can test it. Open http://localhost:8080, type a message in the input text box, press the "Publish!" button and verify that the message was correctly logged in your process terminal window.

## Summary
Congratulations! You’ve just developed a Spring application that exchanges messages using Spring Integration GCP Pub/Sub channel adapters!