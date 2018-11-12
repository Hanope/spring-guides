## Consumer Driven Contracts
This guide walks you through the process of creating a Spring REST application with its contract stubs and consuming the contract within an other Spring application. [Spring Cloud Contract Project](https://cloud.spring.io/spring-cloud-contract)

## What you’ll build
You’ll setup two micro services one providing its contract, and the other one consuming this contract to make sure that the integration to the contract provider service is aligned with the specifications. If in the future, the contract of the producer service changes, then the consumer service’s tests fail catching the potential incompatibility.

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

* [Download](https://github.com/spring-guides/gs-contract-rest/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-contract-rest.git`
* cd into `gs-contract-rest/initial`
* Jump ahead to [Create contract producer service](#create-contract-producer-service).

**When you’re finished**, you can check your results against the code in `gs-contract-rest/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-contract-rest/blob/master/initial/build.gradle).

`contract-rest-service/build.gradle`

```gradle
buildscript {
	ext {
		springBootVersion = '2.0.5.RELEASE'
		verifierVersion = '1.2.1.RELEASE'
	}
	repositories { mavenCentral() }
	dependencies {
		classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
		classpath "org.springframework.cloud:spring-cloud-contract-gradle-plugin:${verifierVersion}"
	}
}

apply plugin: 'groovy'
apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'
apply plugin: 'spring-cloud-contract'


bootJar {
	baseName = 'contract-rest-service'
	version = '0.0.1-SNAPSHOT'
}
sourceCompatibility = 1.8
targetCompatibility = 1.8

repositories { mavenCentral() }

dependencies {
	compile('org.springframework.boot:spring-boot-starter-web')
	testCompile('org.springframework.boot:spring-boot-starter-test')
	testCompile('org.springframework.cloud:spring-cloud-starter-contract-verifier')
}

dependencyManagement {
	imports {
		mavenBom "org.springframework.cloud:spring-cloud-dependencies:Finchley.SR1"
	}
}

repositories {
    maven {
        url 'https://repo.spring.io/libs-milestone'
    }
}

contracts {
	packageWithBaseClasses = 'hello'
	baseClassMappings {
		baseClassMapping(".*hello.*", "hello.BaseClass")
	}
}
```

`contract-rest-client/build.gradle`

```gradle
buildscript {
	ext { springBootVersion = '2.0.5.RELEASE' }
	repositories { mavenCentral() }
	dependencies {
		classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
	}
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

bootJar {
	baseName = 'contract-rest-client'
	version = '0.0.1-SNAPSHOT'
}
sourceCompatibility = 1.8
targetCompatibility = 1.8

repositories { mavenCentral() }

dependencies {
	compile('org.springframework.boot:spring-boot-starter-web')
	testCompile('org.springframework.boot:spring-boot-starter-test')
	testCompile('org.springframework.cloud:spring-cloud-starter-contract-stub-runner')
}

dependencyManagement {
	imports {
		mavenBom "org.springframework.cloud:spring-cloud-dependencies:Finchley.SR1"
	}
}

repositories {
    maven {
        url 'https://repo.spring.io/libs-milestone'
    }
}

eclipse {
	classpath {
		containers.remove('org.eclipse.jdt.launching.JRE_CONTAINER')
		containers 'org.eclipse.jdt.launching.JRE_CONTAINER/org.eclipse.jdt.internal.debug.ui.launcher.StandardVMType/JavaSE-1.8'
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

`contract-rest-service/pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>contract-rest-service</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.5.RELEASE</version>
		<relativePath /> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<java.version>1.8</java.version>
		<spring-cloud.version>Finchley.SR1</spring-cloud.version>
		<spring-cloud-contract.version>1.2.1.RELEASE</spring-cloud-contract.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

        <!--
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-contract-verifier</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-contract-wiremock</artifactId>
			<scope>test</scope>
		</dependency>
        -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud.version}</version>
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

            <!--
			<plugin>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-contract-maven-plugin</artifactId>
				<version>${spring-cloud-contract.version}</version>
				<extensions>true</extensions>
				<configuration>
					<baseClassForTests>hello.BaseClass</baseClassForTests>
				</configuration>
			</plugin>
            -->

		</plugins>
	</build>


</project>
```

`contract-rest-client/pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>contract-rest-client</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.5.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-contract-stub-runner</artifactId>
			<scope>test</scope>
		</dependency>

	</dependencies>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>Finchley.SR1</version>
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


</project>
```

The [Spring Boot Maven plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin) provides many convenient features:

* It collects all the jars on the classpath and builds a single, runnable "über-jar", which makes it more convenient to execute and transport your service.
* It searches for the `public static void main()` method to flag as a runnable class.
* It provides a built-in dependency resolver that sets the version number to match [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml). You can override any version you wish, but it will default to Boot’s chosen set of versions.

## Build with your IDE
* Read how to import this guide straight into [Spring Tool Suite](http://spring.io/guides/gs/sts/).
* Read how to work with this guide in [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea).

## Create contract producer service
You’ll first need to create the service which produces the contract. This is a regular Spring Boot application providing a very simple REST service. The rest service simply returns a `Person` object in JSON.

`contract-rest-service/src/main/java/hello/ContractRestServiceApplication.java`

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class ContractRestServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(ContractRestServiceApplication.class, args);
	}
}
```

### Create the contract of the REST service

The contract of the REST service can be defined as a `.groovy` script. This contract specifies that if there is a `GET` request to url `/person/1`, sample data `id=1`, `name=foo` and `surname=bee` representing a `Person` entity will be returned in the response body of content-type `application/json`.

`contract-rest-service/src/test/resources/contracts/hello/find_person_by_id.groovy`

```groovy
import org.springframework.cloud.contract.spec.Contract

Contract.make {
	description "should return person by id=1"

	request {
		url "/person/1"
		method GET()
	}

	response {
		status 200
		headers {
			contentType applicationJson()
		}
		body (
			id: 1,
			name: "foo",
			surname: "bee"
		)
	}
}
```

At the `test` phase, automatic test classes will be created for the contract specified in the groovy file. The auto generated test java classes will extend the `hello.BaseClass`.

```xml
	<plugin>
		<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-contract-maven-plugin</artifactId>
		<version>${spring-cloud-contract.version}</version>
		<extensions>true</extensions>
		<configuration>
			<baseClassForTests>hello.BaseClass</baseClassForTests>
		</configuration>
	</plugin>
```

`contract-rest-service/src/test/java/hello/BaseClass.java`

```java
package hello;

import org.junit.Before;
import org.junit.runner.RunWith;
import org.mockito.Mockito;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.context.junit4.SpringRunner;

import io.restassured.module.mockmvc.RestAssuredMockMvc;

@RunWith(SpringRunner.class)
@SpringBootTest(classes = ContractRestServiceApplication.class)
public abstract class BaseClass {

	@Autowired PersonRestController personRestController;

	@MockBean PersonService personService;

	@Before public void setup() {
		RestAssuredMockMvc.standaloneSetup(personRestController);

		Mockito.when(personService.findPersonById(1L))
				.thenReturn(new Person(1L, "foo", "bee"));
	}

}
```

At this step, when the tests are executed, test results should be GREEN indicating that the REST controller is aligned with the contract and you have a fully functioning service.

### Check the simple Person query business logic

Model class `Person.java`
`contract-rest-service/src/main/java/hello/Person.java`

```java
package hello;

class Person {

	Person(Long id, String name, String surname) {
		this.id = id;
		this.name = name;
		this.surname = surname;
	}

	private Long id;

	private String name;

	private String surname;

	public Long getId() {
		return id;
	}

	public void setId(Long id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getSurname() {
		return surname;
	}

	public void setSurname(String surname) {
		this.surname = surname;
	}
}
```

Service bean `PersonService.java` which just populates a few Person entity in memory and returns the one when asked.

`contract-rest-service/src/main/java/hello/PersonService.java`

```java
package hello;

import java.util.HashMap;
import java.util.Map;

import org.springframework.stereotype.Service;

@Service
class PersonService {

	private final Map<Long, Person> personMap;

	public PersonService() {
		personMap = new HashMap<>();
		personMap.put(1L, new Person(1L, "Richard", "Gere"));
		personMap.put(2L, new Person(2L, "Emma", "Choplin"));
		personMap.put(3L, new Person(3L, "Anna", "Carolina"));
	}

	Person findPersonById(Long id) {
		return personMap.get(id);
	}
}
```

RestController bean `PersonRestController.java` which calls `PersonService` bean when a REST request is received for a person with the id.

`contract-rest-service/src/main/java/hello/PersonRestController.java`

```java
package hello;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
class PersonRestController {

	private final PersonService personService;

	public PersonRestController(PersonService personService) {
		this.personService = personService;
	}

	@GetMapping("/person/{id}")
	public Person findPersonById(@PathVariable("id") Long id) {
		return personService.findPersonById(id);
	}
}
```

### Test the contract-rest-service application
Run the `ContractRestServiceApplication.java` class as a Java Application or Spring Boot Application. The service should start at port `8000`.

Visit the service in the browser http://localhost:8000/person/1, http://localhost:8000/person/2, etc.

## Create contract consumer service
With the contract producer service ready, now we need to crate the client application which consumes the contract provided. This is a regular Spring Boot application providing a very simple REST service. The rest service simply returns a message with the queried Person’s name, e.g. `Hello Anna`.

`contract-rest-client/src/main/java/hello/ContractRestClientApplication.java`

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.client.RestTemplateBuilder;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication
public class ContractRestClientApplication {

	public static void main(String[] args) {
		SpringApplication.run(ContractRestClientApplication.class, args);
	}
}

@RestController
class MessageRestController {

	private final RestTemplate restTemplate;

	MessageRestController(RestTemplateBuilder restTemplateBuilder) {
		this.restTemplate = restTemplateBuilder.build();
	}

	@RequestMapping("/message/{personId}")
	String getMessage(@PathVariable("personId") Long personId) {
		Person person = this.restTemplate.getForObject("http://localhost:8000/person/{personId}", Person.class, personId);
		return "Hello " + person.getName();
	}

}
```

### Create the contract test
The contract provided by the producer should be consumed as a simple Spring test.

`contract-rest-client/src/test/java/hello/ContractRestClientApplicationTest.java`

```java
package hello;

import org.assertj.core.api.BDDAssertions;
import org.junit.Rule;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.cloud.contract.stubrunner.junit.StubRunnerRule;
import org.springframework.cloud.contract.stubrunner.spring.StubRunnerProperties;
import org.springframework.http.ResponseEntity;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.web.client.RestTemplate;

@RunWith(SpringRunner.class)
@SpringBootTest
public class ContractRestClientApplicationTest {

	@Rule
	public StubRunnerRule stubRunnerRule = new StubRunnerRule()
		.downloadStub("com.example", "contract-rest-service", "0.0.1-SNAPSHOT", "stubs")
		.withPort(8100)
		.stubsMode(StubRunnerProperties.StubsMode.LOCAL);

	@Test
	public void get_person_from_service_contract() {
		// given:
		RestTemplate restTemplate = new RestTemplate();

		// when:
		ResponseEntity<Person> personResponseEntity = restTemplate.getForEntity("http://localhost:8100/person/1", Person.class);

		// then:
		BDDAssertions.then(personResponseEntity.getStatusCodeValue()).isEqualTo(200);
		BDDAssertions.then(personResponseEntity.getBody().getId()).isEqualTo(1l);
		BDDAssertions.then(personResponseEntity.getBody().getName()).isEqualTo("foo");
		BDDAssertions.then(personResponseEntity.getBody().getSurname()).isEqualTo("bee");

	}
}
```

This test class will load the stubs of the contract producer service and make sure that the integration to the service is aligned with the contract.

In case the communication is faulty between the consumer service’s test and the producer’s contract, tests will fail and the problem will need to be fixed before making a new change on production.

### Test the contract-rest-client application
Run the `ContractRestClientApplication.java` class as a Java Application or Spring Boot Application. The service should start at port `9000`.

Visit the service in the browser http://localhost:9000/message/1, http://localhost:9000/message/2, etc.

## Summary
Congratulations! You’ve just used Spring to make your REST services declare their contract and consumer service be aligned with this contract.

## See Also
The following guides may also be helpful:

* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)
* [Creating a Multi Module Project](https://spring.io/guides/gs/multi-module/)