## Messaging with RabbitMQ
이 가이드는 메시지를 게시하고 구독하는 RabbitMQ AMQP 서버를 설정하는 과정을 안내한다.

## 무엇을 만들게 되는가?
Spring AMQP의 `RabbitTemplate`을 사용하여 메시지를 게시하고 `MessageListenerAdapter`를 사용하여 [POJO](http://spring.io/understanding/POJO)를 구독하는 어플리케이션을 만들 것이다.

## 무엇이 필요한가?
* 약 15분
* 선호하는 텍스트 에디터 또는 IDE
* [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 혹은 그 이상
* [Gradle 4+](http://www.gradle.org/downloads) 혹은 [Maven 3.2+](https://maven.apache.org/download.cgi)
* 코드를 IDE로 직접 가져올 수도 있다:
  - [Spring Tool Suite (STS)](http://spring.io/guides/gs/sts)
  - [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea/)
    - RabbitMQ server (아래 설치 지침)

## 이 가이드를 완료하는 방법
대부분의 [Spring Getting Started](http://spring.io/guides) 가이드와 마찬가지로, 처음부터 시작하여 각 단계를 완료하거나 이미 익숙한 기본 설정 단계를 건너뛸 수 있다. 어느 쪽이든 코드가 작동한다.

**처음부터 시작하려면** [Gradle로 빌드하기](#Gradle로-빌드하기)로 이동하시오.

**기본 사항을 건너뛰려면** 다음을 수행하시오:

* 이 가이드의 소스 저장소를 [다운로드](https://github.com/spring-guides/gs-messaging-rabbitmq/archive/master.zip)하여 압축을 풀거나 [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-messaging-rabbitmq.git` 을 사용하여 clone 하시오.
* cd into `gs-messaging-rabbitmq/initial`
* [RabbitMQ message receiver 만들기](#RabbitMQ-message-receiver-만들기)로 넘어가시오.

**끝나면**, `gs-messaging-rabbitmq/complete` 코드와 결과를 비교할 수 있다.

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
아래는 [초기 Gradle 빌드 파일](https://github.com/spring-guides/gs-messaging-rabbitmq/blob/master/initial/build.gradle)이다.

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
    baseName = 'gs-messaging-rabbitmq'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-amqp")
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
    <artifactId>gs-messaging-rabbitmq</artifactId>
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
            <artifactId>spring-boot-starter-amqp</artifactId>
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

[Spring Boot Maven plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin)은 다음과 같은 여러 편리한 기능을 제공한다:

* 클래스 경로에 있는 모든 jar를 모으고 서비스를 전송하고 실행하는데 좀 더 편하게 만들어주는 실행 가능한 "über-jar"를 빌드한다.
* `public static void main()` 메소드를 검색하여 실행 가능 클래스로 플래그를 지정한다.
* [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml)와 일치하도록 버전 번호를 설정하는 빌트인 의존성 리졸버를 제공한다. 모든 버전을 재정의할 수 있지만 기본적으로 Boot에서 선택한 버전으로 설정된다.

## IDE로 빌드하기
* [Spring Tool Suite](http://spring.io/guides/gs/sts/)에서 import 하는 가이드를 읽으시오.
* [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea)에서 가이드를 읽으시오.

## RabbitMQ broker 설정
메시징 어플리케이션을 빌드하기 전에 메시지 수신 및 전송을 처리할 서버를 설정해야 한다.

RabbitMQ는 AMQP 서버이다. 서버는 http://www.rabbitmq.com/download.html 에서 무료로 사용할 수 있다. 수동으로 다운로드하거나 homebrew가 설치된 Mac을 사용하는 경우 다음을 입력하여 사용할 수 있다:

```
brew install rabbitmq
```
서버 압축을 풀면 기본 설정으로 시작할 수 있다.

```
rabbitmq-server
```

다음과 같은 메시지가 나타난다.

```
            RabbitMQ 3.1.3. Copyright (C) 2007-2013 VMware, Inc.
##  ##      Licensed under the MPL.  See http://www.rabbitmq.com/
##  ##
##########  Logs: /usr/local/var/log/rabbitmq/rabbit@localhost.log
######  ##        /usr/local/var/log/rabbitmq/rabbit@localhost-sasl.log
##########
            Starting broker... completed with 6 plugins.
```

또한 로컬에서 도커가 실행되는 경우 [Docker Compose](https://docs.docker.com/compose/)를 사용하여 RabbitMQ 서버를 빠르게 시작할 수 있다. Github의 "complete" 프로젝트의 root에 `docker-compose.yml`가 있다. `docker-compose.yml`는 매우 간단하다:

`docker-compose.yml`

```yml
rabbitmq:
  image: rabbitmq:management
  ports:
    - "5672:5672"
    - "15672:15672"
```

현재 디렉토리에 있는 이 파일을 사용하여 `docker-compose`를 실행하면 컨테이너에서 RabbitMQ를 실행할 수 있다.

## RabbitMQ message receiver 만들기
메시징 기반 어플리케이션에서는 게시된 메시지에 응답할 수 있는 수신자(receiver)를 만들어야 한다.

`src/main/java/hello/Receiver.java`

```java
package hello;

import java.util.concurrent.CountDownLatch;
import org.springframework.stereotype.Component;

@Component
public class Receiver {

    private CountDownLatch latch = new CountDownLatch(1);

    public void receiveMessage(String message) {
        System.out.println("Received <" + message + ">");
        latch.countDown();
    }

    public CountDownLatch getLatch() {
        return latch;
    }

}
```

`Receiver`는 메시지를 수신하는 메소드를 정의하는 간단한 POJO이다. 메시지를 수신하기 위해 등록할 때 원하는 이름을 지정할 수 있다.

> 편의상 이 POJO에는 `CountDownLatch`가 있다. 이것은 메시지가 수신되었음을 알릴 수 있다. 이는 프로덕션 어플리케이션에서 구현할 필요가 없다는 것이다..

## listener 등록 및 메시지 보내기
Spring AMQP의 `RabbitTemplate`은 RabbitMQ로 메시지를 주고받는데 필요한 모든 것을 제공한다. 특히 다음을 구성해야 한다:

* message listener container
* 큐, 교환 및 이들 사이의 바인딩 선언
* listener를 테스트하기 위해 일부 메시지를 보내는 component

> Spring Boot는 자동으로 connection factory와 RabbitTemplate을 생성하여 작성해야 하는 코드의 양을 줄여준다.

`RabbitTemplate`을 사용하여 메시지를 보내면 메시지 수신을 위해 Receiver를 message listener conatiner에 등록한다. connection factory는 둘 다 구동하여 RabbitMQ 서버에 연결할 수 있게 한다.

`src/main/java/hello/Application.java`

```java
package hello;

import org.springframework.amqp.core.Binding;
import org.springframework.amqp.core.BindingBuilder;
import org.springframework.amqp.core.Queue;
import org.springframework.amqp.core.TopicExchange;
import org.springframework.amqp.rabbit.connection.ConnectionFactory;
import org.springframework.amqp.rabbit.listener.SimpleMessageListenerContainer;
import org.springframework.amqp.rabbit.listener.adapter.MessageListenerAdapter;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class Application {

    static final String topicExchangeName = "spring-boot-exchange";

    static final String queueName = "spring-boot";

    @Bean
    Queue queue() {
        return new Queue(queueName, false);
    }

    @Bean
    TopicExchange exchange() {
        return new TopicExchange(topicExchangeName);
    }

    @Bean
    Binding binding(Queue queue, TopicExchange exchange) {
        return BindingBuilder.bind(queue).to(exchange).with("foo.bar.#");
    }

    @Bean
    SimpleMessageListenerContainer container(ConnectionFactory connectionFactory,
            MessageListenerAdapter listenerAdapter) {
        SimpleMessageListenerContainer container = new SimpleMessageListenerContainer();
        container.setConnectionFactory(connectionFactory);
        container.setQueueNames(queueName);
        container.setMessageListener(listenerAdapter);
        return container;
    }

    @Bean
    MessageListenerAdapter listenerAdapter(Receiver receiver) {
        return new MessageListenerAdapter(receiver, "receiveMessage");
    }

    public static void main(String[] args) throws InterruptedException {
        SpringApplication.run(Application.class, args).close();
    }

}
```

`@SpringBootApplication` 은 다음을 모두 추가하는 편리한 주석이다:

* `@Configuration` 은 클래스를 어플리케이션 context의 bean 정의 소스로 태그 지정한다.
* `@EnableAutoConfiguration` 은 Spring Boot에게 클래스 경로 설정, 다른 bean 및 다양한 프로퍼티 설정을 기반으로 bean 추가를 시작하도록 한다.
* 일반적으로 `@EnableWebMvc` 를 Spring MVC app에 추가할 것이지만, Spring Boot는 클래스 경로에서 **spring-webmvc**를 발견할 때 그것을 자동으로 추가한다. 이것은 어플리케이션에 웹 어플리케이션으로 플래그를 지정하고 `DispatcherServlet` 설정과 같은 주요 동작을 활성화 한다.
* `@ComponentScan` 은 `hello` 패키지에서 다른 컴포넌트, 구성 및 서비스를 찾아서 컨트롤러를 찾을 수 있도록 Spring에 지시한다.

`main()` 메소드는 `Spring Boot의 SpringApplicaiton.run()` 메소드를 사용하여 어플리케이션을 시작한다. 한줄의 XML이 없다는 것을 알고 있었는가? **web.xml** 파일도 없다. 이 웹 어플리케이션은 100% 순수 자바이며 인프라 구성에 대해 다룰 필요가 없다.

`listenerAdapter()` 메소드에 정의된 bean은 `container()`에 정의 된 컨테이너에 메시지 리스너로 등록된다. 그것은 "spring-boot" 큐에 있는 메시지를 수신한다. `Receiver` 클래스는 POJO이기 때문에 `MessageListenerAdapter`에 감싸야 한다. 여기서 `receiveMessage`를 호출한다.

> JMS 큐와 AMQP 큐는 다른 의미를 가진다. 예를들어 JMS는 대기중인 메시지를 하나의 consumer에게만 보낸다. 반면 AMQP 큐는 동일한 작업을 수행하지만 AMQP producer는 큐에 직접적으로 메시지를 보내지 않는다. 그 대신 메시지가 교환기로 보내지며, 단일 큐로 이동하거나 여러 큐로 팬아웃되어 JMS topic들의 concept를 에뮬레이팅한다. 자세한 것은 [Understanding AMQP](http://spring.io/understanding/AMQP)를 참조하시오.

message listener container 및 receiver bean은 메시지를 수신하는 데 필요한 모든 것이다. 메시지를 보내려면 Rabbit template이 필요하다.

`queue()` 메소드는 AMQP 큐를 생성한다. `exchange()` 메소드는 topic 교환을 생성한다. `binding()` 메소드는 RabbitTemplate이 교환기에 발행(publish)할 때 발생하는 동작을 정의하여 둘을 서로 바인딩한다.

> Spring AMQP는 `Queue`, `TopicExchange` 그리고 `Binding`이 제대로 설정되기 위해 최상위 Spring bean으로 선언할 것을 요구한다.

이 경우, topic 교환을 사용하고 큐는 `foo.bar`로 시작하는 라우팅 키로 보내진 모든 메시지를 의미하는 `foo.bar.#`로 묶여있다. 이는 큐로 라우팅 된다.

## 테스트 메시지 보내기
테스트 메시지는 `CommandLineRunner`에 의해 보내진다. `CommandLineRunner`는 receiver의 latch를 대기하고 어플리케이션 컨텍스트를 닫는다:

`src/main/java/hello/Runner.java`

```java
package hello;

import java.util.concurrent.TimeUnit;

import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class Runner implements CommandLineRunner {

    private final RabbitTemplate rabbitTemplate;
    private final Receiver receiver;

    public Runner(Receiver receiver, RabbitTemplate rabbitTemplate) {
        this.receiver = receiver;
        this.rabbitTemplate = rabbitTemplate;
    }

    @Override
    public void run(String... args) throws Exception {
        System.out.println("Sending message...");
        rabbitTemplate.convertAndSend(Application.topicExchangeName, "foo.bar.baz", "Hello from RabbitMQ!");
        receiver.getLatch().await(10000, TimeUnit.MILLISECONDS);
    }

}
```

템플릿은 바인딩과 일치하는 라우팅 키 `foo.bar.baz`를 사용하여 메시지를 교환기로 라우팅한다.

runner는 테스트에서 mock으로 될 수 있으며, receiver를 따로따로 테스트할 수 있다.

## 어플리케이션 실행하기
`main()` 메소드는 Spring 어플리케이션 컨택스트를 생성하여 프로세스를 시작한다. 그러면 message listener container가 시작되어 메시지 수신을 시작한다. `Runner` bean이 자동으로 시작된다: 어플리케이션 컨텍스트에서 `RabbitTemplate`을 검색하고 "spring-boot" 큐에 "Hello from RabbitMQ!" 메시지를 보낸다. 마지막으로 Spring 어플리케이션 컨텍스트를 닫고 어플리케이션이 종료된다.

## 실행 가능한 JAR 만들기
Gradle 또는 Maven을 사용하여 커맨드 라인에서 어플리케이션을 실행할 수 있다. 또는 모든 필요한 의존성, 클래스 및 리소스 포함하는 단일 실행 가능한 JAR 파일을 빌드하고 실행할 수 있다. 따라서 개발 생명주기(life cycle), 다양한 환경에 걸쳐 어플리케이션으로 서비스를 쉽게 제공 및 배포할 수 있다.

Gradle을 사용하는 경우 `./gradlew bootRun`을 사용하여 어플리케이션을 실행할 수 있다. 또는 `./gradlew build`를 사용하여 JAR 파일을 작성할 수 있다. 그런 다음 JAR 파일을 실행할 수 있다:

```
java -jar build/libs/gs-messaging-rabbitmq-0.1.0.jar
```

Maven을 사용하는 경우, `./mvnw spring-boot:run`을 사용하여 어플리케이션을 실행할 수 있다. 또는 `./mvnw clean package`로 JAR 파일을 빌드할 수 있다. 그런다음 JAR 파일을 실행할 수 있다:

```
java -jar target/gs-messaging-rabbitmq-0.1.0.jar
```

> 위 절차는 실행 가능한 JAR를 생성한다. 고전의 [WAR 파일을 빌드](http://spring.io/guides/gs/convert-jar-to-war/)하도록 선택할 수도 있다.

다음 출력을 볼 수 있다:

```
Sending message...
Received <Hello from RabbitMQ!>
```

## 요약
축하합니다! Spring과 RabbitMQ로 간단한 게시(publish)와 구독(subscribe) 어플리케이션을 개발했다. [이곳](https://docs.spring.io/spring-amqp/reference/html/_introduction.html#quick-tour)에서 Spring과 RabbitMQ를 이용하여 여기보다 더 많은 것을 할 수 있다.

## 추가로 볼만한 것
다음 가이드 또한 도움이 될 수 있습니다:

* [Messaging with Redis](https://spring.io/guides/gs/messaging-redis/)
* [Messaging with JMS](https://spring.io/guides/gs/messaging-jms/)
* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)