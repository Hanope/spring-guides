## 스프링 부트로 어플리케이션 만들기
This guide provides a sampling of how [Spring Boot](https://github.com/spring-projects/spring-boot) helps you accelerate and facilitate application development. 
이 가이드는 여러분이 어떻게 스프링 부트 개발을 빠르고 용이하게 할 수 있는가를 샘플을 만들며 알려줍니다.
As you read more Spring Getting Started guides, you will see more use cases for Spring Boot. 
여러분이 스프링 시작 가이드를 읽으면 다양한 스프링부트를 사용하는 방법을 볼 수 있을것입니다.
It is meant to give you a quick taste of Spring Boot. 
이것은 여러분에게 스프링부트를 빠르게 맛보게 하기 위한 것입니다.
If you want to create your own Spring Boot-based project, visit [Spring Initializr](https://start.spring.io/), 
만약 여러분이 스프링 부트 프로젝트를 만들고 싶다면 [Spring Initializr](https://start.spring.io/)를 방문하세요.
fill in your project details, pick your options, and you can download either a Maven build file, or a bundled up project as a zip file.
이 사이트에서 여러분이 프로젝트 정보를 입력하고 각종 옵션들을 선택하면 Maven 빌드 파일이나 프로젝트를 압축파일로 제공해 줄 것입니다.

## 만들것
You’ll build a simple web application with Spring Boot and add some useful services to it.
여러분은 스프링 부트를 사용한 간단한 웹 어플리케이션을 만들고 여러가지 사용할만한 서비스를 추가할 것입니다.

## 필요한것
* About 15 minutes
* 약 15분의 시간
* A favorite text editor or IDE
* 좋아하는 텍스트에디터나 IDE
* [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) or later
* [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 이상
* [Gradle 4+](http://www.gradle.org/downloads) or [Maven 3.2+](https://maven.apache.org/download.cgi)
* [Gradle 4+](http://www.gradle.org/downloads) 또는 [Maven 3.2+](https://maven.apache.org/download.cgi)
* You can also import the code straight into your IDE:
* 여러분이 코드를 IDE에 직접 가져오려면 다음을 참고하세요:
  - [Spring Tool Suite (STS)](http://spring.io/guides/gs/sts)
  - [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea/)

## 이 가이드를 완료하는 방법
대부분의 [스프링 시작 가이드](http://spring.io/guides)처럼 처음부터 하나씩 완성해 가거나 익숙한 시작 부분은 건너뛰어도 됩니다. 어느 방법을 선택하시더라도 작동되는 코드만 완성시키면 됩니다.

**처음부터 작업하실 분**은 [Gradle을 사용한 빌드](#Gradle을-사용한-빌드)로 가세요.

**시작부분을 건너뛰실 분**은 아래방법을 따라하세요:

* [다운로드](https://github.com/spring-guides/gs-spring-boot/archive/master.zip) 하시고 압축파일을 풀어주시거나 [GIT](http://spring.io/understanding/Git)을 사용해 복제해주세요 (명령어): `git clone https://github.com/spring-guides/gs-spring-boot.git`
* 소스의 압축을 해제한 폴더에서 `gs-spring-boot/initial`로 이동합니다.(cd)
* [initial](#initial)로 이동합니다.

**다 끝나고나서**, `gs-spring-boot/complete`의 코드와 비교해서 맞는지 확인해보세요.

## Gradle을 사용한 빌드
첫 번째로 여러분은 빌드 스크립트를 만들어야 합니다. 여러분은 스프링 어플리케이션을 빌드 할 때 원하는 시스템을 사용할 수 있습니다만, 코드가 작동하기 위해서는 [Gradle](http://gradle.org/)이나 [Maven](https://maven.apache.org/)이 포함되어 있어야 합니다. Gradle이나 Maven에 익숙하지 않은 경우, [Building Java Projects with Gradle](http://spring.io/guides/gs/gradle) 또는 [Building Java Projects with Maven (http://spring.io/guides/gs/maven)을 참조하세요.

### 폴더 구조 만들기
프로젝트 폴더를 선택하시고 안에 다음과 같이 하위 폴더를 만들어주세요 예를 들면, *nix(linux, unix)에서는 `mkdir -p src/main/java/hello`를 입력하면 됩니다:

```
└── src
    └── main
        └── java
            └── hello
```

### Gradle 빌드 파일 만들기
[초기 Gradle build file](https://github.com/spring-guides/gs-validating-form-input/blob/master/initial/build.gradle)은 아래와 같습니다.

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
    baseName = 'gs-spring-boot'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    testCompile("junit:junit")
}
```

[Spring Boot gradle plugin](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html)은 많고 편리한 기능을 제공합니다:

* 이 플러그인은 클래스패스 위의 jar를 하나의 실행 가능한 jar로 모아서 여러분의 서비스를 실행하고 전송하는데 편리하게 만들어줍니다.
* 이 플러그인은 `public static void main()`메소드를 찾아 실행 가능한 클래스로 표시를 해줍니다.
* 이 플러그인은 [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml)에 맞는 내장된 의존성 해석자를 제공합니다. 여러분이 원하는 버전을 사용할 수도 있습니다만 기본적으로 Spring Boot에서 선택한 버전이 기본 제공됩니다.

## Maven을 사용한 빌드
첫번째로 여러분은 빌드 스크립트를 만들어야 합니다. 여러분은 스프링 어플리케이션을 빌드할때 원하는 시스템을 사용할 수 있습니다만, 코드가 작동하기 위해서는 [Maven](https://maven.apache.org/)이 포함되어 있어야 합니다. Maven에 익숙하지 않은 경우, [Building Java Projects with Maven](http://spring.io/guides/gs/maven)을 참조하세요.

### 폴더 구조 만들기
프로젝트 폴더를 선택하시고 안에 다음과 같이 하위 폴더를 만들어주세요. 예를 들면, *nix(linux, unix)에서는 `mkdir -p src/main/java/hello`를 입력하면 됩니다:

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
    <artifactId>gs-spring-boot</artifactId>
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

이 [Spring Boot Maven plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin)은 많고 편리한 기능을 제공합니다.:

* 이 플러그인은 클래스패스 위의 jar를 하나의 실행가능한 jar로 모아서 여러분의 서비스를 실행하고 전송하는데 편리하게 만들어줍니다.
* 이 플러그인은 `public static void main()`메소드를 찾아 실행 가능한 클래스로 표시를 해줍니다.
* 이 플러그인은 [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml)에 맞는 내장된 의존성 해석자를 제공합니다.

## 여러분의 IDE를 사용한 빌드 하기
* 이 가이드를 STS에 직접 포함하는 방법을 알고싶으면 [Spring Tool Suite](http://spring.io/guides/gs/sts/)을 읽어주세요.
* 이 가이드를 Intellij IDEA에서 작동시키는 법을 알고 싶으면 [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea)을 읽어주세요.

## 스프링부트로 무엇을 할 수 있는지 배우기
Spring Boot offers a fast way to build applications. 
스프링부트는 어플리케이션을 빠르게 빌드할 수 있는 방법을 제공합니다.
It looks at your classpath and at beans you have configured, makes reasonable assumptions about what you’re missing, and adds it. 
스프링부트는 여러분이 설정한 클래스 패스의 빈들을 보고 누락된 부분에 대해 타당하게 추정되는 부분은 추가합니다.
With Spring Boot you can focus more on business features and less on infrastructure.
스프링 부트를 사용하면 여러분은 환경설정등의 부분 보다 비지니스 기능 구현에 더 집중할 수 있습니다.

예를 들면:

* Got Spring MVC? There are several specific beans you almost always need, and Spring Boot adds them automatically. 
스프링 MVC를 원하십니까? 스프링 MVC에 대부분 들어가는 여러 빈을 스프링 부트는 자동으로 추가해줍니다.
A Spring MVC app also needs a servlet container, so Spring Boot automatically configures embedded Tomcat.
그리고 스프링 MVC는 항상 서블릿 컨테이너가 필요한데 스프링 부트는 자동으로 Tomcat을 내장하고 있습니다.
* Got Jetty? If so, you probably do NOT want Tomcat, but instead embedded Jetty. Spring Boot handles that for you.
Jetty를 원하십니까? 여러분이 만약 Tomcat대신 Jetty를 내장하기 원한다면 스프링 부트는 여러분을 위해 해줄것입니다.
* Got Thymeleaf? There are a few beans that must always be added to your application context; Spring Boot adds them for you.
Thymeleaf를 원하십니까? 스프링 부트는 Themyleaf 설정에 필요한 여러 빈들을 여러분의 어플리케이션 컨텍스트에 넣을 것입니다.

These are just a few examples of the automatic configuration Spring Boot provides. 
위의 경우는 스프링 부트가 제공하는 자동 설정의 몇가지 예시 입니다.
At the same time, Spring Boot doesn’t get in your way. 
동시에 스프링부트는 여러분을 방해하지 않습니다.
For example, if Thymeleaf is on your path, Spring Boot adds a `SpringTemplateEngine` to your application context automatically. 
예를 들어, Thymeleaf가 프로젝트의 경로에 있다면, Spring Boot는 자동으로 어플리케이션 컨텍스트에 `SpringTemplateEngine`을 추가합니다
But if you define your own `SpringTemplateEngine` with your own settings, then Spring Boot won’t add one. 
그러나 만약 여러분이 직접 `SpringTemplateEngine`를 추가한 것이 있다면 스프링 부트는 해당 설정을 추가하지 않습니다.
This leaves you in control with little effort on your part.
이러한 스프링 부트의 행동은 여러분이 작업한 파트에 최소의 영향을 끼치게 합니다.

> Spring Boot doesn’t generate code or make edits to your files. 
Instead, when you start up your application, Spring Boot dynamically wires up beans and settings and applies them 
to your application context.
> 스프링 부트는 코드를 생성하거나 파일을 편집하지 않습니다. 대신 여러분이 어플리케이션을 시작할때, 스프링 부트는 동적으로 어플리케이션 컨텍스트에 빈과 설정을 적용합니다.

## 간단한 웹 어플리케이션 만들기
Now you can create a web controller for a simple web application.
이제 간단한 웹 어플리케이션을 위한 웹 컨트롤러를 만들어 보겠습니다.

`src/main/java/hello/HelloController.java`

```java
package hello;

import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;

@RestController
public class HelloController {

    @RequestMapping("/")
    public String index() {
        return "Greetings from Spring Boot!";
    }

}
```

The class is flagged as a `@RestController`, meaning it’s ready for use by Spring MVC to handle web requests. 
이 클래스는 스프링 MVC에서 요청을 다루기 위한 `@RestController`를 가지고 있습니다.
`@RequestMapping` maps `/` to the `index()` method. 
그리고 `@RequestMapping`으로 `/` 요청시 `index()`를 매핑해줍니다.
When invoked from a browser or using curl on the command line, the method returns pure text. 
브라우저에서 호출하거나 프롬프트에서 curl을 사용할 때 위의 메서드는 순수 텍스트를 반환합니다.
That’s because `@RestController` combines `@Controller` and `@ResponseBody`, 
two annotations that results in web requests returning data rather than a view.
이것은 왜냐하면 `@RestController`가 `@Controller`와 `@ResponseBody`가 결합되어서 뷰가 아닌 데이터를 반환하도록 해주기 때문입니다.

## 어플리케이션 클래스 만들기
Here you create an `Application` class with the components:
여러분은 컴포넌트를 가지고 있는 `Application` 클래스를 만들어 보겠습니다.

`src/main/java/hello/Application.java`

```java
package hello;

import java.util.Arrays;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    public CommandLineRunner commandLineRunner(ApplicationContext ctx) {
        return args -> {

            System.out.println("Let's inspect the beans provided by Spring Boot:");

            String[] beanNames = ctx.getBeanDefinitionNames();
            Arrays.sort(beanNames);
            for (String beanName : beanNames) {
                System.out.println(beanName);
            }

        };
    }

}
```

`@SpringBootApplication`은 아래의 모든것을 더해주는 편리한 어노테이션입니다.

* `@Configuration` 태그는 어플리케이션 컨텍스트에서 이 클래스를 빈으로 정의합니다.
* `@EnableAutoConfiguration`는 스프링 부트에서 클래스패스 세팅과 다른 빈과 다양한 설정을 해줍니다.
* 보통 Spring MVC app에서는 `@EnableWebMvc`를 붙이지만, 스프링 부트에서는 클래스 패스 위에 자동으로 **spring-webmvc**가 붙습니다. 이 플래그는 이 어플리케이션이 웹 어플리케이션이라는 것을 표시하고 `DispatcherServlet` 설정 등 핵심 행동을 하도록 합니다.
* `@ComponentScan`는 `hello` 패키지 내의 컨트롤러, 서비스, 컴포넌트(구성요소), 설정등을 찾도록 합니다.

어플리케이션 실행을 위한 `main()` 메소드에서는 스프링 부트에서 실행에 사용하는 `SpringApplication.run()` 메소드를 사용합니다. 여러분은 **web.xml**을 포함해서 한줄의 XML도 없다는 것을 알고 있으신가요? 이 웹 어플리케이션은 100% 순수한 자바로 되어있으며 여러분은 내부구조 설정을 위해 전혀 신경 쓸 필요가 없습니다.

There is also a `CommandLineRunner` method marked as a `@Bean` and this runs on start up. 
이 클래스는 또한 어플리케이션 시작시 `@Bean`으로 표시된 `CommandLineRunner` 메소드가 실행됩니다.
It retrieves all the beans that were created either by your app or were automatically added thanks to Spring Boot. 
It sorts them and prints them out.
이 메소드는 스프링 부트가 자동으로 추가하거나 여러분이 추가한 빈을 가져와서 정렬 후 출력합니다.

## 어플리케이션 실행하기
To run the application, execute:
어플리케이션을 실행하기 위해 입력합니다:

```
./gradlew build && java -jar build/libs/gs-spring-boot-0.1.0.jar
```

If you are using Maven, execute:
만약 여러분이 Maven을 쓰고 있다면 입력해주세요:

```
mvn package && java -jar target/gs-spring-boot-0.1.0.jar
```

You should see some output like this:
여러분은 아래와 같은 출력을 볼 것입니다:

```
Let's inspect the beans provided by Spring Boot:
application
beanNameHandlerMapping
defaultServletHandlerMapping
dispatcherServlet
embeddedServletContainerCustomizerBeanPostProcessor
handlerExceptionResolver
helloController
httpRequestHandlerAdapter
messageSource
mvcContentNegotiationManager
mvcConversionService
mvcValidator
org.springframework.boot.autoconfigure.MessageSourceAutoConfiguration
org.springframework.boot.autoconfigure.PropertyPlaceholderAutoConfiguration
org.springframework.boot.autoconfigure.web.EmbeddedServletContainerAutoConfiguration
org.springframework.boot.autoconfigure.web.EmbeddedServletContainerAutoConfiguration$DispatcherServletConfiguration
org.springframework.boot.autoconfigure.web.EmbeddedServletContainerAutoConfiguration$EmbeddedTomcat
org.springframework.boot.autoconfigure.web.ServerPropertiesAutoConfiguration
org.springframework.boot.context.embedded.properties.ServerProperties
org.springframework.context.annotation.ConfigurationClassPostProcessor.enhancedConfigurationProcessor
org.springframework.context.annotation.ConfigurationClassPostProcessor.importAwareProcessor
org.springframework.context.annotation.internalAutowiredAnnotationProcessor
org.springframework.context.annotation.internalCommonAnnotationProcessor
org.springframework.context.annotation.internalConfigurationAnnotationProcessor
org.springframework.context.annotation.internalRequiredAnnotationProcessor
org.springframework.web.servlet.config.annotation.DelegatingWebMvcConfiguration
propertySourcesBinder
propertySourcesPlaceholderConfigurer
requestMappingHandlerAdapter
requestMappingHandlerMapping
resourceHandlerMapping
simpleControllerHandlerAdapter
tomcatEmbeddedServletContainerFactory
viewControllerHandlerMapping
```

You can clearly see **org.springframework.boot.autoconfigure** beans. There is also a `tomcatEmbeddedServletContainerFactory`.
여러분은 **org.springframework.boot.autoconfigure**와 `tomcatEmbeddedServletContainerFactory` 빈을 정확하게 볼 수 있습니다. 
Check out the service.
그리고 해당 서비스를 확인해보세요.

```
$ curl localhost:8080
Greetings from Spring Boot!
```

## 유닛테스트 추가하기
You will want to add a test for the endpoint you added, and Spring Test already provides some machinery for that, 
and it’s easy to include in your project.
여러분이 클라이언트의 요청에 대한 응답 테스트를 하기 원하신다면 스프링 테스트를 프로젝트 안에 넣어서 테스트 할 수 있도록 제공하고 있습니다.

Add this to your build file’s list of dependencies:
여러분의 의존성 파일 리스트에 다음을 입력해 주세요:

```gradle
    testCompile("org.springframework.boot:spring-boot-starter-test")
```

If you are using Maven, add this to your list of dependencies:
만약 Maven을 사용중이라면 다음을 입력해 주세요:

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
```

Now write a simple unit test that mocks the servlet request and response through your endpoint:

`src/test/java/hello/HelloControllerTest.java`

```java
package hello;

import static org.hamcrest.Matchers.equalTo;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;

@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
public class HelloControllerTest {

    @Autowired
    private MockMvc mvc;

    @Test
    public void getHello() throws Exception {
        mvc.perform(MockMvcRequestBuilders.get("/").accept(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(content().string(equalTo("Greetings from Spring Boot!")));
    }
}
```

The `MockMvc` comes from Spring Test and allows you, via a set of convenient builder classes, to send HTTP requests into the `DispatcherServlet` and make assertions about the result. Note the use of the `@AutoConfigureMockMvc` together with `@SpringBootTest` to inject a `MockMvc` instance. Having used `@SpringBootTest` we are asking for the whole application context to be created. An alternative would be to ask Spring Boot to create only the web layers of the context using the `@WebMvcTest`. Spring Boot automatically tries to locate the main application class of your application in either case, but you can override it, or narrow it down, if you want to build something different.

As well as mocking the HTTP request cycle we can also use Spring Boot to write a very simple full-stack integration test. For example, instead of (or as well as) the mock test above we could do this:

`src/test/java/hello/HelloControllerIT.java`

```java
package hello;

import static org.hamcrest.Matchers.*;
import static org.junit.Assert.*;

import java.net.URL;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.boot.web.server.LocalServerPort;
import org.springframework.http.ResponseEntity;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class HelloControllerIT {

    @LocalServerPort
    private int port;

    private URL base;

    @Autowired
    private TestRestTemplate template;

    @Before
    public void setUp() throws Exception {
        this.base = new URL("http://localhost:" + port + "/");
    }

    @Test
    public void getHello() throws Exception {
        ResponseEntity<String> response = template.getForEntity(base.toString(),
                String.class);
        assertThat(response.getBody(), equalTo("Greetings from Spring Boot!"));
    }
}
```

The embedded server is started up on a random port by virtue of the `webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT` and the actual port is discovered at runtime with the `@LocalServerPort`.

## Add production-grade services
If you are building a web site for your business, you probably need to add some management services. Spring Boot provides several out of the box with its [actuator module](https://docs.spring.io/spring-boot/docs/2.0.5.RELEASE/reference/htmlsingle/#production-ready), such as health, audits, beans, and more.

Add this to your build file’s list of dependencies:

```gradle
    compile("org.springframework.boot:spring-boot-starter-actuator")
```

If you are using Maven, add this to your list of dependencies:

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```

Then restart the app:

```
./gradlew build && java -jar build/libs/gs-spring-boot-0.1.0.jar
```

If you are using Maven, execute:

```
mvn package && java -jar target/gs-spring-boot-0.1.0.jar
```

You will see a new set of RESTful end points added to the application. These are management services provided by Spring Boot.

```
2018-03-17 15:42:20.088  ... : Mapped "{[/error],produces=[text/html]}" onto public org.s...
2018-03-17 15:42:20.089  ... : Mapped "{[/error]}" onto public org.springframework.http.R...
2018-03-17 15:42:20.121  ... : Mapped URL path [/webjars/**] onto handler of type [class ...
2018-03-17 15:42:20.121  ... : Mapped URL path [/**] onto handler of type [class org.spri...
2018-03-17 15:42:20.157  ... : Mapped URL path [/**/favicon.ico] onto handler of type [cl...
2018-03-17 15:42:20.488  ... : Mapped "{[/actuator/health],methods=[GET],produces=[application/vnd...
2018-03-17 15:42:20.490  ... : Mapped "{[/actuator/info],methods=[GET],produces=[application/vnd.s...
2018-03-17 15:42:20.491  ... : Mapped "{[/actuator],methods=[GET],produces=[application/vnd.spring...
```

They include: errors, [actuator/health](http://localhost:8080/actuator/health), [actuator/info](http://localhost:8080/actuator/info), [actuator](http://localhost:8080/actuator).

> There is also a `/actuator/shutdown` endpoint, but it’s only visible by default via JMX. To [enable it as an HTTP endpoint](https://docs.spring.io/spring-boot/docs/2.0.5.RELEASE/reference/htmlsingle/#production-ready-endpoints-enabling-endpoints), add `management.endpoints.shutdown.enabled=true` to your `application.properties` file.

It’s easy to check the health of the app.

```
$ curl localhost:8080/actuator/health
{"status":"UP"}
```

You can try to invoke shutdown through curl.

```
$ curl -X POST localhost:8080/actuator/shutdown
{"timestamp":1401820343710,"error":"Method Not Allowed","status":405,"message":"Request method 'POST' not supported"}
```

Because we didn’t enable it, the request is blocked by the virtue of not existing.

For more details about each of these REST points and how you can tune their settings with an `application.properties` file (in `src/main/resources`), you can read detailed [docs about the endpoints](https://docs.spring.io/spring-boot/docs/2.0.5.RELEASE/reference/htmlsingle/#production-ready-endpoints).

## View Spring Boot’s starters
You have seen some of [Spring Boot’s "starters"](https://docs.spring.io/spring-boot/docs/2.0.5.RELEASE/reference/htmlsingle/#using-boot-starter). You can see them all [here in source code](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-project/spring-boot-starters).

## JAR support and Groovy support
The last example showed how Spring Boot makes it easy to wire beans you may not be aware that you need. And it showed how to turn on convenient management services.

But Spring Boot does yet more. It supports not only traditional WAR file deployments, but also makes it easy to put together executable JARs thanks to Spring Boot’s loader module. The various guides demonstrate this dual support through the `spring-boot-gradle-plugin` and `spring-boot-maven-plugin`.

On top of that, Spring Boot also has Groovy support, allowing you to build Spring MVC web apps with as little as a single file.

Create a new file called **app.groovy** and put the following  code in it:

```groovy
@RestController
class ThisWillActuallyRun {

    @RequestMapping("/")
    String home() {
        return "Hello World!"
    }

}
```

> It doesn’t matter where the file is. You can even fit an application that small inside a [single tweet](https://twitter.com/rob_winch/status/364871658483351552)!

Next, [install Spring Boot’s CLI](https://docs.spring.io/spring-boot/docs/2.0.5.RELEASE/reference/htmlsingle/#getting-started-installing-the-cli).

Run it as follows:

```
$ spring run app.groovy
```

> This assumes you shut down the previous application, to avoid a port collision.

From a different terminal window:

```
$ curl localhost:8080
Hello World!
```

Spring Boot does this by dynamically adding key annotations to your code and using [Groovy Grape](http://groovy.codehaus.org/Grape) to pull down libraries needed to make the app run.

## Summary
Congratulations! You built a simple web application with Spring Boot and learned how it can ramp up your development pace. You also turned on some handy production services. This is only a small sampling of what Spring Boot can do. Checkout [Spring Boot’s online docs](https://docs.spring.io/spring-boot/docs/2.0.5.RELEASE/reference/htmlsingle) if you want to dig deeper.

## See Also
The following guides may also be helpful:

* [Securing a Web Application](https://spring.io/guides/gs/securing-web/)
* [Serving Web Content with Spring MVC](https://spring.io/guides/gs/serving-web-content/)