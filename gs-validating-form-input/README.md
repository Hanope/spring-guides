## 폼에 입력된 값에 대한 유효성 검증하기
이 가이드는 유효성 확인을 사용하기위해 웹 어플리케이션을 구성하는 방법을 안내합니다.

## 앞으로 만들어 볼 것
여러분은 표준 유효성 어노테이션을 사용해 유효성을 검사하는 간단한 유저 입력 스프링 MVC를 만들어봅니다.
그리고 사용자의 유효한 재입력을 도와주는 에러메시지를 어떻게 화면에 표시하는지 볼것입니다.

## 필요한 것
* 15분의 시간
* 선호하는 텍스트 에디터나 IDE
* [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 이상
* [Gradle 4+](http://www.gradle.org/downloads) 또는 [Maven 3.2+](https://maven.apache.org/download.cgi)
* 또한 아래의 IDE를 이용해 코드를 바로 불러올 수 있습니다:
  - [Spring Tool Suite (STS)](http://spring.io/guides/gs/sts)
  - [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea/)

## 이 가이드를 완료하는 방법
대부분의 [스프링 시작 가이드](http://spring.io/guides)처럼 처음부터 하나씩 완성해 가거나
익숙한 시작 부분은 건너뛰어도 됩니다. 어느 방법을 선택하시더라도 작동되는 코드만 완성시키면 됩니다.

**처음부터 작업하실분**은 [Gradle을 사용한 빌드](#Gradle을 사용한 빌드)로 가세요.

**시작부분을 건너뛰실분**은 아래방법을 따라하세요.

* [다운로드](https://github.com/spring-guides/gs-validating-form-input/archive/master.zip) 하시고 압축파일을 풀어주시거나 [GIT](http://spring.io/understanding/Git)을 사용해 복제해주세요 (명령어): `git clone https://github.com/spring-guides/gs-validating-form-input.git`

* 소스의 압축을 해제한 폴더에서 `gs-validating-form-input/initial`로 이동합니다.(cd)
* [PersonForm 객체만들기](#PersonForm 객체만들기)로 이동합니다.

**다 끝나고나서**, `gs-validating-form-input/complete`의 코드와 비교해서 맞는지 확인해보세요.

## Gradle을 사용한 빌드
첫번째로 여러분은 빌드 스크립트를 만들어야 합니다.
여러분은 스프링 어플리케이션을 빌드할때 원하는 시스템을 사용할 수 있습니다만,
코드가 작동하기 위해서는 [Gradle](http://gradle.org/)이나 [Maven](https://maven.apache.org/)이 포함되어 있어야 합니다.
Gradle이나 Maven에 익숙하지 않은 경우, [Building Java Projects with Gradle](http://spring.io/guides/gs/gradle) 또는 [Building Java Projects with Maven](http://spring.io/guides/gs/maven)을 참조하세요.

### 폴더 구조 만들기
프로젝트 폴더를 선택하시고 안에 다음과 같이 하위 폴더를 만들어주세요
예를 들면, *nix(linux, unix)에서는 `mkdir -p src/main/java/hello`를 입력하면 됩니다.

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
    baseName = 'gs-validating-form-input'
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
    compile("org.hibernate.validator:hibernate-validator")
    compile("org.apache.tomcat.embed:tomcat-embed-el")
    testCompile("org.springframework.boot:spring-boot-starter-test")
}
```

[Spring Boot gradle plugin](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html)은 많고 편리한 기능을 제공합니다:
* 이 플러그인은 클래스패스 위의 jar를 하나의 실행가능한 jar로 모아서 여러분의 서비스를 실행하고 전송하는데 편리하게 만들어줍니다.
* 이 플러그인은 `public static void main()`메소드를 찾아 실행가능한 클래스로 표시를 해줍니다.
* 이 플러그인은 [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml)에 맞는 내장된 의존성 해석자를 제공합니다. 여러분이 원하는 버전을 사용할수도 있습니다만 기본적으로 Spring Boot에서 선택한 버전이 기본제공됩니다.

## Maven을 사용한 빌드
첫번째로 여러분은 빌드 스크립트를 만들어야 합니다. 여러분은 스프링 어플리케이션을 빌드할때 원하는 시스템을 사용할 수 있습니다만, 코드가 작동하기 위해서는 [Maven](https://maven.apache.org/)이 포함되어 있어야 합니다. Maven에 익숙하지 않은 경우, [Building Java Projects with Maven](http://spring.io/guides/gs/maven)을 참조하세요.

### 폴더 구조 만들기
프로젝트 폴더를 선택하시고 안에 다음과 같이 하위 폴더를 만들어주세요. 예를 들면, *nix(linux, unix)에서는 `mkdir -p src/main/java/hello`를 입력하면 됩니다.

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
    <artifactId>gs-validating-form-input</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.5.RELEASE</version>
    </parent>

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
            <groupId>org.hibernate.validator</groupId>
            <artifactId>hibernate-validator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-el</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```

이 [Spring Boot Maven plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin)은 많고 편리한 기능을 제공합니다.:

* 이 플러그인은 클래스패스 위의 jar를 하나의 실행가능한 jar로 모아서 여러분의 서비스를 실행하고 전송하는데 편리하게 만들어줍니다.
* 이 플러그인은 `public static void main()`메소드를 찾아 실행가능한 클래스로 표시를 해줍니다.
* 이 플러그인은 [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml)에 맞는 내장된 의존성 해석자를 제공합니다.

## 여러분의 IDE를 사용한 빌드하기
* 이 가이드를 STS에 직접 포함하는 방법을 알고싶으면 [Spring Tool Suite](http://spring.io/guides/gs/sts/)을 읽어주세요.
* 이 가이드를 Intellij IDEA에서 작동시키는 법을 알고싶으면 [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea)을 읽어주세요.

## PersonForm 객체만들기
이 어플리케이션은 사용자의 이름과 나이의 유효성 체크를 포함합니다. 그래서 먼저 사용자를 만들기 위한 폼 클래스를 만들어 보겠습니다.

`src/main/java/hello/PersonForm.java`

```java
package hello;

import javax.validation.constraints.Min;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;

public class PersonForm {

    @NotNull
    @Size(min=2, max=30)
    private String name;

    @NotNull
    @Min(18)
    private Integer age;

    public String getName() {
        return this.name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String toString() {
        return "Person(Name: " + this.name + ", Age: " + this.age + ")";
    }
}
```

작성한 `PersonForm` 클래스는 `name`과 `age`를 가지고 있습니다. 그리고 이 속성들은 많고 다양한 표준 유효성 어노테이션을 가지고 있습니다:

* `@Size(min=2, max=30)`은 이름을 2~30글자만 입력하도록 허용합니다.
* `@NotNull`은 빈값은 들어올 수 없습니다.
* `@Min(18)`은 18미만의 숫자는 허용하지 않습니다.

그 외에도 `name`과 `age`를 활용한 getters/setters와 편리한 `toString()`메소드를 볼 수 있습니다.

## 웹 컨트롤러 만들기
방금 폼에 쓰일 오브젝트를 만들었으므로, 이제 간단한 웹 컨트롤러를 만들 시간입니다.

`src/main/java/hello/WebController.java`

```java
package hello;

import javax.validation.Valid;

import org.springframework.stereotype.Controller;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;


@Controller
public class WebController implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/results").setViewName("results");
    }

    @GetMapping("/")
    public String showForm(PersonForm personForm) {
        return "form";
    }

    @PostMapping("/")
    public String checkPersonInfo(@Valid PersonForm personForm, BindingResult bindingResult) {

        if (bindingResult.hasErrors()) {
            return "form";
        }

        return "redirect:/results";
    }
}
```

이 컨트롤러는 `/`로 작동하는 GET, POST메소드를 가지고 있습니다.

`showForm`메소드는 `form` 템플릿을 반환합니다. 이 템플릿은 `PersonForm`을 연상시켜 템플릿을 반환하게 하는 메소드를 가지고 있습니다. 

`checkPersonFormInfo` 메소드는 두개의 인수를 받습니다.

* 여러분이 만들 폼 양식에 맞는 속성을 가지고 `@Valid`가 표시된 `personForm` 오브젝트
* 유효성 에러를 테스트 할 수 있는 `bindingResult`객체

여러분은 폼과 바운드된 `PersonForm`객체로부터 모든 속성을 얻을 수 있습니다. 그리고 원래의 유저 `form` 템플릿을 보낼때 여러분은 에러를 테스트 할 수 있습니다. 이러한 테스트중에서, 모든 에러속성을 화면에 표시합니다.

모든 유저객체의 속성이 조건에 맞으면 최종 `results` 템플릿으로 이동합니다.

## HTML 화면 만들기
이제 메인페이지를 만들어봅시다.

`src/main/resources/templates/form.html`

```html
<html>
    <body>
        <form action="#" th:action="@{/}" th:object="${personForm}" method="post">
            <table>
                <tr>
                    <td>Name:</td>
                    <td><input type="text" th:field="*{name}" /></td>
                    <td th:if="${#fields.hasErrors('name')}" th:errors="*{name}">Name Error</td>
                </tr>
                <tr>
                    <td>Age:</td>
                    <td><input type="text" th:field="*{age}" /></td>
                    <td th:if="${#fields.hasErrors('age')}" th:errors="*{age}">Age Error</td>
                </tr>
                <tr>
                    <td><button type="submit">Submit</button></td>
                </tr>
            </table>
        </form>
    </body>
</html>
```

이 페이지는 테이블에서 각 필드를 분리된 슬롯으로 나눈 간단한 폼입니다. 
이 폼은 `/`의 POST 메소드를 보도록 되어있습니다. 그리고 이 폼은 `personForm` 객체로 백업되며 웹컨트롤러에서 GET 메소드로 볼 수 있습니다.
이것이 **bean-backed form**으로 우리에게 알려진 것입니다.
`PersonForm`빈은 두개의 필드가 있고, 여러분은 `th:field="{name}"` **그리고** `th:field="{age}"`으로 볼 수 있습니다. 그리고 각 필드의 두번째 요소(td)는 유효성 에러를 볼 수 있습니다.

마지막으로, 여러분은 제출(submit)을 할 수 있는 버튼을 가지고 있습니다. 제출하고 나서 이름이나 나이에 있는 `@Valid` 조건을 위반한 값을 입력할 경우 이 페이지로 돌아가서 에러 메시지를 화면에 보여줄 것 입니다. 만약 이름과 나이를 조건에 맞게 입력 할 경우 다음페이지로 이동할 것 입니다.

`src/main/resources/templates/results.html`

```html
<html>
	<body>
		Congratulations! You are old enough to sign up for this site.
	</body>
</html>
```

> 이 예제에서 어느 페이지도 복잡한 CSS나 JavaScript를 가지고 있지는 않습니다. 그러나 여러분이 웹페이지 꾸미는 것을 배우는 것은 매우 가치있는 일입니다.


## 어플리케이션 클래스 만들기
이 어플리케이션을 위해 여러분은 [Thymeleaf](http://www.thymeleaf.org/doc/tutorials/3.0/thymeleafspring.html) 템플릿 언어를 사용하고 있습니다. 그리고 이 어플리케이션은 순수 HTML보다 더 많은것을 필요로 합니다.

`src/main/java/hello/Application.java`

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

    public static void main(String[] args) throws Exception {
        SpringApplication.run(Application.class, args);
    }

}
```

Spring에서는 MVC 활성화를 위해 어플리케이션 클래스에 보통 `@EnableWebMvc`를 더합니다. 그러나 Spring Boot에서는 클래스 패스 안에 **spring-webmvc**를 찾아서 이미 `@SpringBootApplication` 어노테이션이 더해져 있습니다. 
이 어노테이션은 `@Controller`가 표시된 클래스와 안에 포함된 메소드를 찾습니다.

Thymeleaf 환경설정은 또한 `@SpringBootApplication`안에 포함되어 있습니다. 그리고 `templates/` 폴더 아래에 있는 템플릿들의 '.html'의 접미어를 제거하여 뷰로 보여줍니다.
Thymeleaf 세팅은 여러분의 원하는 바에 따라 다양하게 변경 가능합니다만 이 가이드에서는 다루지 않습니다.

## 실행가능한 JAR로 빌드하기
여러분은 Gradle이나 Maven을 이용한 커맨드 라인으로 이 어플리케이션을 실행할 수 있습니다.
또는 모든 필요한 의존성, 클래스, 자원등을 포함한 하나의 실행가능한 JAR로 빌드할 수도 있습니다.
그래서 다양한 환경에서 개발 주기 전반에 걸쳐 버전을 올리고 서비스를 배포하는 것이 쉬워집니다.

Gradle을 사용할 경우 `./gradlew bootRun` 명령어로 실행할 수 있습니다. 또는 `./gradlew build` 명령어로 JAR파일을 만들고 다음과 같이 JAR파일을 실행할 수 있습니다.

```
java -jar build/libs/gs-validating-form-input-0.1.0.jar
```

Maven을 사용할 경우 `./mvnw spring-boot:run` 명령어로 실행할 수 있습니다. 또는 `./mvnw clean package` 명령어로 JAR파일을 만들고 다음과 같이 JAR파일을 실행할 수 있습니다.

```
java -jar target/gs-validating-form-input-0.1.0.jar
```

> 이 행동은 실행가능한 JAR파일을 만드는 방법입니다. 물론 [WAR파일 만드는 방법](http://spring.io/guides/gs/convert-jar-to-war/)도 대신 선택할 수 있습니다.

이 어플리케이션은 잠시 후 실행됩니다.

웹브라우저에서 http://localhost:8080/을 입력하면 다음과 같이 볼 수 있습니다.

![](http://spring.io/guides/gs/validating-form-input/images/valid-01.png)

이름에 **A**를 입력하고 나이에 **15**를 입력하고 **Submit**을 클릭해 봅시다.

![](http://spring.io/guides/gs/validating-form-input/images/valid-02.png)

![](http://spring.io/guides/gs/validating-form-input/images/valid-03.png)

`PersonForm`클래스의 유효한 입력조건에 맞지않아 메인페이지로 강제이동된 것을 볼 수 있습니다. 입력란을 비우고 Submit 버튼을 누르면 또 다른 오류가 발생합니다.

![](http://spring.io/guides/gs/validating-form-input/images/valid-04.png)

유효한 이름과 나이를 입력하고 Submit 버튼을 누르면 `results` 페이지를 만날 수 있습니다.

![](http://spring.io/guides/gs/validating-form-input/images/valid-05.png)

## 결론
축하합니다! 유효성을 체크가 내장되어 있는 domain object를 활용하여 간단한 웹 어플리케이션을 만들었습니다. 이 방법대로 하면 데이터가 특정 기준을 충족시키고 사용자가 올바르게 입력했는지 확인할 수 있습니다.

## 참고
이 가이드들을 따라하면 도움이 될 것입니다:
* [Serving Web Content with Spring MVC](https://spring.io/guides/gs/serving-web-content/)
* [Handling Form Submission](https://spring.io/guides/gs/handling-form-submission/)
* [Securing a Web Application](https://spring.io/guides/gs/securing-web/)
* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)