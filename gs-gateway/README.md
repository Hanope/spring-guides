## Building a Gateway
This guide walks you through how to use the Spring Cloud Gateway

## What you’ll build
You’ll build a gateway using [Spring Cloud Gateway](https://cloud.spring.io/spring-cloud-gateway/).

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

* [Download](https://github.com/spring-guides/gs-gateway/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-gateway.git`
* cd into `gs-gateway/initial`
* Jump ahead to [Create A Simple Route](#create-a-simple-route).

**When you’re finished**, you can check your results against the code in `gs-gateway/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-gateway/blob/master/initial/build.gradle).

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
    baseName = 'gs-gateway'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:Finchley.SR1"
    }
}

dependencies {
    compile("org.springframework.cloud:spring-cloud-starter-gateway")
    compile("org.springframework.cloud:spring-cloud-starter-netflix-hystrix")
    compile("org.springframework.cloud:spring-cloud-starter-contract-stub-runner"){
        exclude group: "org.springframework.boot", module: "spring-boot-starter-web"
    }
    testCompile("org.springframework.boot:spring-boot-starter-test")
}

repositories {
    maven {
        url 'https://repo.spring.io/libs-milestone'
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
    <artifactId>gs-gateway</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.5.RELEASE</version>
    </parent>

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

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-contract-stub-runner</artifactId>
            <exclusions>
                <exclusion>
                    <artifactId>spring-boot-starter-web</artifactId>
                    <groupId>org.springframework.boot</groupId>
                </exclusion>
            </exclusions>
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

    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/libs-milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>

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

## Creating A Simple Route
The Spring Cloud Gateway uses routes in order to process requests to downstream services. In this guide we will route all of our requests to [HTTPBin](https://httpbin.org/). Routes can be configured a number of ways but for this guide we will use the Java API provided by the Gateway.

To get started, create a new `Bean` of type `RouteLocator` in `Application.java`.

`src/main/java/gateway/Application.java`

```java
@Bean
public RouteLocator myRoutes(RouteLocatorBuilder builder) {
    return builder.routes().build();
}
```

The above `myRoutes` method takes in a `RouteLocatorBuilder` which can easily be used to create routes. In addition to just creating routes, `RouteLocatorBuilder` allows you to add predicates and filters to your routes so you can route handle based on certain conditions as well as alter the request/response as you see fit.

Let’s create a route that routes a request to http://httpbin.org/get when a request is made to the Gateway at `/get`. In our configuration of this route we will add a filter that will add the request header `Hello` with the value `World` to the request before it is routed.

`src/main/java/gateway/Application.java`

```java
@Bean
public RouteLocator myRoutes(RouteLocatorBuilder builder) {
    return builder.routes()
        .route(p -> p
            .path("/get")
            .filters(f -> f.addRequestHeader("Hello", "World"))
            .uri("http://httpbin.org:80"))
        .build();
}
```

To test our very simple Gateway, just run `Application.java`, it should be run on port `8080`. Once the application is running, make a request to http://localhost:8080/get. You can do this using cURL by issuing the following command in your terminal.

```bash
$ curl http://localhost:8080/get
```

You should receive a response back that looks like this

```json
{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Connection": "close",
    "Forwarded": "proto=http;host=\"localhost:8080\";for=\"0:0:0:0:0:0:0:1:56207\"",
    "Hello": "World",
    "Host": "httpbin.org",
    "User-Agent": "curl/7.54.0",
    "X-Forwarded-Host": "localhost:8080"
  },
  "origin": "0:0:0:0:0:0:0:1, 73.68.251.70",
  "url": "http://localhost:8080/get"
}
```

Notice that HTTPBin shows that the header `Hello` with the value `World` was sent in the request.

## Using Hystrix
Now lets do something a little more interesting. Since the services behind the Gateway could potentially behave poorly effecting our clients we might want to wrap the routes we create in circuit breakers. You can do this in the Spring Cloud Gateway using Hystrix. This is implemented via a simple filter that you can add to your requests. Lets create another route to demonstrate this.

In this example we will leverage HTTPBin’s delay API that waits a certain number of seconds before sending a response. Since this API could potentially take a long time to send its response we can wrap the route that uses this API in a `HystrixCommand`. Add a new route to our `RouteLocator` object that looks like the following

`src/main/java/gateway/Application.java`

```java
@Bean
public RouteLocator myRoutes(RouteLocatorBuilder builder) {
    return builder.routes()
        .route(p -> p
            .path("/get")
            .filters(f -> f.addRequestHeader("Hello", "World"))
            .uri("http://httpbin.org:80"))
        .route(p -> p
            .host("*.hystrix.com")
            .filters(f -> f.hystrix(config -> config.setName("mycmd")))
            .uri("http://httpbin.org:80")).
        build();
}
```

There are some differences between this new route configuration and the previous one we created. For one, we are using the host predicate instead of the path predicate. This means that as long as the host is `hystrix.com` we will route the request to HTTPBin and wrap that request in a `HystrixCommand`. We do this by applying a filter to the route. The Hystrix filter can be configured using a configuration object. In this example we are just giving the `HystrixCommand` the name `mycmd`.

Lets test this new route. Start the application, but this time we are going to make a request to `/delay/3`. It is also important that we include a `Host` header that has the a host of `hystrix.com` or else the request won’t be routed. In cURL this would look like

```bash
$ curl --dump-header - --header 'Host: www.hystrix.com' http://localhost:8080/delay/3
```

> We are using `--dump-header` to see the response headers, the - after `--dump-header` is telling cURL to print the headers to stdout.

After executing this command you should see the following in your terminal

```
HTTP/1.1 504 Gateway Timeout
content-length: 0
```

As you can see Hystrix timed out waiting for the response from HTTPBin. When Hystrix times out we can optionaly provide a fallback so that clients do not just received a `504` but something more meaninful. In a production scenario you may return some data from a cache for example, but in our simple example we will just return a response with the body `fallback` instead.

To do this, lets modify our Hystrix filter to provide a URL to call in the case of a timeout.

`src/main/java/gateway/Application.java`

```java
@Bean
public RouteLocator myRoutes(RouteLocatorBuilder builder) {
    return builder.routes()
        .route(p -> p
            .path("/get")
            .filters(f -> f.addRequestHeader("Hello", "World"))
            .uri("http://httpbin.org:80"))
        .route(p -> p
            .host("*.hystrix.com")
            .filters(f -> f.hystrix(config -> config
                .setName("mycmd")
                .setFallbackUri("forward:/fallback")))
            .uri("http://httpbin.org:80"))
        .build();
}
```

Now when the Hystrix wrapped route times out it will call `/fallback` in the Gateway app. Lets add the `/fallback` endpoint to our application.

In `Application.java` add the class level annotation `@RestController`, then add the following `@RequestMapping` to the class.

`src/main/java/gateway/Application.java`

```java
@RequestMapping("/fallback")
public Mono<String> fallback() {
    return Mono.just("fallback");
}
```

To test this new fallback functionality, restart the application and again issue the following cURL command

```bash
$ curl --dump-header - --header 'Host: www.hystrix.com' http://localhost:8080/delay/3
```

With the fallback in place, we now see that we get a `200` back from the Gateway with the response body of `fallback`.

```
HTTP/1.1 200 OK
transfer-encoding: chunked
Content-Type: text/plain;charset=UTF-8

fallback
```

## Writing Tests
As a good developer, we should write some tests to make sure our Gateway is doing what we expect it should. In most cases we want to limit out dependencies on outside resources, especially in unit tests, so we should not depend on HTTPBin. One solution to this problem is to make the URI in our routes configurable, so we can easily change the URI if we need to.

In `Application.java` create a new class called `UriConfiguration`.

```java
@ConfigurationProperties
class UriConfiguration {

    private String httpbin = "http://httpbin.org:80";

    public String getHttpbin() {
        return httpbin;
    }

    public void setHttpbin(String httpbin) {
        this.httpbin = httpbin;
    }
}
```

To enable this `ConfigurationProperties` we need to also add a class-level annotation to `Application.java`.

```java
@EnableConfigurationProperties(UriConfiguration.class)
```

With our new configuration class in place lets use it in the `myRoutes` method.

`src/main/java/gateway/Application.java`

```java
@Bean
public RouteLocator myRoutes(RouteLocatorBuilder builder, UriConfiguration uriConfiguration) {
    String httpUri = uriConfiguration.getHttpbin();
    return builder.routes()
        .route(p -> p
            .path("/get")
            .filters(f -> f.addRequestHeader("Hello", "World"))
            .uri(httpUri))
        .route(p -> p
            .host("*.hystrix.com")
            .filters(f -> f
                .hystrix(config -> config
                    .setName("mycmd")
                    .setFallbackUri("forward:/fallback")))
            .uri(httpUri))
        .build();
}
```

As you can see, instead of hardcoding the URL to HTTPBin we are getting the URL from our new configuration class instead.

Below is the complete contents of `Application.java`.

`src/main/java/gateway/Application.java`

```java
@SpringBootApplication
@EnableConfigurationProperties(UriConfiguration.class)
@RestController
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    public RouteLocator myRoutes(RouteLocatorBuilder builder, UriConfiguration uriConfiguration) {
        String httpUri = uriConfiguration.getHttpbin();
        return builder.routes()
            .route(p -> p
                .path("/get")
                .filters(f -> f.addRequestHeader("Hello", "World"))
                .uri(httpUri))
            .route(p -> p
                .host("*.hystrix.com")
                .filters(f -> f
                    .hystrix(config -> config
                        .setName("mycmd")
                        .setFallbackUri("forward:/fallback")))
                .uri(httpUri))
            .build();
    }

    @RequestMapping("/fallback")
    public Mono<String> fallback() {
        return Mono.just("fallback");
    }
}

@ConfigurationProperties
class UriConfiguration {

    private String httpbin = "http://httpbin.org:80";

    public String getHttpbin() {
        return httpbin;
    }

    public void setHttpbin(String httpbin) {
        this.httpbin = httpbin;
    }
}
```

Create a new class called `ApplicationTest` in `src/main/test/java/gateway`. In the new class add the following content.

```java
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT,
		properties = {"httpbin=http://localhost:${wiremock.server.port}"})
