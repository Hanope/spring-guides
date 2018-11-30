## JPA로 데이터 접근하기
이 가이드는 스프링 데이터 JPA를 통해 관계형 데이터베이스(RDB)에서 데이터를 주고 받는 어플리케이션을 만드는 과정으로 안내합니다.

## 무엇을 만들게 되는가?
여러분은 메모리 기반 데이터베이스에서 `Customer` [POJOs](http://spring.io/understanding/POJO)를 활용하여 저장하는 어플리케이션을 만들 것입니다.

## 무엇이 필요한가?
* 약 15분의 시간
* 좋아하는 텍스트 에디터나 IDE
* [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 또는 그 이상
* [Gradle 4+](http://www.gradle.org/downloads) 또는 [Maven 3.2+](https://maven.apache.org/download.cgi)
* 또한 아래의 IDE를 이용해 코드를 바로 불러올 수 있습니다:
  - [Spring Tool Suite (STS)](http://spring.io/guides/gs/sts)
  - [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea/)

## 이 가이드를 완료하는 방법
대부분의 [스프링 시작 가이드](http://spring.io/guides)처럼 처음부터 하나씩 완성해 가거나 익숙한 시작 부분은 건너뛰어도 됩니다. 어느 방법을 선택하시더라도 작업을 진행할 수 있는 코드가 완성됩니다.

**처음부터 작업하실 분**은 [Gradle을 사용한 빌드](#Gradle을-사용한-빌드)로 가세요.

**시작부분을 건너뛰실 분**은 아래방법을 따라하세요.

* [다운로드](https://github.com/spring-guides/gs-accessing-data-jpa/archive/master.zip) 하시고 압축파일을 풀어주시거나 [GIT](http://spring.io/understanding/Git)을 사용해 복제해주세요: `git clone https://github.com/spring-guides/gs-accessing-data-jpa.git`
* 소스의 압축을 해제한 폴더에서 `gs-validating-form-input/initial`로 이동합니다.
* [간단한 엔티티 정의하기](#간단한-엔티티-정의하기)로 이동합니다.

**끝나고 나면**, `gs-accessing-data-jpa/complete`의 코드와 비교해서 맞는지 확인해보세요.

## Gradle을 사용한 빌드
첫 번째로 여러분은 빌드 스크립트를 만들어야 합니다. 여러분은 스프링 어플리케이션을 빌드 할 때 원하는 시스템을 사용할 수 있습니다만, 코드가 작동하기 위해서는 [Gradle](http://gradle.org/)이나 [Maven](https://maven.apache.org/)이 포함되어 있어야 합니다. Gradle이나 Maven에 익숙하지 않은 경우, [Building Java Projects with Gradle](http://spring.io/guides/gs/gradle) 또는 [Building Java Projects with Maven](http://spring.io/guides/gs/maven)을 참조하세요.

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
    baseName = 'gs-accessing-data-jpa'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
    maven { url "https://repository.jboss.org/nexus/content/repositories/releases" }
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-data-jpa")
    compile("com.h2database:h2")
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
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-accessing-data-jpa</artifactId>
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
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
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

    <repositories>
        <repository>
            <id>spring-releases</id>
            <name>Spring Releases</name>
            <url>https://repo.spring.io/libs-release</url>
        </repository>
        <repository>
            <id>org.jboss.repository.releases</id>
            <name>JBoss Maven Release Repository</name>
            <url>https://repository.jboss.org/nexus/content/repositories/releases</url>
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
* 이 가이드를 STS에 직접 삽입하는 방법을 알고싶으면 [Spring Tool Suite](http://spring.io/guides/gs/sts/)을 읽어주세요.
* 이 가이드를 Intellij IDEA에서 작동시키는 법을 알고 싶으면 [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea)을 읽어주세요.

## 간단한 엔티티 정의하기
이 예제에서는 JPA의 entity 어노테이션이 지정된 Customer 오브젝트를 만들어서 저장합니다.

`src/main/java/hello/Customer.java`

```java
package hello;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Customer {

    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Long id;
    private String firstName;
    private String lastName;

    protected Customer() {}

    public Customer(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }

    @Override
    public String toString() {
        return String.format(
                "Customer[id=%d, firstName='%s', lastName='%s']",
                id, firstName, lastName);
    }

}
```

여기의 `Customer` 클래스는 `id`, `firstName`, lastName의 세 개의 속성을 가지고 있습니다. 또한 이 클래스는 두 개의 생성자를 가지고 있습니다. 클래스의 기본 생성자는 JPA를 위해서만 존재합니다 그리고 여러분이 생성자를 직접 사용하지 않을것이므로 `protected`로 만들었습니다. 다른 생성자는 여러분이 `Customer` 인스턴스를 생성해서 데이터베이스에 저장하는데 사용될 것입니다.

> 이 가이드에서 간결함을 위해 Getter와 Setter는 넣지 않았습니다.

`@Entity` 어노테이션으로 표시된 `Customer` 클래스는 JPA 엔티티 임을 나타냅니다. `@Table` 어노테이션으로 따로 표시되지 않으므로, `Customer`의 이름을 가진 테이블에 자동으로 매핑됩니다.

`Customer`의 `id` 속성은 `@Id` 어노테이션이 붙어 있어 JPA가 객체의 ID로 이 속성을 사용할 것임을 알 수 있습니다. 그리고 `id` 속성에 붙어 있는 `@GeneratedValue` 어노테이션은 ID가 자동적으로 생성되는 것임을 나타냅니다.

나머지 두개의 `firstName`과 `lastName`는 어노테이션이 없습니다. 이것은 그 속성들이 각각 같은 이름을 가진 컬럼과 매핑되는 것을 의미합니다.

편리한 `toString()` 메소드는 customer 속성들을 출력할 것입니다.

## 간단한 쿼리들 만들기
Spring Data JPA는 JPA를 사용하여 관계형 데이터베이스에 저장하는데 초점이 맞춰져 있습니다. 그리고 이것의 가장 강력한 기능은 시작할 때 저장소(리포지토리) 인터페이스가 자동으로 구현이 되는 것입니다.

`Customer` 엔티티에서 리포지토리 인터페이스을 생성하면서 어떻게 JPA가 작동하는지 보겠습니다.

`src/main/java/hello/CustomerRepository.java`

```java
package hello;

import java.util.List;

import org.springframework.data.repository.CrudRepository;

public interface CustomerRepository extends CrudRepository<Customer, Long> {

    List<Customer> findByLastName(String lastName);
}
```

`CustomerRepository`는 `CrudRepository` 인터페이스를 상속받습니다. `CrudRepository`의 일반적인 파리미터는 엔티티와 ID의 타입을 받으며 엔티티에는 `Customer`를 ID의 타입인 `Long`을 넣어서 작업하였습니다. `CrudRepository`를 상속받은 `CustomerRepository`는 `Customer` 엔티티를 가지고 저장, 삭제, 찾기 등의 다양한 메소드를 가지고 있습니다.

또한 Spring Data JPA는 여러분이 JPA의 간단한 메소드 시그니처(메소드를 생성하기 위한 규칙 중 메소드명과 파라미터)를 활용하여 간단하게 다른 쿼리 메소드를 정의할수도 있습니다. `CustomerRepository`에서는 `findByLastName()` 메소드에서 시그니처를 활용해 정의한 것을 볼 수 있습니다.

전통적인 자바 어플리케이션에서는 `CustomerRepository`를 구현하는 클래스를 생성해야 합니다. 그러나 여러분은 리포지토리 인터페이스를 구현하는 클래스를 작성할 필요가 없습니다. 이것이 Spring Data JPA가 강력하다고 불리는 이유입니다. Spring Data JPA는 여러분이 어플리케이션을 실행할 때 즉석에서 인터페이스 구현을 생성합니다.

생성한 것을 연결하고 어떻게 작동되는지 확인해 봅시다.

## 어플리케이션 클래스 만들기
모든 구성요소가 있는 어플리케이션 클래스를 만들어 보겠습니다.

`src/main/java/hello/Application.java`

```java
package hello;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class Application {

	private static final Logger log = LoggerFactory.getLogger(Application.class);

	public static void main(String[] args) {
		SpringApplication.run(Application.class);
	}

	@Bean
	public CommandLineRunner demo(CustomerRepository repository) {
		return (args) -> {
			// save a couple of customers
			repository.save(new Customer("Jack", "Bauer"));
			repository.save(new Customer("Chloe", "O'Brian"));
			repository.save(new Customer("Kim", "Bauer"));
			repository.save(new Customer("David", "Palmer"));
			repository.save(new Customer("Michelle", "Dessler"));

			// fetch all customers
			log.info("Customers found with findAll():");
			log.info("-------------------------------");
			for (Customer customer : repository.findAll()) {
				log.info(customer.toString());
			}
			log.info("");

			// fetch an individual customer by ID
			repository.findById(1L)
				.ifPresent(customer -> {
					log.info("Customer found with findById(1L):");
					log.info("--------------------------------");
					log.info(customer.toString());
					log.info("");
				});

			// fetch customers by last name
			log.info("Customer found with findByLastName('Bauer'):");
			log.info("--------------------------------------------");
			repository.findByLastName("Bauer").forEach(bauer -> {
				log.info(bauer.toString());
			});
			// for (Customer bauer : repository.findByLastName("Bauer")) {
			// 	log.info(bauer.toString());
			// }
			log.info("");
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

`Application`은 `CustomerRepository`를 통한 몇가지 테스트가 들어있는 `main()` 메소드를 포함하고 있습니다. 첫째로, 스프링의 어플리케이션 컨텍스트에서 `CustomerRepository`를 가져옵니다. 그리고 `save()` 메소드를 시연을 위해 데이터를 세팅해서 소수의 `Customer` 오브젝트를 저장합니다. 다음에, `findAll()` 메소드를 불러 모든 `Customer` 오브젝트를 데이터베이스로 부터 가져옵니다. 그리고 `findOne()` 메소드를 사용하여 ID로 하나의 `Customer` 오브젝트를 가져옵니다. 마지막으로 모든 고객 오브젝트 중 `findByLastName()` 메소드를 활용하여 이름이 "Bauer"인 고객을 가져옵니다.

> 기본적으로, 스프링 부트는 `@SpringBootApplication`이 위치한 패키지와 하위 서브패키지에서만 JPA 리포지토리를 지원합니다. 패키지내부에서 접근불가능한 곳에 정의된 JPA 리포지토리를 사용하려면, `@EnableJpaRepositories`과 파라미터 `basePackageClasses=MyRepository.class`를 사용해 안전하게 쓸 수 있습니다.

## 실행가능한 JAR로 빌드하기
여러분은 Gradle이나 Maven을 이용한 커맨드 라인으로 이 어플리케이션을 실행할 수 있습니다. 또는 모든 필요한 의존성, 클래스, 자원 등을 포함한 하나의 실행 가능한 JAR로 빌드 할 수도 있습니다. 그래서 다양한 환경에서 개발 주기 전반에 걸쳐 버전을 올리고 서비스를 배포하는 것이 쉬워집니다.

Gradle을 사용할 경우 `./gradlew bootRun` 명령어로 실행할 수 있습니다. 또는 `./gradlew build` 명령어로 JAR 파일을 만들고 다음과 같이 JAR 파일을 실행할 수 있습니다.

```
java -jar build/libs/gs-accessing-data-jpa-0.1.0.jar
```

Maven을 사용할 경우 `./mvnw spring-boot:run` 명령어로 실행할 수 있습니다. 또는 `./mvnw clean package` 명령어로 JAR 파일을 만들고 다음과 같이 JAR 파일을 실행할 수 있습니다.

```
java -jar target/gs-accessing-data-jpa-0.1.0.jar
```

> 이 행동은 실행 가능한 JAR 파일을 만드는 방법입니다. 물론 [WAR파일로 만드는 방법](http://spring.io/guides/gs/convert-jar-to-war/)도 대신 선택할 수 있습니다.

실행하면 여러분은 이것을 볼 수 있을겁니다:

```
== Customers found with findAll():
Customer[id=1, firstName='Jack', lastName='Bauer']
Customer[id=2, firstName='Chloe', lastName='O'Brian']
Customer[id=3, firstName='Kim', lastName='Bauer']
Customer[id=4, firstName='David', lastName='Palmer']
Customer[id=5, firstName='Michelle', lastName='Dessler']

== Customer found with findOne(1L):
Customer[id=1, firstName='Jack', lastName='Bauer']

== Customer found with findByLastName('Bauer'):
Customer[id=1, firstName='Jack', lastName='Bauer']
Customer[id=3, firstName='Kim', lastName='Bauer']
```

## 요약
축하합니다! 여러분은 Spring Data JPA를 사용하여 리포지토리를 구체적으로 구현하지 않은 상태로 데이터베이스에 저장하고 가져오는 간단한 어플리케이션을 만들었습니다.

> 여러분이 조금의 노력을 추가해서 JPA 리포지토리를 활용하는 하이퍼미디어 기반의 RESTful한 프론트 엔드에 관심이 있다면, [Accessing JPA Data with REST](http://spring.io/guides/gs/accessing-data-rest)을 읽어보시는 것이 좋습니다.

## 추가로 볼만한 것
다음 가이드 또한 도움이 될 수 있습니다:

* [Accessing Data with Gemfire](https://spring.io/guides/gs/accessing-data-gemfire/)
* [Accessing Data with MongoDB](https://spring.io/guides/gs/accessing-data-mongodb/)
* [Accessing data with MySQL](https://spring.io/guides/gs/accessing-data-mysql/)
* [Accessing Data with Neo4j](https://spring.io/guides/gs/accessing-data-neo4j/)
