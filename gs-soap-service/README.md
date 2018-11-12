## Producing a SOAP web service
This guide walks you through the process of creating a SOAP-based web service server with Spring.

## What you’ll build
You will build a server that exposes data from various European countries using a WSDL-based SOAP web service.

> To simplify the example, you will use hardcoded data for the United Kingdom, Spain and Poland.

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

* [Download](https://github.com/spring-guides/gs-soap-service/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-soap-service.git`
* cd into `gs-soap-service/initial`
* Jump ahead to [Add Spring-WS dependency](#add-spring-ws-dependency).

**When you’re finished**, you can check your results against the code in `gs-soap-service/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-soap-service/blob/master/initial/build.gradle).

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
    baseName = 'gs-producing-web-service'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
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
    <artifactId>gs-producting-web-service</artifactId>
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

## Add Spring-WS dependency
The project you create needs to include `spring-ws-core` as a dependency in your build file as well as wsdl4j.

For maven:

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web-services</artifactId>
</dependency>
<dependency>
	<groupId>wsdl4j</groupId>
	<artifactId>wsdl4j</artifactId>
</dependency>
```

For gradle:

```gradle
sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web-services")
    testCompile("org.springframework.boot:spring-boot-starter-test")
    compile("wsdl4j:wsdl4j:1.6.1")
    jaxb("org.glassfish.jaxb:jaxb-xjc:2.2.11")
    compile(files(genJaxb.classesDir).builtBy(genJaxb))
}
```

## Create an XML schema to define the domain
The web service domain is defined in an XML schema file (XSD) that Spring-WS will export automatically as a WSDL.

Create an XSD file with operations to return a country’s **name**, **population**, **capital** and **currency**:

`src/main/resources/countries.xsd`

```xsd
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:tns="http://spring.io/guides/gs-producing-web-service"
           targetNamespace="http://spring.io/guides/gs-producing-web-service" elementFormDefault="qualified">

    <xs:element name="getCountryRequest">
        <xs:complexType>
            <xs:sequence>
                <xs:element name="name" type="xs:string"/>
            </xs:sequence>
        </xs:complexType>
    </xs:element>

    <xs:element name="getCountryResponse">
        <xs:complexType>
            <xs:sequence>
                <xs:element name="country" type="tns:country"/>
            </xs:sequence>
        </xs:complexType>
    </xs:element>

    <xs:complexType name="country">
        <xs:sequence>
            <xs:element name="name" type="xs:string"/>
            <xs:element name="population" type="xs:int"/>
            <xs:element name="capital" type="xs:string"/>
            <xs:element name="currency" type="tns:currency"/>
        </xs:sequence>
    </xs:complexType>

    <xs:simpleType name="currency">
        <xs:restriction base="xs:string">
            <xs:enumeration value="GBP"/>
            <xs:enumeration value="EUR"/>
            <xs:enumeration value="PLN"/>
        </xs:restriction>
    </xs:simpleType>
</xs:schema>
```

## Generate domain classes based on an XML schema

The next step is to generate Java classes from the XSD file. The right approach is do this automatically during build time using a maven or gradle plugin.

Plugin configuration for maven:

```xml
<plugin>
	<groupId>org.codehaus.mojo</groupId>
	<artifactId>jaxb2-maven-plugin</artifactId>
	<version>1.6</version>
	<executions>
		<execution>
			<id>xjc</id>
			<goals>
				<goal>xjc</goal>
			</goals>
		</execution>
	</executions>
	<configuration>
		<schemaDirectory>${project.basedir}/src/main/resources/</schemaDirectory>
		<outputDirectory>${project.basedir}/src/main/java</outputDirectory>
		<clearOutputDir>false</clearOutputDir>
	</configuration>
</plugin>
```

Generated classes are placed in `target/generated-sources/jaxb/` directory.

To do the same with gradle, first you need to configure JAXB in your build file:

```gradle
configurations {
    jaxb
}

bootJar {
    baseName = 'gs-producing-web-service'
    version =  '0.1.0'
    from genJaxb.classesDir
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web-services")
    testCompile("org.springframework.boot:spring-boot-starter-test")
    compile("wsdl4j:wsdl4j:1.6.1")
    jaxb("org.glassfish.jaxb:jaxb-xjc:2.2.11")
    compile(files(genJaxb.classesDir).builtBy(genJaxb))
}
```

> The build file above has `tag` and `end` comments. This is to make it easier to extract bits of it into this guide for more detailed explanation. These comments aren’t needed in your own build file.

Next step is to add task `genJaxb` used by gradle to generate Java classes:

```gradle
task genJaxb {
    ext.sourcesDir = "${buildDir}/generated-sources/jaxb"
    ext.classesDir = "${buildDir}/classes/jaxb"
    ext.schema = "src/main/resources/countries.xsd"

    outputs.dir classesDir

    doLast() {
        project.ant {
            taskdef name: "xjc", classname: "com.sun.tools.xjc.XJCTask",
                    classpath: configurations.jaxb.asPath
            mkdir(dir: sourcesDir)
            mkdir(dir: classesDir)

            xjc(destdir: sourcesDir, schema: schema) {
                arg(value: "-wsdl")
                produces(dir: sourcesDir, includes: "**/*.java")
            }

            javac(destdir: classesDir, source: 1.6, target: 1.6, debug: true,
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

## Create country repository
In order to provide data to the web service, create a country repository. In this guide you create a dummy country repository implementation with hardcoded data.

```java
package hello;

import javax.annotation.PostConstruct;
import java.util.HashMap;
import java.util.Map;

import io.spring.guides.gs_producing_web_service.Country;
import io.spring.guides.gs_producing_web_service.Currency;
import org.springframework.stereotype.Component;
import org.springframework.util.Assert;

@Component
public class CountryRepository {
	private static final Map<String, Country> countries = new HashMap<>();

	@PostConstruct
	public void initData() {
		Country spain = new Country();
		spain.setName("Spain");
		spain.setCapital("Madrid");
		spain.setCurrency(Currency.EUR);
		spain.setPopulation(46704314);

		countries.put(spain.getName(), spain);

		Country poland = new Country();
		poland.setName("Poland");
		poland.setCapital("Warsaw");
		poland.setCurrency(Currency.PLN);
		poland.setPopulation(38186860);

		countries.put(poland.getName(), poland);

		Country uk = new Country();
		uk.setName("United Kingdom");
		uk.setCapital("London");
		uk.setCurrency(Currency.GBP);
		uk.setPopulation(63705000);

		countries.put(uk.getName(), uk);
	}

	public Country findCountry(String name) {
		Assert.notNull(name, "The country's name must not be null");
		return countries.get(name);
	}
}
```

## Create country service endpoint
To create a service endpoint, you only need a [POJO](http://spring.io/understanding/POJO) with a few Spring WS annotations to handle the incoming SOAP requests.

```java
package hello;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.ws.server.endpoint.annotation.Endpoint;
import org.springframework.ws.server.endpoint.annotation.PayloadRoot;
import org.springframework.ws.server.endpoint.annotation.RequestPayload;
import org.springframework.ws.server.endpoint.annotation.ResponsePayload;

import io.spring.guides.gs_producing_web_service.GetCountryRequest;
import io.spring.guides.gs_producing_web_service.GetCountryResponse;

@Endpoint
public class CountryEndpoint {
	private static final String NAMESPACE_URI = "http://spring.io/guides/gs-producing-web-service";

	private CountryRepository countryRepository;

	@Autowired
	public CountryEndpoint(CountryRepository countryRepository) {
		this.countryRepository = countryRepository;
	}

	@PayloadRoot(namespace = NAMESPACE_URI, localPart = "getCountryRequest")
	@ResponsePayload
	public GetCountryResponse getCountry(@RequestPayload GetCountryRequest request) {
		GetCountryResponse response = new GetCountryResponse();
		response.setCountry(countryRepository.findCountry(request.getName()));

		return response;
	}
}
```

`@Endpoint` registers the class with Spring WS as a potential candidate for processing incoming SOAP messages.

`@PayloadRoot` is then used by Spring WS to pick the handler method based on the message’s **namespace** and **localPart**.

`@RequestPayload` indicates that the incoming message will be mapped to the method’s `request` parameter.

The `@ResponsePayload` annotation makes Spring WS map the returned value to the response payload.

> In all of these chunks of code, the `io.spring.guides` classes will report compile-time errors in your IDE unless you have run the task to generate the domain classes based on the WSDL.

## Configure web service beans
Create a new class with Spring WS related beans configuration:

```java
package hello;

import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.ClassPathResource;
import org.springframework.ws.config.annotation.EnableWs;
import org.springframework.ws.config.annotation.WsConfigurerAdapter;
import org.springframework.ws.transport.http.MessageDispatcherServlet;
import org.springframework.ws.wsdl.wsdl11.DefaultWsdl11Definition;
import org.springframework.xml.xsd.SimpleXsdSchema;
import org.springframework.xml.xsd.XsdSchema;

@EnableWs
@Configuration
public class WebServiceConfig extends WsConfigurerAdapter {
	@Bean
	public ServletRegistrationBean messageDispatcherServlet(ApplicationContext applicationContext) {
		MessageDispatcherServlet servlet = new MessageDispatcherServlet();
		servlet.setApplicationContext(applicationContext);
		servlet.setTransformWsdlLocations(true);
		return new ServletRegistrationBean(servlet, "/ws/*");
	}

	@Bean(name = "countries")
	public DefaultWsdl11Definition defaultWsdl11Definition(XsdSchema countriesSchema) {
		DefaultWsdl11Definition wsdl11Definition = new DefaultWsdl11Definition();
		wsdl11Definition.setPortTypeName("CountriesPort");
		wsdl11Definition.setLocationUri("/ws");
		wsdl11Definition.setTargetNamespace("http://spring.io/guides/gs-producing-web-service");
		wsdl11Definition.setSchema(countriesSchema);
		return wsdl11Definition;
	}

	@Bean
	public XsdSchema countriesSchema() {
		return new SimpleXsdSchema(new ClassPathResource("countries.xsd"));
	}
}
```

* Spring WS uses a different servlet type for handling SOAP messages: `MessageDispatcherServlet`. It is important to inject and set `ApplicationContext` to `MessageDispatcherServlet`. Without that, Spring WS will not detect Spring beans automatically.
* By naming this bean `messageDispatcherServlet`, it does not [replace Spring Boot’s default `DispatcherServlet` bean](https://docs.spring.io/spring-boot/docs/2.0.5.RELEASE/reference/htmlsingle/#howto-switch-off-the-spring-mvc-dispatcherservlet).
* `DefaultMethodEndpointAdapter` configures annotation driven Spring WS programming model. This makes it possible to use the various annotations like `@Endpoint` mentioned earlier.
* `DefaultWsdl11Definition` exposes a standard WSDL 1.1 using `XsdSchema`

It’s important to notice that you need to specify bean names for `MessageDispatcherServlet` and `DefaultWsdl11Definition`. Bean names determine the URL under which web service and the generated WSDL file is available. In this case, the WSDL will be available under http://<host>:<port>/ws/countries.wsdl.

This configuration also uses the WSDL location servlet transformation `servlet.setTransformWsdlLocations(true)`. If you visit http://localhost:8080/ws/countries.wsdl, the `soap:address` will have the proper address. If you instead visit the WSDL from the public facing IP address assigned to your machine, you will see that address instead.

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
java -jar build/libs/gs-soap-service-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-soap-service-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file instead](http://spring.io/guides/gs/convert-jar-to-war/).

Logging output is displayed. The service should be up and running within a few seconds.

## Test the application
Now that the application is running, you can test it. Create a file `request.xml` containing the following SOAP request:

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
				  xmlns:gs="http://spring.io/guides/gs-producing-web-service">
   <soapenv:Header/>
   <soapenv:Body>
      <gs:getCountryRequest>
         <gs:name>Spain</gs:name>
      </gs:getCountryRequest>
   </soapenv:Body>
</soapenv:Envelope>
```

The are a few options when it comes to testing the SOAP interface. You can use something like [SoapUI](http://www.soapui.org/) or just use command line tools if you are on a *nix/Mac system as shown below.

```bash
$ curl --header "content-type: text/xml" -d @request.xml http://localhost:8080/ws
```

As a result you should see this response:

```xml
<?xml version="1.0"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/">
  <SOAP-ENV:Header/>
  <SOAP-ENV:Body>
    <ns2:getCountryResponse xmlns:ns2="http://spring.io/guides/gs-producing-web-service">
      <ns2:country>
        <ns2:name>Spain</ns2:name>
        <ns2:population>46704314</ns2:population>
        <ns2:capital>Madrid</ns2:capital>
        <ns2:currency>EUR</ns2:currency>
      </ns2:country>
    </ns2:getCountryResponse>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

> Odds are that the output will be a compact XML document instead of the nicely formatted one shown above. If you have xmllib2 installed on your system, you can `curl -s <args above> | xmllint --format -` see the results formatted nicely.

## Summary
Congratulations! You’ve developed a SOAP-based service using Spring Web Services.