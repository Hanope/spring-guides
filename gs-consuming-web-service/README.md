## Consuming a SOAP web service
This guide walks you through the process of consuming a [SOAP-based web service](http://spring.io/understanding/SOAP) with Spring.

## What you’ll build
You will build a client that fetches country data data from a remote, WSDL-based web service using [SOAP](https://en.wikipedia.org/wiki/SOAP). You can find out more about the country service, and run the service yourself by following [this guide](https://spring.io/guides/gs/producing-web-service/).

The service provides country data. You will be able to query data about a country based on its name.

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

* [Download](https://github.com/spring-guides/gs-consuming-web-service/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-consuming-web-service.git`
* cd into `gs-consuming-web-service/initial`
* Jump ahead to [Generate domain objects based on a WSDL](#generate-domain-objects-based-on-a-wsdl).

**When you’re finished**, you can check your results against the code in `gs-consuming-web-service/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-consuming-web-service/blob/master/initial/build.gradle).

`build.gradle`

```gradle
configurations {
    jaxb
}

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

repositories {
    mavenCentral()
}

// tag::wsdl[]
task genJaxb {
    ext.sourcesDir = "${buildDir}/generated-sources/jaxb"
    ext.classesDir = "${buildDir}/classes/jaxb"
    ext.schema = "http://localhost:8080/ws/countries.wsdl"

    outputs.dir classesDir

    doLast() {
        project.ant {
            taskdef name: "xjc", classname: "com.sun.tools.xjc.XJCTask",
                    classpath: configurations.jaxb.asPath
            mkdir(dir: sourcesDir)
            mkdir(dir: classesDir)

            xjc(destdir: sourcesDir, schema: schema,
                    package: "hello.wsdl") {
                arg(value: "-wsdl")
                produces(dir: sourcesDir, includes: "**/*.java")
            }

            javac(destdir: classesDir, source: 1.8, target: 1.8, debug: true,
                    debugLevel: "lines,vars,source",
                    classpath: configurations.jaxb.asPath) {
                src(path: sourcesDir)
                include(name: "**/*.java")
                include(name: "*.java")
            }

            copy(todir: classesDir) {
                fileset(dir: sourcesDir, erroronmissingdir: false) {
                    exclude(name: "**/*.java")
                }
            }
        }
    }
}
// end::wsdl[]

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter")
    compile("org.springframework.ws:spring-ws-core")
    compile(files(genJaxb.classesDir).builtBy(genJaxb))

    jaxb "com.sun.xml.bind:jaxb-xjc:2.1.7"
}

bootJar {
    baseName = 'gs-consuming-web-service'
    version =  '0.1.0'

    from genJaxb.classesDir
}


task afterEclipseImport {
    dependsOn genJaxb
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
    <artifactId>gs-consuming-web-service</artifactId>
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
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
           <dependency>
               <groupId>org.springframework.ws</groupId>
               <artifactId>spring-ws-core</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <!-- tag::wsdl[] -->
            <plugin>
                <groupId>org.jvnet.jaxb2.maven2</groupId>
                <artifactId>maven-jaxb2-plugin</artifactId>
                <version>0.12.3</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>generate</goal>
                        </goals>
                    </execution>
                </executions>
                <configuration>
                    <schemaLanguage>WSDL</schemaLanguage>
                    <generatePackage>hello.wsdl</generatePackage>
                    <schemas>
                        <schema>
                            <url>http://localhost:8080/ws/countries.wsdl</url>
                        </schema>
                    </schemas>
                </configuration>
            </plugin>
            <!-- end::wsdl[] -->
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

## Run the target web service locally
Follow the steps in the [companion guide](https://spring.io/guides/gs/producing-web-service/), or just clone the [repository](https://github.com/spring-guides/gs-producing-web-service) and run the service (e.g. using `mvn spring-boot:run`) from its `complete` directory. You can verify that is working by visiting http://localhost:8080/ws/countries.wsdl in your browser.

## Generate domain objects based on a WSDL
The interface to a SOAP web service is captured in a [WSDL](https://en.wikipedia.org/wiki/Web_Services_Description_Language). JAXB provides an easy means to generate Java classes from a WSDL (or rather: the XSD contained in the `<Types/>` section of the WSDL). The WSDL for the country service can be found at http://localhost:8080/ws/countries.wsdl.

To generate Java classes from the WSDL in maven, you need the following plugin setup:

```xml
<plugin>
    <groupId>org.jvnet.jaxb2.maven2</groupId>
    <artifactId>maven-jaxb2-plugin</artifactId>
    <version>0.13.1</version>
    <executions>
        <execution>
            <goals>
                <goal>generate</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <schemaLanguage>WSDL</schemaLanguage>
        <generatePackage>hello.wsdl</generatePackage>
        <schemas>
            <schema>
                <url>http://localhost:8080/ws/countries.wsdl</url>
            </schema>
        </schemas>
    </configuration>
</plugin>
```

This setup will generate classes for the WSDL found at the specified URL, putting those classes in the `hello.wsdl` package.

To do the same with gradle, you will need the following in your build file:

```gradle
task genJaxb {
    ext.sourcesDir = "${buildDir}/generated-sources/jaxb"
    ext.classesDir = "${buildDir}/classes/jaxb"
    ext.schema = "http://localhost:8080/ws/countries.wsdl"

    outputs.dir classesDir

    doLast() {
        project.ant {
            taskdef name: "xjc", classname: "com.sun.tools.xjc.XJCTask",
                    classpath: configurations.jaxb.asPath
            mkdir(dir: sourcesDir)
            mkdir(dir: classesDir)

            xjc(destdir: sourcesDir, schema: schema,
                    package: "hello.wsdl") {
                arg(value: "-wsdl")
                produces(dir: sourcesDir, includes: "**/*.java")
            }

            javac(destdir: classesDir, source: 1.8, target: 1.8, debug: true,
                    debugLevel: "lines,vars,source",
                    classpath: configurations.jaxb.asPath) {
                src(path: sourcesDir)
                include(name: "**/*.java")
                include(name: "*.java")
            }

            copy(todir: classesDir) {
                fileset(dir: sourcesDir, erroronmissingdir: false) {
                    exclude(name: "**/*.java")
                }
            }
        }
    }
}
```

As gradle does not have a JAXB plugin (yet), it involves an ant task, which makes it a bit more complex than in maven.

In both cases, the JAXB domain object generation process has been wired into the build tool’s lifecycle so there are no extra steps to run.

## Create a country service client
To create a web service client, you simply have to extend the [WebServiceGatewaySupport](https://docs.spring.io/spring-ws/sites/2.0/apidocs/org/springframework/ws/client/core/support/WebServiceGatewaySupport.html) class and code your operations:

`src/main/java/hello/CountryClient.java`

```java
package hello;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.springframework.ws.client.core.support.WebServiceGatewaySupport;
import org.springframework.ws.soap.client.core.SoapActionCallback;

import hello.wsdl.GetCountryRequest;
import hello.wsdl.GetCountryResponse;

public class CountryClient extends WebServiceGatewaySupport {

	private static final Logger log = LoggerFactory.getLogger(CountryClient.class);

	public GetCountryResponse getCountry(String country) {

		GetCountryRequest request = new GetCountryRequest();
		request.setName(country);

		log.info("Requesting location for " + country);

		GetCountryResponse response = (GetCountryResponse) getWebServiceTemplate()
				.marshalSendAndReceive("http://localhost:8080/ws/countries", request,
						new SoapActionCallback(
								"http://spring.io/guides/gs-producing-web-service/GetCountryRequest"));

		return response;
	}

}
```

The client contains one method: `getCountry` which does the actual SOAP exchange.

In this method, both the `GetCountryRequest` and the `GetCountryResponse` classes are derived from the WSDL and were generated in the JAXB generation process described in the previous step. It creates the `GetCountryRequest` request object and sets it up with the `country` parameter (the name of the country). After printing out the country name, it uses the [WebServiceTemplate](https://docs.spring.io/spring-ws/sites/2.0/apidocs/org/springframework/ws/client/core/WebServiceTemplate.html) supplied by the `WebServiceGatewaySupport` base class to do the actual SOAP exchange. It passes the `GetCountryRequest` request object, as well as a `SoapActionCallback` to pass on a [SOAPAction](https://www.w3.org/TR/2000/NOTE-SOAP-20000508/#_Toc478383528) header with the request, as the WSDL described that it needed this header in the `<soap:operation/>` elements. It casts the response into a `GetCountryResponse` object, which is then returned.

## Configuring web service components
Spring WS uses Spring Framework’s OXM module which has the `Jaxb2Marshaller` to serialize and deserialize XML requests.

`src/main/java/hello/CountryConfiguration.java`

```java
package hello;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.oxm.jaxb.Jaxb2Marshaller;

@Configuration
public class CountryConfiguration {

	@Bean
	public Jaxb2Marshaller marshaller() {
		Jaxb2Marshaller marshaller = new Jaxb2Marshaller();
		// this package must match the package in the <generatePackage> specified in
		// pom.xml
		marshaller.setContextPath("hello.wsdl");
		return marshaller;
	}

	@Bean
	public CountryClient countryClient(Jaxb2Marshaller marshaller) {
		CountryClient client = new CountryClient();
		client.setDefaultUri("http://localhost:8080/ws");
		client.setMarshaller(marshaller);
		client.setUnmarshaller(marshaller);
		return client;
	}

}
```

The `marshaller` is pointed at the collection of generated domain objects and will use them to both serialize and deserialize between XML and [POJOs](http://spring.io/understanding/POJO).

The `countryClient` is created and configured with the URI of the country service shown up above. It is also configured to use the JAXB marshaller.

## Make the application executable
This application is packaged up to run from the console and retrieve the data for a given country name:

`src/main/java/hello/Application.java`

```java
package hello;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

import hello.wsdl.GetCountryResponse;

@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

	@Bean
	CommandLineRunner lookup(CountryClient quoteClient) {
		return args -> {
			String country = "Spain";

			if (args.length > 0) {
				country = args[0];
			}
			GetCountryResponse response = quoteClient.getCountry(country);
			System.err.println(response.getCountry().getCurrency());
		};
	}

}
```

The `main()` method defers to the `SpringApplication` helper class, providing `CountryConfiguration.class` as an argument to its `run()` method. This tells Spring to read the annotation metadata from `CountryConfiguration` and to manage it as a component in the [Spring application context](http://spring.io/understanding/application-context).

> This application is hard coded to look up symbol 'MSFT' which correspond to Microsoft Corporation. Towards the end of this guide, you’ll see how to plug in a different symbol without editing the code.

## Build an executable JAR
You can run the application from the command line with Gradle or Maven. Or you can build a single executable JAR file that contains all the necessary dependencies, classes, and resources, and run that. This makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.

If you are using Gradle, you can run the application using `./gradlew bootRun`. Or you can build the JAR file using `./gradlew build`. Then you can run the JAR file:

```
java -jar build/libs/gs-consuming-web-service-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-consuming-web-service-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file instead](http://spring.io/guides/gs/convert-jar-to-war/).

Logging output is displayed. The service should be up and running within a few seconds.

```
Requesting country data for Spain

<getCountryRequest><name>Spain</name>...</getCountryRequest>
```

You can plug in a different country by typing `java -jar build/libs/gs-consuming-web-service-0.1.0.jar Poland`

```
Requesting location for Poland

<getCountryRequest><name>Poland</name>...</getCountryRequest>
```

## Summary
Congratulations! You’ve just developed a client to consume a SOAP-based web service with Spring.

## See Also
The following guides may also be helpful:

* [Producing a SOAP web service](https://spring.io/guides/gs/producing-web-service/)
* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)