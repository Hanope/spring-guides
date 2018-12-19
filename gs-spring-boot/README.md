## 스프링 부트로 어플리케이션 만들기
이 가이드는 여러분이 어떻게 스프링 부트 개발을 빠르고 용이하게 할 수 있는가를 샘플을 만들며 알려줍니다. 여러분이 스프링 시작 가이드를 더 읽으면 다양한 스프링 부트를 사용하는 방법을 볼 수 있을 것입니다. 이러한 방법들은 여러분에게 스프링 부트를 빠르게 맛보게 하기 위한 것입니다. 만약 여러분이 스프링 부트 프로젝트를 만들고 싶다면 [Spring Initializr](https://start.spring.io/)를 방문하세요. 이 사이트에서 여러분이 프로젝트 정보를 입력하고 각종 옵션들을 선택하면 Maven 빌드 파일이나 프로젝트를 압축파일로 제공해 줄 것입니다.

## 무엇을 만들게 되는가?
여러분은 스프링 부트를 사용한 간단한 웹 어플리케이션을 만들고 여러 가지 사용할만한 서비스를 추가할 것입니다.

## 무엇이 필요한가?
* 약 15분의 시간
* 좋아하는 텍스트에디터나 IDE
* [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 이상
* [Gradle 4+](http://www.gradle.org/downloads) 또는 [Maven 3.2+](https://maven.apache.org/download.cgi)
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
스프링 부트는 어플리케이션을 빠르게 빌드 할 수 있는 방법을 제공합니다. 스프링 부트는 여러분이 설정한 클래스 패스의 빈을 보고 누락된 부분에 대해 타당하게 추정되는 부분은 추가합니다. 그래서 스프링 부트를 사용하면 여러분은 환경설정 등의 부분 보다 비즈니스 기능 구현에 더 집중할 수 있습니다.

예를 들면:

* 스프링 MVC를 원하십니까? 스프링 MVC에 대부분 들어가는 여러 빈을 스프링 부트는 자동으로 추가해줍니다. 그리고 스프링 MVC는 항상 서블릿 컨테이너가 필요한데 스프링 부트는 자동으로 Tomcat을 내장하고 있습니다.
* Jetty를 원하십니까? 여러분이 만약 Tomcat 대신 Jetty를 내장하기 원한다면 스프링 부트는 여러분을 위해 해줄 것입니다.
* Thymeleaf를 원하십니까? 스프링 부트는 Themyleaf 설정에 필요한 여러 빈들을 여러분의 어플리케이션 컨텍스트에 넣을 것입니다.

위의 경우는 스프링 부트가 제공하는 자동 설정의 몇 가지 예시입니다. 동시에 스프링 부트는 여러분을 방해하지 않습니다. 예를 들어, Thymeleaf가 프로젝트의 경로에 있다면, Spring Boot는 자동으로 어플리케이션 컨텍스트에 `SpringTemplateEngine`을 추가합니다 그러나 만약 여러분이 직접 `SpringTemplateEngine`를 추가한 것이 있다면 스프링 부트는 해당 설정을 추가하지 않습니다. 이러한 스프링 부트의 행동은 여러분이 작업한 파트에 최소의 영향을 끼치게 합니다.

> 스프링 부트는 코드를 생성하거나 파일을 편집하지 않습니다. 대신 여러분이 어플리케이션을 시작할 때, 스프링 부트는 동적으로 어플리케이션 컨텍스트에 빈과 설정을 적용합니다.

## 간단한 웹 어플리케이션 만들기
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

이 클래스는 스프링 MVC에서 요청을 다루기 위한 `@RestController`가 표시되어 있습니다. 그리고 `@RequestMapping`으로 `/` 요청 시 `index()`메소드가 응답하도록 매핑해줍니다. 브라우저에서 호출하거나 프롬프트에서 curl을 사용할 때 위의 메서드는 순수 텍스트를 반환합니다. 이것은 왜냐하면 `@RestController`가 `@Controller`와 `@ResponseBody`가 결합되어서 뷰가 아닌 데이터를 반환하도록 해주기 때문입니다.

## 어플리케이션 클래스 만들기
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

어플리케이션 실행을 위한 `main()` 메소드에서는 스프링 부트에서 실행에 사용하는 `SpringApplication.run()` 메소드를 사용합니다. 여러분은 **web.xml**을 포함해서 한 줄의 XML도 없다는 것을 알고 있으신가요? 이 웹 어플리케이션은 100% 순수한 자바로 되어있으며 여러분은 내부 구조 설정을 위해 전혀 신경 쓸 필요가 없습니다.

그리고 어플리케이션 시작 시 `@Bean`이 표시된 `CommandLineRunner` 메소드가 실행됩니다. 이 메소드는 스프링 부트가 자동으로 추가하거나 여러분이 추가한 빈을 가져와서 정렬 후 출력합니다.

## 어플리케이션 실행하기
어플리케이션을 실행하기 위해 입력합니다:

```
./gradlew build && java -jar build/libs/gs-spring-boot-0.1.0.jar
```

만약 여러분이 Maven을 쓰고 있다면 입력해주세요:

```
mvn package && java -jar target/gs-spring-boot-0.1.0.jar
```

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

여러분은 **org.springframework.boot.autoconfigure**와 `tomcatEmbeddedServletContainerFactory` 빈을 정확하게 볼 수 있습니다. 

그리고 해당 서비스의 동작을 확인해보세요.

```
$ curl localhost:8080
Greetings from Spring Boot!
```

## 유닛테스트 추가하기
여러분이 클라이언트의 요청에 대한 응답 테스트를 하기 원하신다면 스프링 테스트를 프로젝트 안에 넣어서 테스트할 수 있도록 제공하고 있습니다.

여러분의 의존성 파일 리스트에 다음과 같이 추가합니다:

```gradle
    testCompile("org.springframework.boot:spring-boot-starter-test")
```

만약 Maven을 사용중이라면 다음을 입력해 주세요:

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
```

이제 서블릿 요청 및 응답에 대한 유닛 테스트 mock을 작성해 보겠습니다.

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

`MockMvc`는 스프링 테스트에서 작성되었으며 편리한 빌더 클래스를 통해 `DispatcherServlet`로 HTTP 요청을 보내 테스트 결과를 만듭니다. 그리고 `@AutoConfigureMockMvc`와 `@SpringBootTest`를 사용해서 `MockMvc` 인스턴스를 주입(inject) 하는 것을 확인할 수 있습니다. `@SpringBootTest`를 사용하면 어플리케이션 컨텍스트가 만들어지도록 해줍니다. 아니면 `@WebMvcTest`를 사용하여 스프링 부트가 웹 레이어만 만들도록 할 수도 있습니다. 기본적으로 스프링 부트는 자동적으로 어플리케이션 전체 범위에서 메인 클래스를 찾지만 여러분이 무엇인가 다른 작업을 하고 싶다면 이것을 오버라이드 해서 범위를 줄일 수 있습니다.

HTTP 요청 사이클에 대한 테스트뿐만 아니라 우리가 쉽게 풀 스택 테스트를 할 수 있도록 해줍니다. 예를 들면 mock 테스트 대신에 이것을 시도해 볼 수 있습니다:

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

위 코드에서 내장된 서버는 `webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT`에 의해 랜덤 포트로 실행되지만 실제 작동될 포트는 `@LocalServerPort`로 확인 가능합니다.

## 상용 등급의 서비스 추가하기
여러분이 상업적으로 웹사이트를 만든다면 아마도 서비스 관리가 필요할 것입니다. 그래서 스프링 부트는 [actuator module](https://docs.spring.io/spring-boot/docs/2.0.5.RELEASE/reference/htmlsingle/#production-ready)를 통해서 서버의 상태나 빈 등 여러 가지 모니터링 옵션을 제공합니다.

여러분의 의존성 파일 리스트에 다음과 같이 추가합니다:

```gradle
    compile("org.springframework.boot:spring-boot-starter-actuator")
```

만약 Maven을 사용중이라면 다음을 입력해 주세요:

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```

그리고 어플리케이션을 재시작합니다:

```
./gradlew build && java -jar build/libs/gs-spring-boot-0.1.0.jar
```

만약 Maven을 사용중이라면 아래 명령어를 실행해주세요:

```
mvn package && java -jar target/gs-spring-boot-0.1.0.jar
```

여러분은 어플리케이션에 새로운 RESTful 포인트가 추가된 것을 볼 수 있습니다. 그리고 이것은 스프링 부트가 제공하는 관리를 위한 서비스입니다.

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

에러표시를 위한 것과 [actuator/health](http://localhost:8080/actuator/health), [actuator/info](http://localhost:8080/actuator/info), [actuator](http://localhost:8080/actuator)가 추가되었습니다.

> 여기에는 `/actuator/shutdown`도 있지만 JMX를 통해서만 볼 수 있습니다. 해당 기능을 위해서는 `application.properties` 파일의 `management.endpoints.shutdown.enabled=true`를 추가해주시고 정보를 더 얻으시려면 [enable it as an HTTP endpoint](https://docs.spring.io/spring-boot/docs/2.0.5.RELEASE/reference/htmlsingle/#production-ready-endpoints-enabling-endpoints)를 읽어주세요.

방금 추가한 기능은 어플리케이션의 상태를 체크하기 쉽도록 해줍니다.

```
$ curl localhost:8080/actuator/health
{"status":"UP"}
```

curl을 통해 shutdown을 호출할 수도 있습니다.

```
$ curl -X POST localhost:8080/actuator/shutdown
{"timestamp":1401820343710,"error":"Method Not Allowed","status":405,"message":"Request method 'POST' not supported"}
```

하지만 우리가 이 기능을 활성화하지 않아서 요청은 거부되었습니다.

만약 여러분이 `application.properties` 파일의 REST 포인트에 대한 각각 상세한 정보를 얻고 튜닝하기를 원하신다면 [docs about the endpoints](https://docs.spring.io/spring-boot/docs/2.0.5.RELEASE/reference/htmlsingle/#production-ready-endpoints)를 읽어주세요.

## 스프링 부트 스타터 보기
여러분은 [Spring Boot’s "starters"](https://docs.spring.io/spring-boot/docs/2.0.5.RELEASE/reference/htmlsingle/#using-boot-starter). You can see them all [here in source code](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-project/spring-boot-starters)의 일부를 보았습니다.

## JAR 지원과 Groovy 지원
마지막 예제에서 스프링 부트가 우리가 필요성을 모를 수도 있는 빈의 연결을 쉽게 할 수 있다는 것을 보여주었습니다. 그리고 관리 서비스를 편리하게 켤 수 있는지를 보여주었습니다.

그러나 스프링 부트는 더 많은 것을 합니다. 스프링 부트는 전통적인 버포를 위한 WAR 파일을 만드는 것 외에도 로더 모듈을 통해 실행 가능한 JAR를 쉽게 만들 수 있도록 해줍니다. 여러 스프링 시작 가이드는 `spring-boot-gradle-plugin`과 `spring-boot-maven-plugin`를 동시 지원하는 것을 보여주고 있습니다.

위와 같이 스프링 부트는 MVC 웹 앱을 하나의 파일로 만들 수 있도록 Groovy 또한 지원합니다.

**app.groovy**라 명명된 파일을 만들고 아래와 같은 코드를 넣어보겠습니다:

```groovy
@RestController
class ThisWillActuallyRun {

    @RequestMapping("/")
    String home() {
        return "Hello World!"
    }

}
```

> 파일이 어디 있는지 문제 되지는 않습니다. [single tweet](https://twitter.com/rob_winch/status/364871658483351552)처럼 어플리케이션 안에 작은 응용 프로그램을 넣을 수도 있습니다.

다음 [install Spring Boot’s CLI](https://docs.spring.io/spring-boot/docs/2.0.5.RELEASE/reference/htmlsingle/#getting-started-installing-the-cli)를 작업해 주세요.

그리고 아래의 내용을 따라서 입력합니다:

```
$ spring run app.groovy
```

> 포트 충돌을 막기 위해서 실행 중인 이전 어플리케이션이 종료되었다고 가정합니다.

다른 터미널창에서 입력해주세요:

```
$ curl localhost:8080
Hello World!
```

스프링 부트는 여러분의 코드에 동적으로 키 어노테이션을 추가하고 [Groovy Grape](http://groovy.codehaus.org/Grape)를 사용해서 앱의 실행에 필요한 라이브러리를 다운받습니다.

## 요약
축하합니다! 여러분은 스프링 부트로 간단한 웹 애플리케이션을 구축하고 개발 속도를 높일 수 있는 방법을 배웠습니다. 그리고 간단한 서비스를 만들고 실행해보았습니다. 이것은 스프링 부트로 할 수 있는 작은 샘플일 뿐입니다. 여러분이 좀 더 깊게 알기를 원한다면 [Spring Boot’s online docs](https://docs.spring.io/spring-boot/docs/2.0.5.RELEASE/reference/htmlsingle)를 읽어보세요.

## 추가로 볼만한 것
다음 가이드 또한 도움이 될 수 있습니다:

* [Securing a Web Application](https://spring.io/guides/gs/securing-web/)
* [Serving Web Content with Spring MVC](https://spring.io/guides/gs/serving-web-content/)