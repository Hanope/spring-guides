## 웹 어플리케이션 보호하기
이 가이드는 리소스를 활용하는 간단한 웹 어플리케이션을 스프링 시큐리티로 보호하는 과정으로 안내합니다.

## 무엇을 만들게 되는가?
여러분은 스프링 MVC를 활용해 백엔드에 직접 작성된 유저 리스트를 사용하여 보호된 로그인 폼 페이지를 만들 것입니다.

## 무엇이 필요한가?
* 약 15분의 시간
* 좋아하는 텍스트 에디터나 IDE
* [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 이상
* [Gradle 4+](http://www.gradle.org/downloads) 또는 [Maven 3.2+](https://maven.apache.org/download.cgi)
* 여러분이 코드를 IDE에 직접 가져오려면 다음을 참고하세요:
  - [Spring Tool Suite (STS)](http://spring.io/guides/gs/sts)
  - [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea/)

## 이 가이드를 완료하는 방법
대부분의 [스프링 시작 가이드](http://spring.io/guides)처럼 처음부터 하나씩 완성해 가거나 익숙한 시작 부분은 건너뛰어도 됩니다. 어느 방법을 선택하시더라도 작동되는 코드가 완성될 것입니다.

**처음부터 작업하실 분**은 [Gradle을 사용한 빌드](#Gradle을-사용한-빌드)로 가세요.

**시작부분을 건너뛰실 분**은 아래방법을 따라하세요:

* [다운로드](https://github.com/spring-guides/gs-securing-web/archive/master.zip) 하시고 압축파일을 풀어주시거나 [GIT](http://spring.io/understanding/Git)을 사용해 복제해주세요 (명령어): `git clone https://github.com/spring-guides/gs-securing-web.git`
* 소스의 압축을 해제한 폴더에서 `gs-securing-web/initial`로 이동합니다.(cd)
* [Set up Spring Security](#set-up-spring-security)로 이동합니다.

**다 끝나고나서**, `gs-securing-web/complete`의 코드와 비교해서 맞는지 확인해보세요.

## Gradle을 사용한 빌드
첫 번째로 여러분은 빌드 스크립트를 만들어야 합니다. 여러분은 스프링 어플리케이션을 빌드 할 때 원하는 시스템을 사용할 수 있습니다만, 코드가 작동하기 위해서는 [Gradle](http://gradle.org/)이나 [Maven](https://maven.apache.org/)이 포함되어 있어야 합니다. Gradle이나 Maven에 익숙하지 않은 경우, [Building Java Projects with Gradle](http://spring.io/guides/gs/gradle) 또는 [Building Java Projects with Maven (http://spring.io/guides/gs/maven) 을 참조하세요.

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

### Gradle 빌드 파일 만들기
[초기 Gradle build file](https://github.com/spring-guides/gs-securing-web/blob/master/initial/build.gradle)은 아래와 같습니다.

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
    baseName = 'gs-securing-web'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-thymeleaf")
    compile("org.springframework.boot:spring-boot-starter-web")
    testCompile("junit:junit")
    testCompile("org.springframework.boot:spring-boot-starter-test")
    testCompile("org.springframework.security:spring-security-test")

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
    <artifactId>gs-securing-web</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.5.RELEASE</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
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
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-test</artifactId>
            <scope>test</scope>
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
            <name>Spring Releases</name>
            <url>https://repo.spring.io/libs-release</url>
        </pluginRepository>
    </pluginRepositories>

</project>
```

이 [Spring Boot Maven plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin)은 많고 편리한 기능을 제공합니다.:

* 이 플러그인은 클래스패스 위의 jar를 하나의 실행가능한 jar로 모아서 여러분의 서비스를 실행하고 전송하는데 편리하게 만들어줍니다.
* 이 플러그인은 `public static void main()`메소드를 찾아 실행 가능한 클래스로 표시를 해줍니다.
* 이 플러그인은 [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml)에 맞는 내장된 의존성 해석자를 제공합니다.

## 여러분의 IDE를 사용한 빌드 하기
* 이 가이드를 STS에 직접 포함하는 방법을 알고싶으면 [Spring Tool Suite](http://spring.io/guides/gs/sts/)을 읽어주세요.
* 이 가이드를 Intellij IDEA에서 작동시키는 법을 알고 싶으면 [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea)을 읽어주세요.

## 보호되지 않은 웹 어플리케이션 만들기
웹 어플리케이션에 보안을 적용하기 전에 보안을 적용할 웹 어플리케이션이 필요합니다. 그래서 아래에서 매우 간단한 웹 어플리케이션을 만들어 보겠습니다. 그리고 그다음 단계에서 스프링 시큐리티로 보호해 보겠습니다.

이 웹 어플리케이션은 두 개의 뷰를 가지고 있습니다. 홈페이지와 "Hello World" 페이지입니다. 그리고 홈페이지는 Thymeleaf 템플릿으로 되어 있습니다.

`src/main/resources/templates/home.html`

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org" xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity3">
    <head>
        <title>Spring Security Example</title>
    </head>
    <body>
        <h1>Welcome!</h1>

        <p>Click <a th:href="@{/hello}">here</a> to see a greeting.</p>
    </body>
</html>
```

여러분이 봤듯이 이 간단한 뷰는 "/hello" 페이지로 가는 링크를 가지고 있습니다. 그리고 Thymeleaf 템플릿으로 정의되어 있습니다.

`src/main/resources/templates/hello.html`

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity3">
    <head>
        <title>Hello World!</title>
    </head>
    <body>
        <h1>Hello world!</h1>
    </body>
</html>
```

이 웹 어플리케이션은 스프링 MVC 기반입니다. 그러므로 여러분은 스프링 MVC의 설정과 컨트롤러가 보여줄 템플릿을 만들 필요가 있습니다. 여기의 환경설정 클래스는 어플리케이션에서 스프링 MVC를 구성해줍니다.

`src/main/java/hello/MvcConfig.java`

```java
package hello;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MvcConfig implements WebMvcConfigurer {

    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/home").setViewName("home");
        registry.addViewController("/").setViewName("home");
        registry.addViewController("/hello").setViewName("hello");
        registry.addViewController("/login").setViewName("login");
    }

}
```

`WebMvcConfigurer`클래스의 메소드를 오버라이드 한 `addViewControllers()` 메소드는 네 개의 뷰 컨트롤러를 추가합니다. 두 개의 뷰 컨트롤러는 "home" 뷰 이름과(`home.html`으로 정의됨) "hello" 이름을 참조합니다.(`hello.html`으로 정의됨) 네 번째 컨트롤러는 "login"이라는 다른 뷰 이름을 가지고 있고 이 페이지는 다음 섹션에서 만들 것입니다.

이 시점에서 [Make the application executable](http://spring.io/guides/gs/securing-web/#run_the_app)로 가셔서 로그인을 제외한 어플리케이션을 실행할 수 있습니다.

여기까지 여러분이 시큐리티를 붙이기 위해 기반이 될 간단한 웹 어플리케이션을 만들어 보았습니다.

## 스프링 시큐리티 설치하기
여러분이 권한이 없는 유저가 "/hello" 페이지에 접근하는 것을 막기 원한다고 가정해봅시다. 현재 상황에서는 사용자가 홈페이지의 링크를 클릭하면 아무런 보호가 없이 페이지를 볼 수 있습니다. 여러분은 사용자가 페이지를 보기 전에 권한을 체크해서 막을 수 있는 기능의 추가가 필요할 것입니다.

여러분은 어플리케이션안에 스프링 시큐리티를 설정할 수 있습니다. 스프링 시큐리티가 클래스 패스 안에 있다면, 스프링 부트는 기본 인증과 함께 [자동으로 마지막 경로(endpoints)를 보호합니다.](https://docs.spring.io/spring-boot/docs/2.0.5.RELEASE/reference/htmlsingle/#boot-features-security) 그러나 여러분은 보안 설정을 추가로 커스터마이징 할 수 있습니다. 그래도 가장 먼저 해야 할 일은 스프링 시큐리티를 클래스 패스 안에 추가하는 것이죠.

Gradle을 사용하신다면 `dependencies`에 다음과 같이 추가해주세요:

`build.gradle`

```gradle
dependencies {
    ...
    compile("org.springframework.boot:spring-boot-starter-security")
    ...
}
```

Maven을 사용하신다면 `<dependencies>`에 다음을 추가해주세요:

`pom.xml`

```xml
<dependencies>
    ...
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
    ...
</dependencies>
```

아래는 인증된 사용자만 인사말을 볼 수 있도록 하는 보안 구성입니다:

`src/main/java/hello/WebSecurityConfig.java`

```java
package hello;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;

@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .antMatchers("/", "/home").permitAll()
                .anyRequest().authenticated()
                .and()
            .formLogin()
                .loginPage("/login")
                .permitAll()
                .and()
            .logout()
                .permitAll();
    }

    @Bean
    @Override
    public UserDetailsService userDetailsService() {
        UserDetails user =
             User.withDefaultPasswordEncoder()
                .username("user")
                .password("password")
                .roles("USER")
                .build();

        return new InMemoryUserDetailsManager(user);
    }
}
```

`WebSecurityConfig` 클래스는 스프링 시큐리티의 웹 시큐리티 기능을 켜주고 스프링 MVC에게 설정을 제공하기 위한 `@EnableWebSecurity` 어노테이션을 가지고 있습니다. 이것은 또한 `WebSecurityConfigurerAdapter`를 확장하고 여러 메소드를 오버라이드 해서 웹 시큐리티의 구성을 설정합니다.

`configure(HttpSecurity)` 메소드는 URL 경로를 보호할지 안 할지 정의합니다. 특히, "/"하고 "/home"경로는 어떠한 인증도 필요 없이 접근 가능 하도록 설정되어 있으나 다른 경로는 반드시 인증이 필요하도록 구성되어 있습니다.

만약 사용자가 성공적으로 로그인하면 사용자가 입력한 경로로 다시 이동합니다. `loginPage()`메소드는 특정된 "/login" 경로의 커스텀 페이지를 누구나 볼 수 있도록 허락합니다.

`userDetailsService()`메소드는 하나의 사용자를 메모리 내부 저장소에 설정합니다. 이 사용자의 이름은 "user"이며 비밀번호는 "password"고 "USER"의 역할을 가집니다.

이제 우리는 로그인 페이지가 필요하게 되었습니다. 이미 뷰 컨트롤러에서는 "login" 뷰를 볼 준비가 되었으니 로그인 뷰를 만들어 보겠습니다:

`src/main/resources/templates/login.html`

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity3">
    <head>
        <title>Spring Security Example </title>
    </head>
    <body>
        <div th:if="${param.error}">
            Invalid username and password.
        </div>
        <div th:if="${param.logout}">
            You have been logged out.
        </div>
        <form th:action="@{/login}" method="post">
            <div><label> User Name : <input type="text" name="username"/> </label></div>
            <div><label> Password: <input type="password" name="password"/> </label></div>
            <div><input type="submit" value="Sign In"/></div>
        </form>
    </body>
</html>
```

저 페이지 내의 Thymeleaf 템플릿은 "/login"으로 사용자명과 비밀번호를 post로 제공합니다. 아까 설정한 대로 스프링 시큐리티는 요청을 가로채서 유저를 인증시키는 필터를 제공할 것입니다. 사용자가 인증에 실패한다면, 페이지는 "/login?error"로 이동하여 적당한 에러 메시지를 보여줄 것입니다. 사용자가 로그아웃하는데 성공한다면, 어플리케이션은 "/login?logout"로 보내서 적당한 성공 메시지를 보여줄 것입니다.

마지막으로 사용자에게 현재 사용자 이름을 표시하고 로그 아웃 할 수 있는 방법을 제공해야 합니다. `hello.html`을 업데이트하여 현재 사용자의 이름을 보여주고 "Sign Out" 폼을 포함시켜 보겠습니다.

`src/main/resources/templates/hello.html`

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity3">
    <head>
        <title>Hello World!</title>
    </head>
    <body>
        <h1 th:inline="text">Hello [[${#httpServletRequest.remoteUser}]]!</h1>
        <form th:action="@{/logout}" method="post">
            <input type="submit" value="Sign Out"/>
        </form>
    </body>
</html>
```

우리는 `HttpServletRequest#getRemoteUser()`와 스프링 시큐리티를 사용하여 사용자 이름을 표시할 것입니다. "로그아웃" 폼은 "/logout" 경로로 POST 요청을 하며 성공하면 "/login?logout"로 이동합니다.

## 실행가능한 어플리케이션 만들기
해당 서비스를 외부 어플리케이션 서버에 배포하기 위해 전통적인 어플리케이션 파일 보관(아카이브,archive) 혹은 [WAR](http://spring.io/understanding/WAR) 파일로 만들 수도 있지만 다음과 같은 간단한 접근 방법으로 독립 실행형 응용 프로그램을 만들 수 있습니다. 여러분의 패키지는 오래되고 훌륭한 `main()` 메소드로 컨트롤되는 하나의 실행 가능한 JAR 파일로 만듭니다. 그리고 이 과정을 따라 하면 여러분은 외부 인스턴스에 배포하는 대신 HTTP 작동 환경을 위해 스프링에서 지원하는 내장된 [Tomcat](http://spring.io/understanding/Tomcat) 서블릿 컨테이너을 사용합니다.

`src/main/java/hello/Application.java`

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

    public static void main(String[] args) throws Throwable {
        SpringApplication.run(Application.class, args);
    }

}
```

`@SpringBootApplication`은 아래의 모든것을 더해주는 편리한 어노테이션입니다.

* `@Configuration` 태그는 어플리케이션 컨텍스트에서 이 클래스를 빈으로 정의합니다.
* `@EnableAutoConfiguration`는 스프링 부트에서 클래스패스 세팅과 다른 빈과 다양한 설정을 해줍니다.
* 보통 Spring MVC app에서는 `@EnableWebMvc`를 붙이지만, 스프링 부트에서는 클래스 패스 위에 자동으로 **spring-webmvc**가 붙습니다. 이 플래그는 이 어플리케이션이 웹 어플리케이션이라는 것을 표시하고 `DispatcherServlet` 설정 등 중요한 행동을 하도록 합니다.
* `@ComponentScan`은 `hello` 패키지 내의 컨트롤러, 서비스, 컴포넌트(구성요소), 설정등을 찾도록 합니다.

어플리케이션 실행을 위한 `main()` 메소드에서는 스프링 부트에서 실행에 사용하는 `SpringApplication.run()` 메소드를 사용합니다. 여러분은 **web.xml**을 포함해서 한 줄의 XML도 없다는 것을 알고 있으신가요? 이 웹 어플리케이션은 100% 순수한 자바로 되어있으며 여러분은 내부 구조 설정을 위해 전혀 신경 쓸 필요가 없습니다.

## 실행가능한 JAR로 빌드하기
여러분은 Gradle이나 Maven을 이용한 커맨드 라인으로 이 어플리케이션을 실행할 수 있습니다. 또는 모든 필요한 의존성, 클래스, 자원 등을 포함한 하나의 실행 가능한 JAR로 빌드 할 수도 있습니다. 그래서 다양한 환경에서 개발 주기 전반에 걸쳐 버전을 올리고 서비스를 배포하는 것이 쉬워집니다.

Gradle을 사용할 경우 `./gradlew bootRun` 명령어로 실행할 수 있습니다. 또는 `./gradlew build` 명령어로 JAR 파일을 만들고 다음과 같이 JAR 파일을 실행할 수 있습니다.

```
java -jar build/libs/gs-securing-web-0.1.0.jar
```

Maven을 사용할 경우 `./mvnw spring-boot:run` 명령어로 실행할 수 있습니다. 또는 `./mvnw clean package` 명령어로 JAR 파일을 만들고 다음과 같이 JAR 파일을 실행할 수 있습니다.

```
java -jar target/gs-securing-web-0.1.0.jar
```

이 행동은 실행 가능한 JAR 파일을 만드는 방법입니다. 물론 [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/)도 대신 선택할 수 있습니다.

```
... app starts up ...
```

어플리케이션이 시작하고 나면 여러분의 웹 브라우저를 켜서 `http://localhost:8080`을 입력하시면 다음과 같은 홈페이지를 볼 수 있습니다:

![](http://spring.io/guides/gs/securing-web/images/home.png)

여러분이 링크를 클릭하면 `/hello`로 작성된 인사 페이지를 들어가려고 시도합니다. 그러나 페이지가 보호되고 있으며 여러분은 로그인하지 않았기 때문에 로그인 페이지로 이동하게 됩니다:

![](http://spring.io/guides/gs/securing-web/images/login.png)

> 만약 스프링 시큐리티를 설정하지 않은 버전으로 이곳에 내려왔다면 로그인 페이지는 볼 수 없습니다. 나머지 보안을 적용하는 코드는 원하실 때 작업하십시오.

로그인 페이지에서 사용자 이름에 "user"를 입력하고 비밀번호에 "password"를 입력하고 로그인을 시도하면 인증되고 인사 페이지를 볼 수 있습니다.

![](http://spring.io/guides/gs/securing-web/images/greeting.png)

여러분이 "로그아웃" 버튼을 클릭하면 여러분의 인증은 취소되고, 로그아웃한 것을 나타내는 메시지와 함께 로그인 페이지로 돌아갑니다.

## 요약
축하합니다! 여러분은 스프링 시큐리티로 보호되는 간단한 웹 어플리케이션을 만들었습니다.

## 추가로 볼만한 것
다음 가이드 또한 도움이 될 수 있습니다:

* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)
* [Serving Web Content with Spring MVC](https://spring.io/guides/gs/serving-web-content/)
* [Spring Security Architecture](https://spring.io/guides/topicals/spring-security-architecture/) (Reference guide)
* [Spring Security and Angular JS](https://spring.io/guides/tutorials/spring-security-and-angular-js/) (Tutorial)