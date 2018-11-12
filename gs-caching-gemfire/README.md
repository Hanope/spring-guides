## Caching Data with Pivotal GemFire
This guide walks through the process of using Pivotal GemFire’s data fabric to cache certain calls from your code.

> For more general knowledge of Pivotal GemFire concepts and accessing data from Pivotal GemFire, read through the guide, [Accessing Data with Pivotal GemFire](https://spring.io/guides/gs/accessing-data-gemfire/).

## What you’ll build
You’ll build a service that requests quotes from a CloudFoundry hosted Quote service and caches them in Pivotal GemFire.

Then, you’ll see that fetching the same quote again eliminates the expensive call to the Quote service since Spring’s Cache Abstraction, backed by Pivotal GemFire, will be used to cache the results, given the same request.

The Quote service is located at…​

```
http://gturnquist-quoters.cfapps.io
```

The Quote service has the following API…​

```
GET /api         - get all quotes
GET /api/random  - get random quote
GET /api/{id}    - get specific quote
```

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

* [Download](https://github.com/spring-guides/gs-caching-gemfire/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-caching-gemfire.git`
* cd into `gs-caching-gemfire/initial`
* Jump ahead to [Create a bindable object for fetching data](#create-a-bindable-object-for-fetching-data).

**When you’re finished**, you can check your results against the code in `gs-caching-gemfire/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-caching-gemfire/blob/master/initial/build.gradle).

`build.gradle`

```gradle
buildscript {
    repositories {
        mavenCentral()
        maven { url "https://repo.spring.io/libs-release" }
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:2.0.5.RELEASE")
    }
}

plugins {
    id "io.spring.dependency-management" version "1.0.5.RELEASE"
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'

sourceCompatibility = 1.8
targetCompatibility = 1.8

bootJar {
    baseName = 'gs-caching-gemfire'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
    maven { url "https://repo.spring.io/libs-release" }
}

dependencies {
    compile("org.springframework.boot:spring-boot-starter") {
        exclude group: "org.springframework.boot", module: "spring-boot-starter-logging"
    }
    compile("org.springframework.data:spring-data-gemfire")
    compile("com.fasterxml.jackson.core:jackson-databind")
    compile("org.projectlombok:lombok")
    runtime("javax.cache:cache-api")
    runtime("org.springframework.shell:spring-shell:1.2.0.RELEASE")
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
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.5.RELEASE</version>
    </parent>

    <groupId>org.springframework</groupId>
    <artifactId>gs-caching-gemfire</artifactId>
    <version>0.1.0</version>

    <properties>
        <spring-shell.version>1.2.0.RELEASE</spring-shell.version>
    </properties>

    <repositories>
        <repository>
            <id>spring-releases</id>
            <url>https://repo.spring.io/libs-release</url>
        </repository>
    </repositories>

    <dependencies>
        <dependency>
            <groupId>javax.cache</groupId>
            <artifactId>cache-api</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-gemfire</artifactId>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.shell</groupId>
            <artifactId>spring-shell</artifactId>
            <version>${spring-shell.version}</version>
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

## Create a bindable object for fetching data
Now that you’ve set up the project and build system, you can focus on defining the domain objects necessary to capture the bits you need to pull quotes (the data) from the Quote service.

`src/main/java/hello/Quote.java`

```java
package hello;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

import org.springframework.util.ObjectUtils;

import lombok.Data;

@Data
@JsonIgnoreProperties(ignoreUnknown = true)
@SuppressWarnings("unused")
public class Quote {

	private Long id;

	private String quote;

	@Override
	public boolean equals(Object obj) {

		if (this == obj) {
			return true;
		}

		if (!(obj instanceof Quote)) {
			return false;
		}

		Quote that = (Quote) obj;

		return ObjectUtils.nullSafeEquals(this.getId(), that.getId());
	}

	@Override
	public int hashCode() {

		int hashValue = 17;

		hashValue = 37 * hashValue + ObjectUtils.nullSafeHashCode(getId());

		return hashValue;
	}

	@Override
	public String toString() {
		return getQuote();
	}
}
```

The `Quote` domain class has `id` and `quote` properties. These are the two primary attributes you will gather further along in this guide. The implementation of the `Quote` class has been simplified by the use of [Project Lombok](https://projectlombok.org/).

In addition to `Quote`, the `QuoteResponse` captures the entire payload of the response sent by the Quote service sent in the quote request. It includes the `status` (a.k.a. `type`) of the request along with the `quote`. This class also uses [Project Lombok](https://projectlombok.org/) to simplify the implementation.

`src/main/java/hello/QuoteResponse.java`

```java
package hello;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;
import com.fasterxml.jackson.annotation.JsonProperty;

import lombok.Data;

@Data
@JsonIgnoreProperties(ignoreUnknown = true)
public class QuoteResponse {

	@JsonProperty("value")
	private Quote quote;

	@JsonProperty("type")
	private String status;

	@Override
	public String toString() {
		return String.format("{ @type = %1$s, quote = '%2$s', status = %3$s }",
			getClass().getName(), getQuote(), getStatus());
	}
}
```

A typical response from the Quote service appears as follows:

```json
{
  "type":"success",
  "value": {
    "id":1,
    "quote":"Working with Spring Boot is like pair-programming with the Spring developers."
  }
}
```

Both classes are marked with `@JsonIgnoreProperties(ignoreUnknown=true)`. This means even though other JSON attributes may be retrieved, they’ll be ignored.

## Query Quote service for data
Your next step is to create a service class that queries for quotes.

`src/main/java/hello/QuoteService.java`

```java
package hello;

import java.util.Collections;
import java.util.Map;
import java.util.Optional;

import org.springframework.cache.annotation.CachePut;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

@SuppressWarnings("unused")
@Service
public class QuoteService {

	protected static final String ID_BASED_QUOTE_SERVICE_URL = "http://gturnquist-quoters.cfapps.io/api/{id}";
	protected static final String RANDOM_QUOTE_SERVICE_URL = "http://gturnquist-quoters.cfapps.io/api/random";

	private volatile boolean cacheMiss = false;

	private final RestTemplate quoteServiceTemplate = new RestTemplate();

	/**
	 * Determines whether the previous service method invocation resulted in a cache miss.
	 *
	 * @return a boolean value indicating whether the previous service method invocation resulted in a cache miss.
	 */
	public boolean isCacheMiss() {
		boolean cacheMiss = this.cacheMiss;
		this.cacheMiss = false;
		return cacheMiss;
	}

	protected void setCacheMiss() {
		this.cacheMiss = true;
	}

	/**
	 * Requests a quote with the given identifier.
	 *
	 * @param id the identifier of the {@link Quote} to request.
	 * @return a {@link Quote} with the given ID.
	 */
	@Cacheable("Quotes")
	public Quote requestQuote(Long id) {
		setCacheMiss();
		return requestQuote(ID_BASED_QUOTE_SERVICE_URL, Collections.singletonMap("id", id));
	}

	/**
	 * Requests a random quote.
	 *
	 * @return a random {@link Quote}.
	 */
	@CachePut(cacheNames = "Quotes", key = "#result.id")
	public Quote requestRandomQuote() {
		setCacheMiss();
		return requestQuote(RANDOM_QUOTE_SERVICE_URL);
	}

	protected Quote requestQuote(String URL) {
		return requestQuote(URL, Collections.emptyMap());
	}

	protected Quote requestQuote(String URL, Map<String, Object> urlVariables) {

		return Optional.ofNullable(this.quoteServiceTemplate.getForObject(URL, QuoteResponse.class, urlVariables))
			.map(QuoteResponse::getQuote)
			.orElse(null);
	}
}
```

The `QuoteService` uses Spring’s `RestTemplate` to query the Quote service’s API. The Quote service returns a JSON object, but Spring uses Jackson to bind the data to a `QuoteResponse` and ultimately, a `Quote` object.

The key piece of this service class is how `requestQuote` has been annotated with `@Cacheable("Quotes")`. *[Spring’s Caching Abstraction](https://docs.spring.io/spring/docs/current/spring-framework-reference/integration.html#cache)* intercepts the call to `requestQuote` to check whether the service method has already been called. If so, Spring’s Caching Abstraction just returns the cached copy. Otherwise, Spring proceeds to invoke the method, store the response in the cache, and then return the results to the caller.

We also used the `@CachePut` annotation on the `requestRandomQuote` service method. Since the quote returned from this service method invocation will be random, we don’t know which quote we will receive. Therefore, we cannot consult the cache (i.e. `Quotes`) prior to the call, but we can cache the result of the call, which will have a positive affect on subsequent `requestQuote(id)` calls, assuming the quote of interest was randomly selected and cached previously.

The `@CachePut` uses a SpEL expression (“#result.id”) to access the result of the service method invocation and retrieve the ID of the `Quote` to use as the cache key. You can learn more about *Spring’s Cache Abstraction* SpEL context [here](https://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#cache-spel-context).

> You must supply the name of the cache. We named it "Quotes" for demonstration purposes, but in production, it is recommended to pick an appropriately descriptive name. This also means different methods can be associated with different caches. This is useful if you have different configuration settings for each cache, such as different expiration or eviction policies, etc.

Later on when you run the code, you will see the time it takes to run each call and be able to discern the impact that caching has on service response times. This demonstrates the value of caching certain calls. If your application is constantly looking up the same data, caching the results can improve your performance dramatically.

## Make the application executable
Although Pivotal GemFire caching can be embedded in web apps and WAR files, the simpler approach demonstrated below creates a standalone application. You package everything in a single, executable JAR file, driven by a good old Java `main()` method.

`src/main/java/hello/Application.java`

```java
package hello;

import java.util.Optional;

import org.apache.geode.cache.client.ClientRegionShortcut;
import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.data.gemfire.cache.config.EnableGemfireCaching;
import org.springframework.data.gemfire.config.annotation.ClientCacheApplication;
import org.springframework.data.gemfire.config.annotation.EnableCachingDefinedRegions;

@SpringBootApplication
@ClientCacheApplication(name = "CachingGemFireApplication", logLevel = "error")
@EnableCachingDefinedRegions(clientRegionShortcut = ClientRegionShortcut.LOCAL)
@EnableGemfireCaching
@SuppressWarnings("unused")
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    ApplicationRunner runner(QuoteService quoteService) {

        return args -> {
            Quote quote = requestQuote(quoteService, 12L);
            requestQuote(quoteService, quote.getId());
            requestQuote(quoteService, 10L);
        };
    }

    private Quote requestQuote(QuoteService quoteService, Long id) {

        long startTime = System.currentTimeMillis();

        Quote quote = Optional.ofNullable(id)
            .map(quoteService::requestQuote)
            .orElseGet(quoteService::requestRandomQuote);

        long elapsedTime = System.currentTimeMillis();

        System.out.printf("\"%1$s\"%nCache Miss [%2$s] - Elapsed Time [%3$s ms]%n", quote,
            quoteService.isCacheMiss(), (elapsedTime - startTime));

        return quote;
    }
}
```

`@SpringBootApplication` is a convenience annotation that adds all of the following:

* `@Configuration` tags the class as a source of bean definitions for the application context.
* `@EnableAutoConfiguration` tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings.
* Normally you would add `@EnableWebMvc` for a Spring MVC app, but Spring Boot adds it automatically when it sees **spring-webmvc** on the classpath. This flags the application as a web application and activates key behaviors such as setting up a `DispatcherServlet`.
* `@ComponentScan` tells Spring to look for other components, configurations, and services in the `hello` package, allowing it to find the controllers.

The `main()` method uses Spring Boot’s `SpringApplication.run()` method to launch an application. Did you notice that there wasn’t a single line of XML? No **web.xml** file either. This web application is 100% pure Java and you didn’t have to deal with configuring any plumbing or infrastructure.

At the top of the configuration is a single, vital annotation: `@EnableGemfireCaching`. This turns on caching (i.e. is meta-annotated with Spring’s `@EnableCaching` annotation) and declares additional, important beans in the background to support caching using Pivotal GemFire as the caching provider.

The first bean is an instance of `QuoteService` used to access the Quotes REST-ful Web service.

The other two are needed to cache Quotes and perform the actions of the application.

* `quotesRegion` defines a Pivotal GemFire `LOCAL` lcient Region inside the cache to store quotes. It is specifically named "Quotes" to match the usage of `@Cacheable("Quotes")` on our `QuoteService` method.
* `runner` is an instance of the Spring Boot `ApplicationRunner` interface used to run our application.

The first time a quote is requested (using `requestQuote(id)`), a cache miss occurs and the service method will be invoked, incurring a noticeable delay that is no where close to zero ms. In this case, caching is linked by the input parameters (i.e. `id`) of the service method, `requestQuote`. In other words, the `id` method parameter is the cache key. Subsequent requests for the same quote identified by ID, will result in a cache hit, thereby avoiding the expensive service call.

For demonstration purposes, the call to the `QuoteService` is wrapped in a separate method (`requestQuote` inside the `Application` class) to capture the time to make the service call. This lets you see exactly how long any one request is taking.

## Build an executable JAR
You can run the application from the command line with Gradle or Maven. Or you can build a single executable JAR file that contains all the necessary dependencies, classes, and resources, and run that. This makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.

If you are using Gradle, you can run the application using `./gradlew bootRun`. Or you can build the JAR file using `./gradlew build`. Then you can run the JAR file:

```
java -jar build/libs/gs-caching-gemfire-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-caching-gemfire-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/) instead.

Logging output is displayed. The service should be up and running within a few seconds.

```
"@springboot with @springframework is pure productivity! Who said in #java one has to write double the code than in other langs? #newFavLib"
Cache Miss [true] - Elapsed Time [776 ms]
"@springboot with @springframework is pure productivity! Who said in #java one has to write double the code than in other langs? #newFavLib"
Cache Miss [false] - Elapsed Time [0 ms]
"Really loving Spring Boot, makes stand alone Spring apps easy."
Cache Miss [true] - Elapsed Time [96 ms]
```

From this you can see that the first call to the Quote service for a quote took 776 ms and resulted in a cache miss. However, the second call requesting the same quote took 0 ms and resulted in a cache hit. This clearly shows that the second call was cached and never actually hit the Quote service. However, when a final service call for a specific, non-cached quote request is made, it took 96 ms and resulted in a cache miss since this new quote was not previously in the cache before the call.

## Summary
Congratulations! You’ve just built a service that performed an expensive operation and tagged it so that it will cache results.

## See Also
The following guides may also be helpful:

* [Caching Data with Spring](https://spring.io/guides/gs/caching/)
* [Serving Web Content with Spring MVC](https://spring.io/guides/gs/serving-web-content/)
* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)