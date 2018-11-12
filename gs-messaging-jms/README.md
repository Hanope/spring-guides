## Messaging with JMS
This guide walks you through the process of publishing and subscribing to messages using a JMS broker.

## What you’ll build
You’ll build an application that uses Spring’s `JmsTemplate` to post a single message and subscribes to it with a `@JmsListener` annotated method of a managed bean.

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

* [Download](https://github.com/spring-guides/gs-messaging-jms/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-messaging-jms.git`
* cd into `gs-messaging-jms/initial`
* Jump ahead to [Create a message receiver](#create-a-message-receiver).

**When you’re finished**, you can check your results against the code in `gs-messaging-jms/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-messaging-jms/blob/master/initial/build.gradle).

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
    baseName = 'gs-messaging-jms'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-activemq")
    compile("org.apache.activemq:activemq-broker")
    compile("com.fasterxml.jackson.core:jackson-databind")
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
    <artifactId>gs-messaging-jms</artifactId>
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
            <artifactId>spring-boot-starter-activemq</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.activemq</groupId>
            <artifactId>activemq-broker</artifactId>
        </dependency>

        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
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

## Create a message receiver
Spring provides the means to publish messages to any [POJO](http://spring.io/understanding/POJO).

In this guide, you will look at how to send a message out over a JMS message broker. To start things off, let’s create a very simply POJO that embodies the details of an email message. Pay note, we aren’t sending an email message. We’re simply sending the details from one place to another about WHAT to send in a message.

`src/main/java/hello/Email.java`

```java
package hello;

public class Email {

    private String to;
    private String body;

    public Email() {
    }

    public Email(String to, String body) {
        this.to = to;
        this.body = body;
    }

    public String getTo() {
        return to;
    }

    public void setTo(String to) {
        this.to = to;
    }

    public String getBody() {
        return body;
    }

    public void setBody(String body) {
        this.body = body;
    }

    @Override
    public String toString() {
        return String.format("Email{to=%s, body=%s}", getTo(), getBody());
    }

}
```

This POJO is quite simple, containing two fields, **to** and **body**, along with the presumed set of getters and setters.

From here, you can define a message receiver:

`src/main/java/hello/Receiver.java`

```java
package hello;

import org.springframework.jms.annotation.JmsListener;
import org.springframework.stereotype.Component;

@Component
public class Receiver {

    @JmsListener(destination = "mailbox", containerFactory = "myFactory")
    public void receiveMessage(Email email) {
        System.out.println("Received <" + email + ">");
    }

}
```

`Receiver` is also known as a **message driven POJO**. As you can see in the code above, there is no need to implement any particular interface or for the method to have any particular name. Besides, the method may have a [very flexible signature](https://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#jms-annotated-method-signature). Note in particular that this class has no import on the JMS API.

The `JmsListener` annotation defines the name of the `Destination` that this method should listen to and the reference to the `JmsListenerContainerFactory` to use to create the underlying message listener container. Strictly speaking that last attribute is not necessary unless you need to customize the way the container is built as Spring Boot registers a default factory if necessary.

The [reference documentation](https://docs.spring.io/spring/docs/current/spring-framework-reference/integration.html#jms-annotated-method-signature) covers this in more detail.

## Send and receive JMS messages with Spring

Next, wire up a sender and a receiver.

`src/main/java/hello/Application.java`

```java
package hello;

import javax.jms.ConnectionFactory;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jms.DefaultJmsListenerContainerFactoryConfigurer;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.jms.annotation.EnableJms;
import org.springframework.jms.config.DefaultJmsListenerContainerFactory;
import org.springframework.jms.config.JmsListenerContainerFactory;
import org.springframework.jms.core.JmsTemplate;
import org.springframework.jms.support.converter.MappingJackson2MessageConverter;
import org.springframework.jms.support.converter.MessageConverter;
import org.springframework.jms.support.converter.MessageType;

@SpringBootApplication
@EnableJms
public class Application {

    @Bean
    public JmsListenerContainerFactory<?> myFactory(ConnectionFactory connectionFactory,
                                                    DefaultJmsListenerContainerFactoryConfigurer configurer) {
        DefaultJmsListenerContainerFactory factory = new DefaultJmsListenerContainerFactory();
        // This provides all boot's default to this factory, including the message converter
        configurer.configure(factory, connectionFactory);
        // You could still override some of Boot's default if necessary.
        return factory;
    }

    @Bean // Serialize message content to json using TextMessage
    public MessageConverter jacksonJmsMessageConverter() {
        MappingJackson2MessageConverter converter = new MappingJackson2MessageConverter();
        converter.setTargetType(MessageType.TEXT);
        converter.setTypeIdPropertyName("_type");
        return converter;
    }

    public static void main(String[] args) {
        // Launch the application
        ConfigurableApplicationContext context = SpringApplication.run(Application.class, args);

        JmsTemplate jmsTemplate = context.getBean(JmsTemplate.class);

        // Send a message with a POJO - the template reuse the message converter
        System.out.println("Sending an email message.");
        jmsTemplate.convertAndSend("mailbox", new Email("info@example.com", "Hello"));
    }

}
```

`@SpringBootApplication` is a convenience annotation that adds all of the following:

* `@Configuration` tags the class as a source of bean definitions for the application context.
* `@EnableAutoConfiguration` tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings.
* Normally you would add `@EnableWebMvc` for a Spring MVC app, but Spring Boot adds it automatically when it sees **spring-webmvc** on the classpath. This flags the application as a web application and activates key behaviors such as setting up a `DispatcherServlet`.
* `@ComponentScan` tells Spring to look for other components, configurations, and services in the `hello` package, allowing it to find the controllers.

The `main()` method uses Spring Boot’s `SpringApplication.run()` method to launch an application. Did you notice that there wasn’t a single line of XML? No **web.xml** file either. This web application is 100% pure Java and you didn’t have to deal with configuring any plumbing or infrastructure.

`@EnableJms` triggers the discovery of methods annotated with `@JmsListener`, creating the message listener container under the covers.

For clarity, we have also defined a `myFactory` bean that is referenced in the `JmsListener` annotation of the receiver. Because we use the `DefaultJmsListenerContainerFactoryConfigurer` infrastructure provided by Spring Boot, that `JmsMessageListenerContainer` will be identical to the one that boot creates by default.

The default `MessageConverter` is able to convert only basic types (such as `String`, `Map`, `Serializable`) and our `Email` is not `Serializable` on purpose. We want to use Jackson and serialize the content to json in text format (i.e. as a `TextMessage`). Spring Boot will detect the presence of a `MessageConverter` and will associate it to both the default `JmsTemplate` and any `JmsListenerContainerFactory` created by `DefaultJmsListenerContainerFactoryConfigurer`.

`JmsTemplate` makes it very simple to send messages to a JMS destination. In the `main` runner method, after starting things up, you can just use `jmsTemplate` to send an `Email` POJO. Because our custom `MessageConverter` has been automatically associated to it, a json document will be generated in a `TextMessage` only.

Two beans that you don’t see defined are `JmsTemplate` and `ConnectionFactory`. These are created automatically by Spring Boot. In this case, the ActiveMQ broker runs embedded.

By default, Spring Boot creates a `JmsTemplate` configured to [transmit to queues](https://docs.spring.io/spring/docs/current/spring-framework-reference/integration.html#jms-destinations) by having **pubSubDomain** set to false. The `JmsMessageListenerContainer` is also configured the same. To override, set `spring.jms.isPubSubDomain=true` via Boot’s property settings (either inside `application.properties` or by environment variable). Then make sure the receiving container has the same setting.

> Spring’s `JmsTemplate` can receive messages directly through its `receive` method, but that only works synchronously, meaning it will block. That’s why we recommend that you use a listener container such as `DefaultMessageListenerContainer` with a cache-based connection factory, so you can consume messages asynchronously and with maximum connection efficiency.

## Build an executable JAR
You can run the application from the command line with Gradle or Maven. Or you can build a single executable JAR file that contains all the necessary dependencies, classes, and resources, and run that. This makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.

If you are using Gradle, you can run the application using `./gradlew bootRun`. Or you can build the JAR file using `./gradlew build`. Then you can run the JAR file:

```
java -jar build/libs/gs-messaging-jms-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-messaging-jms-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/) instead.

When it runs, buried amidst all the logging, you should see these messages:

```
Sending an email message.
Received <Email{to=info@example.com, body=Hello}>
```

## Summary
Congratulations! You’ve just developed a publisher and consumer of JMS-based messages.