@AutoConfigureWireMock(port = 0)
public class ApplicationTest {

	@Autowired
	private WebTestClient webClient;

	@Test
	public void contextLoads() throws Exception {
		//Stubs
		stubFor(get(urlEqualTo("/get"))
				.willReturn(aResponse()
					.withBody("{\"headers\":{\"Hello\":\"World\"}}")
					.withHeader("Content-Type", "application/json")));
		stubFor(get(urlEqualTo("/delay/3"))
			.willReturn(aResponse()
				.withBody("no fallback")
				.withFixedDelay(3000)));

		webClient
			.get().uri("/get")
			.exchange()
			.expectStatus().isOk()
			.expectBody()
			.jsonPath("$.headers.Hello").isEqualTo("World");

		webClient
			.get().uri("/delay/3")
			.header("Host", "www.hystrix.com")
			.exchange()
			.expectStatus().isOk()
			.expectBody()
			.consumeWith(
				response -> assertThat(response.getResponseBody()).isEqualTo("fallback".getBytes()));
	}
}
```

Our test is actually taking advantage of WireMock from Spring Cloud Contract in order stand up a server that can mock the APIs from HTTPBin. The first thing to notice is the use of `@AutoConfigureWireMock(port = 0)`. This annotation will start WireMock on a random port for us.

Next notice that we are taking advantage of our `UriConfiguration` class and setting the `httpbin` property in the `@SpringBootTest` annotation to the WireMock server running locally. Within the test we then setup "stubs" for the HTTPBin APIs we call via the Gateway and mock the behavior we expect. Finally we use `WebTestClient` to actually make requests to the Gateway and validate the responses.

## Summary
Congratulations! You’ve just built your first Spring Coud Gateway application!