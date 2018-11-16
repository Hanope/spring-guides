## Messaging with Redis
이 가이드는 Spring Data Redis를 사용하여 Redis를 통해 전송된 메시지를 게시하고 구독하는 과정을 안내한다.

## 무엇을 만들게 되는가
`StringRedisTemplate`을 사용하여 문자열 메시지를 게시하고 `MessageListenerAdapter`를 사용하여 [POJO](http://spring.io/understanding/POJO)를 구독하는 어플리케이션을 만들 것이다.

> Spring Data Redis를 메시지 게시 수단으로 사용하는 것이 이상하게 들릴 수도 있지만, 보시다시피 Redis는 NoSQL 데이터 저장소뿐만 아니라 메시징 시스템도 제공한다.

## 무엇이 필요한가
* 약 15분
* 선호하는 텍스트 에디터 혹은 IDE
* [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 혹은 그 이상
* [Gradle 4+](http://www.gradle.org/downloads) 혹은 [Maven 3.2+](https://maven.apache.org/download.cgi)
* 코드를 IDE로 직접 가져올 수도 있다:
  - [Spring Tool Suite (STS)](http://spring.io/guides/gs/sts)
  - [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea/)
    - Redis server (아래 설치 지침)

## 이 가이드를 완료하는 방법
대부분의 [Spring Getting Started](http://spring.io/guides) 가이드와 마찬가지로, 처음부터 시작하여 각 단계를 완료하거나 이미 익숙한 기본 설정 단계를 건너뛸 수 있다. 어느 쪽이든 코드가 작동한다.

**처음부터 시작하려면** [Gradle로 빌드하기](#Gradle로-빌드하기)로 이동하시오.

**기본 사항을 건너뛰려면** 다음을 수행하시오:

* 이 가이드의 소스 저장소를 [다운로드](https://github.com/spring-guides/gs-messaging-redis/archive/master.zip)하여 압축을 풀거나 [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-messaging-redis.git` 을 사용하여 clone 하시오.
* cd into `gs-messaging-redis/initial`
* [Redis message receiver 만들기](#Redis-message-receiver-만들기)로 넘어가시오.

**끝나면**, `gs-messaging-redis/complete` 코드와 결과를 비교할 수 있다.

## Gradle로 빌드하기
먼저 기본 빌드 스크립트를 설정한다. Spring으로 app을 만들 때 원하는 빌드 시스템을 사용할 수 있지만 [Gradle](http://gradle.org/) 및 [Maven](https://maven.apache.org/)으로 작업하는 데 필요한 코드가 여기에 있다. 둘다 익숙하지 않은 경우 [Gradle로 Java 프로젝트 만들기](http://spring.io/guides/gs/gradle) 또는 [Maven으로 Java 프로젝트 만들기](http://spring.io/guides/gs/maven)를 참조하시오.

### 디렉토리 구조 만들기
선택한 프로젝트 디렉토리에서 *nix 시스템의 `mkdir -p src/main/java/hello` 를 사용하여 다음 하위 디렉토리 구조를 작성한다:

```
└── src
    └── main
        └── java
            └── hello
```

### Gradle 빌드 파일 만들기
아래는 [초기 Gradle 빌드 파일](https://github.com/spring-guides/gs-messaging-redis/blob/master/initial/build.gradle)이다.

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
    baseName = 'gs-messaging-redis'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter")
    compile("org.springframework.boot:spring-boot-starter-data-redis")
    testCompile("junit:junit")
}
```

[Spring Boot gradle plugin](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html)은 다음과 같은 여러 편리한 기능을 제공한다:

* 클래스 경로에 있는 모든 jar를 모으고 서비스를 전송하고 실행하는데 좀 더 편하게 만들어주는 실행 가능한 "über-jar"를 빌드한다.
* `public static void main()` 메소드를 검색하여 실행 가능 클래스로 플래그를 지정한다.
* [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml)와 일치하도록 버전 번호를 설정하는 빌트인 의존성 리졸버를 제공한다. 모든 버전을 재정의할 수 있지만 기본적으로 Boot에서 선택한 버전으로 설정된다.

## Maven으로 빌드하기
먼저 기본 빌드 스크립트를 설정한다. Spring으로 app을 만들 때 원하는 빌드 시스템을 사용할 수 있지만 [Maven](https://maven.apache.org/)으로 작업하는 데 필요한 코드가 여기에 있다. Maven이 익숙하지 않은 경우 [Maven으로 Java 프로젝트 만들기](http://spring.io/guides/gs/maven)를 참조하시오.

### 디렉토리 구조 만들기
선택한 프로젝트 디렉토리에서 *nix 시스템의 `mkdir -p src/main/java/hello`를 사용하여 다음 하위 디렉토리 구조를 작성한다:

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
    <artifactId>gs-messaging-redis</artifactId>
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
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
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

[Spring Boot Maven plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin)은 다음과 같은 여러 편리한 기능을 제공한다:

* 클래스 경로에 있는 모든 jar를 모으고 서비스를 전송하고 실행하는데 좀 더 편하게 만들어주는 실행 가능한 "über-jar"를 빌드한다.
* `public static void main()` 메소드를 검색하여 실행 가능 클래스로 플래그를 지정한다.
* [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml)와 일치하도록 버전 번호를 설정하는 빌트인 의존성 리졸버를 제공한다. 모든 버전을 재정의할 수 있지만 기본적으로 Boot에서 선택한 버전으로 설정된다.

## IDE로 빌드하기
* [Spring Tool Suite](http://spring.io/guides/gs/sts/)에서 import 하는 가이드를 읽으시오.
* [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea)에서 가이드를 읽으시오.

## Redis server 시작하기
메시징 어플리케이션을 빌드하기 전에 메시지 수신 및 송신을 처리할 서버를 설정해야 한다.

Redis는 메시징 시스템과 함께 제공되는 BSD 라이센스의 key-value 데이터 스토어 오픈소스이다. 이 서버는 https://redis.io/download 에서 무료로 사용할 수 있다. 수동으로 다운로드하거나 homebrew가 설치된 Mac을 사용하는 경우 다음을 입력하여 사용할 수 있다:

```
brew install redis
```

Redis 압축을 풀면 기본 설정으로 시작할 수 있다.

```
redis-server
```

다음과 같은 메시지가 나타난다.

```
[35142] 01 May 14:36:28.939 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
[35142] 01 May 14:36:28.940 * Max number of open files set to 10032
                _._
              _.-``__ ''-._
        _.-``    `.  `_.  ''-._           Redis 2.6.12 (00000000/0) 64 bit
    .-`` .-```.  ```\/    _.,_ ''-._
  (    '      ,       .-`  | `,    )     Running in stand alone mode
  |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
  |    `-._   `._    /     _.-'    |     PID: 35142
    `-._    `-._  `-./  _.-'    _.-'
  |`-._`-._    `-.__.-'    _.-'_.-'|
  |    `-._`-._        _.-'_.-'    |           http://redis.io
    `-._    `-._`-.__.-'_.-'    _.-'
  |`-._`-._    `-.__.-'    _.-'_.-'|
  |    `-._`-._        _.-'_.-'    |
    `-._    `-._`-.__.-'_.-'    _.-'
        `-._    `-.__.-'    _.-'
            `-._        _.-'
                `-.__.-'

[35142] 01 May 14:36:28.941 # Server started, Redis version 2.6.12
[35142] 01 May 14:36:28.941 * The server is now ready to accept connections on port 6379
```

## Redis message receiver 만들기
메시징 기반 어플리케이션에는 메시지 게시자와 메시징 수신자가 있다. 메시지 수신자(receiver)를 작성하려면 메시지에 응답하는 메소드가 있는 수신자(receiver)를 구현하시오.

`src/main/java/hello/Receiver.java`

```java
package hello;

import java.util.concurrent.CountDownLatch;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;

public class Receiver {
    private static final Logger LOGGER = LoggerFactory.getLogger(Receiver.class);

    private CountDownLatch latch;

    @Autowired
    public Receiver(CountDownLatch latch) {
        this.latch = latch;
    }

    public void receiveMessage(String message) {
        LOGGER.info("Received <" + message + ">");
        latch.countDown();
    }
}
```

`Receiver`는 메시지를 수신하는 메소드를 정의하는 간단한 POJO이다. `Receiver`를 메시지 리스너로 등록할 때 볼 수 있듯이, 원하는대로 message-handling 메소드의 이름을 지정할 수 있다.

> 시연용에는 countdown latch가 생성자에 의해 autowired 된다. 이렇게하면 메시지를 받았을 때 신호를 보낼 수 있다.

## listener 등록 및 메시지 보내기
Spring Data Redis는 Redis를 사용하여 메시지를 주고받는 데 필요한 모든 컴포넌트를 제공한다. 특히, 다음을 구성해야 한다:

* connection factory
* message listener container
* Redis template

Redis template을 사용하여 메시지를 보내고 message listener container에 `Receiver`를 등록하여 메시지를 수신한다. connection factory는 템플릿과 message listner container를 모두 구동하여 Redis 서버에 연결할 수 있도록 한다.

이 예제는 [Jedis](https://github.com/xetorthio/jedis) Redis 라이브러리를 기반으로 하는 `JedisConnectionFactory`의 인스턴스인 Spring Boot의 기본 `RedisConnectionFactory`를 사용한다. connection factory는 message listener container와 Redis template에 주입된다.

`src/main/java/hello/Application.java`

```java
package hello;

import java.util.concurrent.CountDownLatch;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.data.redis.listener.PatternTopic;
import org.springframework.data.redis.listener.RedisMessageListenerContainer;
import org.springframework.data.redis.listener.adapter.MessageListenerAdapter;

@SpringBootApplication
public class Application {

	private static final Logger LOGGER = LoggerFactory.getLogger(Application.class);

	@Bean
	RedisMessageListenerContainer container(RedisConnectionFactory connectionFactory,
			MessageListenerAdapter listenerAdapter) {

		RedisMessageListenerContainer container = new RedisMessageListenerContainer();
		container.setConnectionFactory(connectionFactory);
		container.addMessageListener(listenerAdapter, new PatternTopic("chat"));

		return container;
	}

	@Bean
	MessageListenerAdapter listenerAdapter(Receiver receiver) {
		return new MessageListenerAdapter(receiver, "receiveMessage");
	}

	@Bean
	Receiver receiver(CountDownLatch latch) {
		return new Receiver(latch);
	}

	@Bean
	CountDownLatch latch() {
		return new CountDownLatch(1);
	}

	@Bean
	StringRedisTemplate template(RedisConnectionFactory connectionFactory) {
		return new StringRedisTemplate(connectionFactory);
	}

	public static void main(String[] args) throws InterruptedException {

		ApplicationContext ctx = SpringApplication.run(Application.class, args);

		StringRedisTemplate template = ctx.getBean(StringRedisTemplate.class);
		CountDownLatch latch = ctx.getBean(CountDownLatch.class);

		LOGGER.info("Sending message...");
		template.convertAndSend("chat", "Hello from Redis!");

		latch.await();

		System.exit(0);
	}
}
```

`listenerAdapter` 메소드에 정의된 bean은 `conatiner`에 정의된 message listener container에 메시지 리스너로 등록되고 "chat" 토픽의 메시지를 청취한다. `Receiver` 클래스는 POJO이므로, `addMessageListener()`에 의해 요구된 `MessageListener` 인터페이스를 구현하는 message listener adapter에 래핑되어야 한다. 또한 message listener adapter는 메시지가 도착할 때 `Receiver`에 대해 `receiveMessage()` 메소드를 호출하도록 구성된다.

connection factory와 message listener container bean만 있으면 메시지를 수신할 수 있다. 메시지를 보내려면 Redis template도 필요하다. key와 value가 모두 `String`인 Redis의 일반적인 사용에 초첨을 맞춘 `RestTemplate`을 구현하는 `StringRedisTemplate`으로 구성된 빈이 있다.

`main()` 메소드는 Spring application context를 생성하여 모든 것을 작동시킨다. application context는 message listener container를 시작하고 message listener container bean은 메시지 수신을 시작한다. `main()` 메소드는 application context에서 `StringRedisTemplate` bean을 검색하고 그것을 사용하여 "chat" 토픽에 "Hello from Redis!" 메시지를 보내는 데 사용한다. 마지막으로, Spring application context를 닫고 어플리케이션이 종료된다.

## 실행 가능한 JAR 만들기
Gradle 또는 Maven을 사용하여 커맨드 라인에서 어플리케이션을 실행할 수 있다. 또는 모든 필요한 의존성, 클래스 및 리소스 포함하는 단일 실행 가능한 JAR 파일을 빌드하고 실행할 수 있다. 따라서 개발 생명주기(life cycle), 다양한 환경에 걸쳐 어플리케이션으로 서비스를 쉽게 제공 및 배포할 수 있다.

Gradle을 사용하는 경우 `./gradlew bootRun`을 사용하여 어플리케이션을 실행할 수 있다. 또는 `./gradlew build`를 사용하여 JAR 파일을 작성할 수 있다. 그런 다음 JAR 파일을 실행할 수 있다:

```
java -jar build/libs/gs-messaging-redis-0.1.0.jar
```

Maven을 사용하는 경우, `./mvnw spring-boot:run`을 사용하여 어플리케이션을 실행할 수 있다. 또는 `./mvnw clean package`로 JAR 파일을 빌드할 수 있다. 그런다음 JAR 파일을 실행할 수 있다:

```
java -jar target/gs-messaging-redis-0.1.0.jar
```

> 위 절차는 실행 가능한 JAR를 생성한다. 고전의 [WAR 파일을 빌드](http://spring.io/guides/gs/convert-jar-to-war/)하도록 선택할 수도 있다.

다음 출력을 볼 수 있다.:

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.0.5.RELEASE)

2014-04-18 08:03:34.032  INFO 47002 --- [           main] hello.Application                        : Starting Application on retina with PID 47002 (/Users/gturnquist/src/spring-guides/gs-messaging-redis/complete/target/classes started by gturnquist)
2014-04-18 08:03:34.062  INFO 47002 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@7a53c84a: startup date [Fri Apr 18 08:03:34 CDT 2014]; root of context hierarchy
2014-04-18 08:03:34.326  INFO 47002 --- [           main] o.s.c.support.DefaultLifecycleProcessor  : Starting beans in phase 2147483647
2014-04-18 08:03:34.357  INFO 47002 --- [           main] hello.Application                        : Started Application in 0.605 seconds (JVM running for 0.899)
2014-04-18 08:03:34.357  INFO 47002 --- [           main] hello.Application                        : Sending message...
2014-04-18 08:03:34.370  INFO 47002 --- [    container-2] hello.Receiver                           : Received <Hello from Redis!>
2014-04-18 08:03:34.379  INFO 47002 --- [       Thread-1] s.c.a.AnnotationConfigApplicationContext : Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@7a53c84a: startup date [Fri Apr 18 08:03:34 CDT 2014]; root of context hierarchy
2014-04-18 08:03:34.380  INFO 47002 --- [       Thread-1] o.s.c.support.DefaultLifecycleProcessor  : Stopping beans in phase 2147483647
```

## 요약
축하합니다! 방금 Spring과 Redis를 사용하여 간단한 게시(publish)와 구독(subscribe) 어플리케이션을 개발했다.

> [Redis support](http://gopivotal.com/products/redis)를 사용할 수 있다.

## 다른 예제들
다음 가이드들도 도움이 될 것이다:

* [Messaging with RabbitMQ](https://spring.io/guides/gs/messaging-rabbitmq/)
* [Messaging with JMS](https://spring.io/guides/gs/messaging-jms/)
* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)