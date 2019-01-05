## Vault 사용해보기
이 가이드는 비밀 관리 툴인 [HashiCorp Vault](https://www.vaultproject.io/)에서 데이터를 불러오는 [Spring Vault](https://projects.spring.io/spring-vault/)를 사용하여 어플리케이션을 만드는 과정으로 안내합니다.

## 무엇을 만들게 되는가?
여러분은 transit encryption backend를 사용하여 Valut에 저장된 비밀정보를 불러올 것입니다.

## 무엇이 필요한가?
* 약 15분의 시간
* 좋아하는 텍스트 에디터나 IDE
* [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 이상
* [Gradle 4+](http://www.gradle.org/downloads) 또는 [Maven 3.2+](https://maven.apache.org/download.cgi)
* 여러분이 코드를 IDE에 직접 가져오려면 다음을 참고하세요:
  - [Spring Tool Suite (STS)](http://spring.io/guides/gs/sts)
  - [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea/)

## 이 가이드를 완료하는 방법
대부분의 [스프링 시작 가이드](http://spring.io/guides)처럼 처음부터 하나씩 완성해 가거나 익숙한 시작 부분은 건너뛰어도 됩니다. 어느 방법을 선택하시더라도 작동되는 코드를 완성시킬 수 있습니다.

**처음부터 작업하실 분**은 [Gradle을 사용한 빌드](#Gradle을-사용한-빌드)로 가세요.

**시작부분을 건너뛰실 분**은 아래방법을 따라하세요:

* [다운로드](https://github.com/spring-guides/gs-accessing-vault/archive/master.zip) 하시고 압축파일을 풀어주시거나 [GIT](http://spring.io/understanding/Git)을 사용해 복제해주세요 (명령어): `git clone https://github.com/spring-guides/gs-accessing-vault.git`
* 소스의 압축을 해제한 폴더에서 `gs-accessing-vault/initial`로 이동합니다.(cd)
* [HashiCorp Vault를 설치하고 실행하기](#HashiCorp-Vault를-설치하고-실행하기)로 이동합니다.

**다 끝나고나서**, `gs-accessing-vault/complete`의 코드와 비교해서 맞는지 확인해보세요.

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
[초기 Gradle build file](https://github.com/spring-guides/gs-accessing-vault/blob/master/initial/build.gradle)은 아래와 같습니다.

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
    baseName = 'gs-accessing-vault'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

ext {
    springCloudVersion = 'Finchley.SR1'
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile('org.springframework.cloud:spring-cloud-starter-vault-config')
    testCompile("org.springframework.boot:spring-boot-starter-test")
}

dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
    }
}

repositories {
    maven {
        url 'https://repo.spring.io/libs-milestone'
    }
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
    <artifactId>gs-accessing-vault</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.5.RELEASE</version>
    </parent>

    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Finchley.SR1</spring-cloud.version>
    </properties>

    <dependencies>

        <!-- Vault Starter -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-vault-config</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

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
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/libs-milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>


</project>
```

이 [Spring Boot Maven plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin)은 많고 편리한 기능을 제공합니다.:

* 이 플러그인은 클래스패스 위의 jar를 하나의 실행가능한 jar로 모아서 여러분의 서비스를 실행하고 전송하는데 편리하게 만들어줍니다.
* 이 플러그인은 `public static void main()`메소드를 찾아 실행 가능한 클래스로 표시를 해줍니다.
* 이 플러그인은 [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml)에 맞는 내장된 의존성 해석자를 제공합니다.

## 여러분의 IDE를 사용한 빌드 하기
* 이 가이드를 STS에 직접 포함하는 방법을 알고싶으면 [Spring Tool Suite](http://spring.io/guides/gs/sts/)을 읽어주세요.
* 이 가이드를 Intellij IDEA에서 작동시키는 법을 알고 싶으면 [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea)을 읽어주세요.

## HashiCorp Vault를 설치하고 실행하기
프로젝트 설정과 함께 여러분은 HashiCorp Vault를 설치하고 실행할 수 있습니다.

homebrew가 설치된 맥을 쓰신다면 간단하게 진행할 수 있습니다:

```bash
$ brew install vault
```

또는, 여러분의 시스템에 맞게 https://www.vaultproject.io/downloads.html에서 Vault를 다운로드할 수 있습니다:

```bash
$ https://releases.hashicorp.com/vault/0.8.3/vault_0.8.3_darwin_amd64.zip
$ unzip vault_0.8.3_darwin_amd64.zip
```

레드햇, 우분투, 데비안, CENT OS, 윈도우등의 패키지 매니저로 설치하시려면 https://www.vaultproject.io/docs/install/index.html를 보고 설치해 주세요.

Vault를 설치하신 후, 콘솔 창에서 실행할 수 있습니다. 그리고 아래의 커맨드를 입력하셔서 서버를 시작해 주세요.

```
$ vault server --dev --dev-root-token-id="00000000-0000-0000-0000-000000000000"
```

여러분은 마지막 한 줄을 다음과 같이 볼 수 있을 겁니다.

```
[INFO ] core: post-unseal setup complete
```

> 이 커맨드는 Vault를 개발 모드로 시작하며 암호화하지 않고 저장소를 메모리 안에 만들어서 사용합니다. 또한 이 커맨드로 Vault를 내부에서 사용하는 것으로는 괜찮습니다. 실제 운영 용도로는 신뢰할 수 있는 저장소와 적절한 SSL 인증서를 사용해야 합니다. Vault’s [Production Hardening guide](https://www.vaultproject.io/guides/production.html)에서 정보를 더 얻을 수 있습니다.

## Vault안에 비밀정보 저장하기
Vault는 숨겨진 상태로 암호화된 중요한 데이터를 저장할 수 있는 비밀 관리 시스템입니다. 암호, 암호화 키, API 키와 같은 중요한 구성 정보를 저장하는 것이 이상적입니다.

다른 콘솔 창을 켜서 커맨드 라인으로 Vault 안에 어플리케이션 구성 정보를 저장해 보겠습니다.

첫 번째로, 여러분은 Vault CLI에서 인증된 토큰을 제공하기 위한 두 개의 환경 변수를 설정해야 합니다.

```bash
$ export export VAULT_TOKEN="00000000-0000-0000-0000-000000000000"
$ export VAULT_ADDR="http://127.0.0.1:8200"
```

이제 키-값으로 구성된 정보를 Vault 안으로 저장해 보겠습니다:

```bash
$ vault write secret/github github.oauth2.key=foobar
```

### 여러분의 어플리케이션을 환경설정 하기

여러분은 여기 `bootstrap.properties`에서 환경설정을 할 수 있습니다. Spring Cloud Vault는 부트스트랩 컨텍스트에서 환경설정이 됩니다.

`src/main/resources/bootstrap.properties`

```properties
spring.cloud.vault.token=00000000-0000-0000-0000-000000000000
spring.cloud.vault.scheme=http
```

## 어플리케이션 클래스 만들기
모든 구성요소를 담은 Application 클래스를 만듭니다.

`src/main/java/hello/Application.java`

```java
package hello;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.vault.core.VaultSysOperations;
import org.springframework.vault.core.VaultTemplate;
import org.springframework.vault.core.VaultTransitOperations;
import org.springframework.vault.support.VaultMount;
import org.springframework.vault.support.VaultResponse;

@SpringBootApplication
public class Application implements CommandLineRunner {

    @Autowired
    private VaultTemplate vaultTemplate;

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Override
    public void run(String... strings) throws Exception {

        // You usually would not print a secret to stdout
		VaultResponse response = vaultTemplate.read("secret/github");
		System.out.println("Value of github.oauth2.key");
        System.out.println("-------------------------------");
        System.out.println(response.getData().get("github.oauth2.key"));
        System.out.println("-------------------------------");
        System.out.println();

        // Let's encrypt some data using the Transit backend.
        VaultTransitOperations transitOperations = vaultTemplate.opsForTransit();

        // We need to setup transit first (assuming you didn't set up it yet).
        VaultSysOperations sysOperations = vaultTemplate.opsForSys();

        if (!sysOperations.getMounts().containsKey("transit/")) {

            sysOperations.mount("transit", VaultMount.create("transit"));

            transitOperations.createKey("foo-key");
        }

        // Encrypt a plain-text value
        String ciphertext = transitOperations.encrypt("foo-key", "Secure message");

        System.out.println("Encrypted value");
        System.out.println("-------------------------------");
        System.out.println(ciphertext);
        System.out.println("-------------------------------");
        System.out.println();

        // Decrypt

        String plaintext = transitOperations.decrypt("foo-key", ciphertext);

        System.out.println("Decrypted value");
        System.out.println("-------------------------------");
        System.out.println(plaintext);
        System.out.println("-------------------------------");
        System.out.println();
    }
}
```

Spring Cloud Vault는 `VaultOperations`를 통해 Vault와 상호작용합니다. Vault의 속성은 형식에 안전한(type-safe) 액세스를 위해 `MyConfiguration`에 매핑됩니다. `@EnableConfigurationProperties(MyConfiguration.class)`는 `MyConfiguration`빈을 등록하고 속성들을 매핑할 수 있게 해줍니다.

`Application`은 `MyConfiguration`인스턴스를 자동으로 생성할 수 있는(autowire) `main()` 메소드를 포함하고 있습니다.

## 실행가능한 JAR로 빌드하기
여러분은 Gradle이나 Maven을 이용한 커맨드 라인으로 이 어플리케이션을 실행할 수 있습니다. 또는 모든 필요한 의존성, 클래스, 자원 등을 포함한 하나의 실행 가능한 JAR로 빌드 할 수도 있습니다. 그래서 다양한 환경에서 개발 주기 전반에 걸쳐 버전을 올리고 서비스를 배포하는 것이 쉬워집니다.

Gradle을 사용할 경우 `./gradlew bootRun` 명령어로 실행할 수 있습니다. 또는 `./gradlew build` 명령어로 JAR 파일을 만들고 다음과 같이 JAR 파일을 실행할 수 있습니다.

```
java -jar build/libs/gs-accessing-vault-0.1.0.jar
```

Maven을 사용할 경우 `./mvnw spring-boot:run` 명령어로 실행할 수 있습니다. 또는 `./mvnw clean package` 명령어로 JAR 파일을 만들고 다음과 같이 JAR 파일을 실행할 수 있습니다.

```
java -jar target/gs-accessing-vault-0.1.0.jar
```

> 이 과정은 실행가능한 JAR를 만듭니다. 물론 [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/)도 옵션으로 선택하실 수 있습니다.

우리가 만든 `Application`은 `CommandLineRunner`를 구현합니다, 그래서 `run` 메소드가 시작 시 자동으로 호출됩니다. 여러분은 다음과 같은 화면을 볼 수 있습니다:

```
Value of github.oauth2.key
-------------------------------
foobar
-------------------------------

Encrypted value
-------------------------------
vault:v1:2wgVE2PXiR9o55xbyur5KHJl8IwyGDkDU4l1SZScUq6BuqZYgTopwvc4
-------------------------------

Decrypted value
-------------------------------
Secure message
-------------------------------
```

> Vault의 secret backend는 고유 문서를 URI를 사용해서 비교합니다. 문서는 Vault 데이터로 편하게 변환할 수 있도록 JSON 기반으로 이루어져 있습니다.

## 요약
축하합니다! 여러분은 Vault 서버를 설치하고 강력하게 암호화된 데이터를 스프링 Vault를 사용하여 읽는 간단한 어플리케이션을 만들어 보았습니다.