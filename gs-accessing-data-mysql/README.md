## MySQL 데이터 접근하기
이 가이드는 다른 모든 가이드와 많은 샘플 애플리케이션이 사용하는 메모리 내장형 데이터베이스와 달리 MySQL 데이터베이스와 연결된 스프링 애플리케이션을 만드는 과정을 안내합니다. 이 가이드는 Spring Data JPA를 사용하여 데이터베이스에 접근하지만, Spring Data JPA는 많은 가능한 선택 사항 중 하나 일뿐입니다 (예 : 일반 Spring JDBC를 사용할 수 있음).

## 목표
먼저 MySQL 데이터 베이스, 스프링 애플리케이션를 만들고 새로 생성된 데이터베이스와 연결을 목표로 합니다.

> MySQL은 GPL로 라이선스가 부여되어 있으므로 이를 사용하여 배포하는 모든 프로그램 바이너리도 GPL을 사용해야합니다. 자세한 내용은 [GNU General Public Licence](https://www.gnu.org/licenses/gpl.html)을 참고하세요.

## 요구사항
* [MySQL](https://dev.mysql.com/downloads/) 버전 5.6 또는 최신버전. 도커가 설치되어있는 경우 데이터베이스를 [컨테이너](https://hub.docker.com/_/mysql/)로 실행하는 것이 유용 할 수 있습니다.
* 약 15분
* 좋아하는 텍스트 에디터 또는 자신있는 개발환경
* [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 또는 최신버전의 JDK
* [Gradle 4+](http://www.gradle.org/downloads) 또는 [Maven 3.2+](https://maven.apache.org/download.cgi)
* 다운로드 가능한 IDE:
  - [Spring Tool Suite (STS)](http://spring.io/guides/gs/sts)
  - [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea/)

## 이 가이드를 완성하는 방법
대부분의 Spring Getting Started 가이드와 마찬가지로, 처음부터 시작하여 각 단계를 완료하거나 이미 익숙한 기본 설정 단계를 건너 뛸 수 있습니다.

**처음부터 시작하려면**, [Gradle_빌드하기](#Gradle_빌드하기).

**기초 건너뛰기** :

* [다운로드](https://github.com/spring-guides/gs-accessing-data-mysql/archive/master.zip)하여 압축을 풀거나 레파지토리를 클론하여 사용하세요 [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-accessing-data-mysql.git`
* cd 로 폴더 이동 `gs-accessing-data-mysql/initial`
* 이동하기 [데이터베이스_생성하기](#create-the-database).

**완성 했을 경우**, `gs-accessing-data-mysql/complete`의 코드와 비교하여 결과를 확인할 수 있습니다.

## Gradle_빌드하기
먼저 기본 빌드 스크립트를 설정합니다. Spring을 사용하여 응용 프로그램을 빌드 할 때 원하는 빌드 시스템을 사용할 수 있지만 [Gradle](http://gradle.org/)을 사용하여 작업해야하는 코드와 [Maven](https://maven.apache.org/)을 사용하여 작업해야하는 방법이 포함되어 있습니다. 익숙하지 않은 경우 [Gradle을 사용하여 Java 프로젝트 빌드하기](http://spring.io/guides/gs/gradle) 또는 [Maven을 사용하여 Java 프로젝트 빌드하기](http://spring.io/guides/gs/maven) 를 참조하십시오.

### 디렉토리 구조 만들기
선택한 프로젝트 디렉토리에서 다음과 같은 하위 디렉토리 구조를 만듭니다. *nix 시스템에서는 `mkdir -p src/main/java/hello` 를 사용합니다 :

```
└── src
    └── main
        └── java
            └── hello
```

### Gradle 빌드 파일 만들기
아래는 [초기 Gradle 빌드 파일](https://github.com/spring-guides/gs-accessing-data-mysql/blob/master/initial/build.gradle)입니다.

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
    baseName = 'gs-accessing-data-mysql'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")

    // JPA Data (We are going to use Repositories, Entities, Hibernate, etc...)
    compile 'org.springframework.boot:spring-boot-starter-data-jpa'

    // Use MySQL Connector-J
    compile 'mysql:mysql-connector-java'

    testCompile('org.springframework.boot:spring-boot-starter-test')
}
```

[Spring Boot gradle plugin](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html)은 많은 편리한 기능을 제공합니다 :

* 클래스패스와 빌드파일을 모으고 실행할 수있는 "über-jar"를 하나 만들고 실행하여 서비스를 실행하고 전송하는 것이 더 편리합니다.
* 이것은 `public static void main()`메소드를 검색하여 실행 가능한 클래스로 변환한다.
* [스프링 부트 의존성](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml)에 맞게 버전 번호를 설정하는 빌트인 의존성 분석기를 제공합니다. 원하는 모든 버전을 무시할 수 있지만 기본적으로 Boot에서 선택한 버전 세트가 사용됩니다.

## Maven으로 빌드
먼저 기본 빌드 스크립트를 설정합니다. Spring을 사용하여 응용 프로그램을 빌드 할 때 원하는 빌드 시스템을 사용할 수 있지만 [Maven](https://maven.apache.org/)을 사용하여 작업해야하는 코드가 여기에 포함됩니다. Maven에 익숙하지 않은 경우 [Maven으로 Java 프로젝트 빌드하기](http://spring.io/guides/gs/maven)를 참조하십시오. 

### 디렉토리 구조 만들기
선택한 프로젝트 디렉토리에서 다음과 같은 하위 디렉토리 구조를 만듭니다. *nix 시스템에서는 `mkdir -p src/ main/java/hello` 를 사용합니다 :

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
    <artifactId>gs-mysql-data</artifactId>
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

        <!-- JPA Data (We are going to use Repositories, Entities, Hibernate, etc...) -->

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <!-- Use MySQL Connector-J -->

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
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

[Spring Boot Maven plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin) 플러그인은 많은 편리한 기능을 제공합니다 :

* 클래스패스와 빌드파일을 모으고 실행할 수있는 "über-jar"를 하나 만들고 실행하여 서비스를 실행하고 전송하는 것이 더 편리합니다.
* 이것은 `public static void main()`메소드를 검색하여 실행 가능한 클래스로 변환한다.
* [스프링 부트 의존성](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml)에 맞게 버전 번호를 설정하는 빌트인 의존성 분석기를 제공합니다. 원하는 모든 버전을 무시할 수 있지만 기본적으로 Boot에서 선택한 버전 세트가 사용됩니다.

## IDE로 빌드하기
* [Spring Tool Suite로 직접 빌드하는 방법](http://spring.io/guides/gs/sts/)을 읽어보십시오.
* [IntelliJ IDEA로 직접 빌드하는 방법](http://spring.io/guides/gs/intellij-idea)을 읽어보십시오.

## 데이터베이스 생성하기
터미널로 이동하십시오. (Microsoft Windows의 프롬프트 `cmd`) 이후 새로운 사용자를 생성 할 수 있는 사용자로 MySQL 클라이언트를 엽니다.

예시 : Linux에서는 다음 명령을 사용하십시오.

```bash
$ sudo mysql --password
```

> 
MySQL을 루트로 연결합니다. **이것은 프로덕션 서버에 권장되는 방법은 아닙니다.**


새 데이터베이스 생성하기

```bash
mysql> create database db_example; -- 새 데이터베이스 만들기
mysql> create user 'springuser'@'localhost' identified by 'ThePassword'; -- 사용자 생성하기
mysql> grant all on db_example.* to 'springuser'@'localhost'; -- 생성된 사용자에게 모든 권한을 부여하기
```

## `application.properties` 파일을 만들기
Spring Boot는 모든 것에 기본값을 주며, 데이터베이스의 기본값은 `H2` 이므로이 값을 변경하고 다른 데이터베이스를 사용하려면 `application.properties` 파일에 연결 속성을 정의해야합니다.

sources 폴더에서, `src/main/resources/application.properties` 리소스 파일을 생성합니다.

```properties
spring.jpa.hibernate.ddl-auto=create
spring.datasource.url=jdbc:mysql://localhost:3306/db_example
spring.datasource.username=springuser
spring.datasource.password=ThePassword
```

여기서 `spring.jpa.hibernate.ddl-auto` 는 `none` , `update` , `create` , `create-drop` 일 수 있습니다. 자세한 내용은 Hibernate 문서를 참조하십시오.

* `none` 이것이 MySQL의 기본값이며, 데이터베이스 구조는 변경되지 않습니다.
* `update` Hibernate는 주어진 Entity 구조에 따라 데이터베이스를 변경합니다.
* `create` 매번 데이터베이스를 생성하지만 닫을 때 데이터베이스를 삭제하지 않습니다.
* `create-drop` 데이터베이스를 생성 한 후 `SessionFactory`가 닫힐 때 데이터베이스를 삭제합니다.

우리는 아직 데이터베이스 구조가 없기 때문에 `create`로 구조를 생성합니다. 첫 번째 실행 후 프로그램 요구 사항에 따라 `update` 또는 `none`으로 전환 할 수 있습니다. 데이터베이스 구조를 약간 변경하려면 `update`를 사용하십시오.

`H2`와 다른 내장 데이터베이스의 기본값은 `create-drop`이지만, `MySQL`과 같은 것들은 `none`입니다.

데이터베이스를 프로덕션 상태로 만든 후에는이 옵션을 `none` 으로 만들고 Spring 응용 프로그램에 연결된 MySQL 사용자의 모든 권한을 취소 한 다음 SELECT, UPDATE, INSERT, DELETE 만 제공하는 것이 좋습니다.

자세한 내용은 이 가이드의 끝 부분에 나와 있습니다.

## `@Entity` 모델 만들기

`src/main/java/hello/User.java`

```java
package hello;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity // Entity 어노테이션은 Hibernate에게 이 클래스에서 테이블을 만들도록 지시한다.
public class User {
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Integer id;

    private String name;

    private String email;

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}


}
```

위 모델은 Hibernate가 자동으로 테이블로 변환하는 엔티티 클래스입니다.

## repository만들기

`src/main/java/hello/UserRepository.java`

```java
package hello;

import org.springframework.data.repository.CrudRepository;

import hello.User;

// 이것은 AUTO에 의해 자동으로 Spring에 의해 userRepository라는 Bean에 구현 될 것입니다.
// CRUD는 생성, 읽기, 업데이트, 삭제를 나타냅니다.

public interface UserRepository extends CrudRepository<User, Integer> {

}
```

위 코드는 리포지터리 (repository) 인터페이스입니다. 대문자를 변경해, 같은 이름의 bean로 Spring에 의해 자동적으로 구현됩니다. bean명은 `userRepository`입니다.

## Spring 애플리케이션을위한 새로운 컨트롤러 생성하기

`src/main/java/hello/MainController.java`

```java
package hello;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

import hello.User;
import hello.UserRepository;

@Controller    // Controller 어노테이션은 클래스가 컨트롤러라는 것을 의미합니다.
@RequestMapping(path="/demo") // URL이 demo로 시작한다는 것을 의미합니다 (응용 프로그램 경로 이후).
public class MainController {
	@Autowired // userRepository라는 빈을 얻는다는 것을 의미합니다.
	           // Spring에 의해 자동으로 생성 된 데이터를 처리하기 위해 사용합니다.
	private UserRepository userRepository;

	@GetMapping(path="/add") // GET 방식으로 요청하기
	public @ResponseBody String addNewUser (@RequestParam String name
			, @RequestParam String email) {
		// @ResponseBody는 반환 된 문자열이 뷰 이름이 아니라 요청값임을 의미합니다.
		// @RequestParam은 GET 또는 POST 요청의 매개 변수임을 의미합니다.

		User n = new User();
		n.setName(name);
		n.setEmail(email);
		userRepository.save(n);
		return "Saved";
	}

	@GetMapping(path="/all")
	public @ResponseBody Iterable<User> getAllUsers() {
		// 이렇게하면 사용자에게 JSON 또는 XML이 반환됩니다.
		return userRepository.findAll();
	}
}
```

> 위의 예제는 `@GetMapping`이 `@RequestMapping(method = GET)`의 바로 가기이므로 `GET`과 `PUT`, `POST` 등을 명시 적으로 지정하지 않았습니다. `@RequestMapping`은 기본적으로 모든 HTTP 연산을 매핑합니다. 이 매핑을 줄이려면`@RequestMapping(method = GET)`또는 다른 어노테이션을 사용하십시오.

## 애플리케이션을 실행 가능하게 만들기
이 서비스를 외부 응용 프로그램 서버에 배포하기위한 기존 [WAR](http://spring.io/understanding/WAR)  파일로 패키지화 할 수 있지만 아래에 설명 된 간단한 방법은 독립 실행 형 응용 프로그램을 만듭니다. 자바 `main()`메소드로 구동되는, 실행 가능한 단일 JAR 파일로 모든 것을 패키지화합니다. 도중에, 외부 인스턴스로 전개하는 대신 [Tomcat](http://spring.io/understanding/Tomcat) 서블릿 컨테이너를 HTTP 런타임으로 임베딩하기위한 Spring의 지원을 사용한다.

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

## 실행 가능한 JAR로 빌드하기
Gradle 또는 Maven을 사용하여 명령 줄에서 응용 프로그램을 실행할 수 있습니다. 또는 모든 필요한 종속성, 클래스 및 자원을 포함하는 단일 실행 가능 JAR 파일을 빌드하고 실행할 수 있습니다. 따라서 개발 수명주기, 다양한 환경에 걸쳐 응용 프로그램으로 서비스를 쉽게 배포, 버전 및 배포 할 수 있습니다.

Gradle을 사용하는 경우, `./gradlew bootRun`을 사용하여 응용 프로그램을 실행할 수 있습니다. 또는 `./gradlew build`를 사용하여 JAR 파일을 빌드 할 수 있습니다. 그런 다음 JAR 파일을 실행할 수 있습니다 :

```
java -jar build/libs/gs-accessing-data-mysql-0.1.0.jar
```

Maven을 사용한다면 `./mvnw spring-boot:run`을 사용하여 응용 프로그램을 실행할 수 있습니다. 또는 `./mvnw clean package`로 JAR 파일을 빌드 할 수 있습니다. 그런 다음 JAR 파일을 실행할 수 있습니다 :

```
java -jar target/gs-accessing-data-mysql-0.1.0.jar
```

> 
위의 절차는 실행 가능한 JAR을 생성합니다. 대신 [고전적인 WAR 파일을 빌드](http://spring.io/guides/gs/convert-jar-to-war/)하도록 선택할 수도 있습니다

로깅 출력이 표시됩니다. 몇 초 내에 서비스가 실행됩니다.

## 응용 프로그램 테스트
이제 응용 프로그램이 실행 중이므로 이를 테스트 할 수 있습니다.


예를 들어, `curl`을 사용하십시오. 이제 테스트 할 수 있는 2 개의 REST 웹 서비스가 있습니다.

`localhost:8080/demo/all` 모든 데이터를 얻어오기 또는 `localhost:8080/demo/add` 하나의 사용자를 데이터에 추가하기

```bash
$ curl 'localhost:8080/demo/add?name=First&email=someemail@someemailprovider.com'
```

리턴값

```bash
Saved
```

```bash
$ curl 'localhost:8080/demo/all'
```

리턴값

```json
[{"id":1,"name":"First","email":"someemail@someemailprovider.com"}]
```

## 보안 변경하기
이제 프로덕션 환경에서 SQL 삽입 공격에 노출 될 수 있습니다. 해커는 `DROP TABLE`또는 다른 파괴적인 SQL 명령을 삽입 할 수 있습니다. 따라서 보안 사례로서 응용 프로그램을 사용자에게 노출하기 전에 데이터베이스에 변경 사항을 적용하십시오.

```bash
mysql> revoke all on db_example.* from 'springuser'@'localhost';
```

이것은 Spring 애플리케이션과 관련된 사용자로부터 모든 권한을 취소합니다. 이제 Spring 애플리케이션은 **데이터베이스에서** 아무 것도 할 수 없습니다.

```bash
mysql> grant select, insert, delete, update on db_example.* to 'springuser'@'localhost';
```

이렇게하면 Spring 애플리케이션에 구조 (스키마)가 아닌 데이터베이스의 **데이터만** 변경하는 데 필요한 권한 만 부여됩니다.

이제 당신의 `src/main/resources/application.properties`에서 변경하십시오.

```properties
spring.jpa.hibernate.ddl-auto=none
```

위 코드는 Hibernate가 엔티티들로부터 테이블을 생성하기위한 첫 번째 실행에 있던 `create` 대신에 사용됩니다.

데이터베이스를 변경하려면 권한을 변경하고 `spring.jpa.hibernate.ddl-auto` 파일을 `update` 파일로 변경 한 다음 응용 프로그램을 다시 실행하고 반복하십시오. 또는 Flyway 또는 Liquibase와 같은 전용 마이그레이션 도구를 사용하십시오.

## 요약
축하합니다! 당신은 방금 MySQL 데이터베이스에 바인딩 된 Spring 애플리케이션을 개발했습니다.

## 참고 사항

다음 가이드도 도움이 될 수 있습니다 :

* [JPA로 데이터 액세스](https://spring.io/guides/gs/accessing-data-jpa/)
* [MongoDB로 데이터 액세스](https://spring.io/guides/gs/accessing-data-mongodb/)
* [Gemfire로 데이터 액세스](https://spring.io/guides/gs/accessing-data-gemfire/)