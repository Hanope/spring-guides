## Accessing Vault
This guide walks you through the process of using [Spring Vault](https://projects.spring.io/spring-vault/) to build an application that loads secrets from [HashiCorp Vault](https://www.vaultproject.io/), a secrets management tool.

## What you’ll build
You will load secrets stored in Vault and use the transit encryption backend.

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

* [Download](https://github.com/spring-guides/gs-accessing-vault/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-accessing-vault.git`
* cd into `gs-accessing-vault/initial`
* Jump ahead to [Install and launch HashiCorp Vault](#install-and-launch-hashicorp-vault).

**When you’re finished**, you can check your results against the code in `gs-accessing-vault/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-accessing-vault/blob/master/initial/build.gradle).

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

The [Spring Boot Maven plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin) provides many convenient features:

* It collects all the jars on the classpath and builds a single, runnable "über-jar", which makes it more convenient to execute and transport your service.
* It searches for the `public static void main()` method to flag as a runnable class.
* It provides a built-in dependency resolver that sets the version number to match [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml). You can override any version you wish, but it will default to Boot’s chosen set of versions.

## Build with your IDE
* Read how to import this guide straight into [Spring Tool Suite](http://spring.io/guides/gs/sts/).
* Read how to work with this guide in [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea).

## Install and launch HashiCorp Vault
With your project set up, you can install and launch HashiCorp Vault.

If you are using a Mac with homebrew, this is as simple as:

```bash
$ brew install vault
```

Alternatively, download Vault for your operating system from https://www.vaultproject.io/downloads.html:

```bash
$ https://releases.hashicorp.com/vault/0.8.3/vault_0.8.3_darwin_amd64.zip
$ unzip vault_0.8.3_darwin_amd64.zip
```

For other systems with package management, such as Redhat, Ubuntu, Debian, CentOS, and Windows, see instructions at https://www.vaultproject.io/docs/install/index.html.

After you install Vault, launch it in a console window. This command also starts up a server process.

```
$ vault server --dev --dev-root-token-id="00000000-0000-0000-0000-000000000000"
```

You should see the following as one of the last output lines:

```
[INFO ] core: post-unseal setup complete
```

> The command above starts Vault in development mode using in-memory storage without transport encryption. This is fine for evaluating Vault locally. Make sure to use proper SSL certificates and a reliable storage backend for production use. Consult Vault’s [Production Hardening guide](https://www.vaultproject.io/guides/production.html) for further details.

## Store secrets in Vault
Vault is a secrets management system allowing you to store sensitive data which is encrypted at rest. It’s ideal to store sensitive configuration details such as passwords, encryption keys, API keys.

Launch another console window to store application configuration in Vault using the Vault command line.

First, you need to set two environment variables to point the Vault CLI to the Vault endpoint and provide an authentication token.

```bash
$ export export VAULT_TOKEN="00000000-0000-0000-0000-000000000000"
$ export VAULT_ADDR="http://127.0.0.1:8200"
```

Now you can store a configuration key-value pairs inside Vault:

```bash
$ vault write secret/github github.oauth2.key=foobar
```

### Configure your application

Here you configure your application with `bootstrap.properties`. Spring Cloud Vault is configured with the bootstrap context.

`src/main/resources/bootstrap.properties`

```properties
spring.cloud.vault.token=00000000-0000-0000-0000-000000000000
spring.cloud.vault.scheme=http
```

## Create an Application class
Here you create an Application class with all the components.

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

Spring Cloud Vault uses `VaultOperations` to interact with Vault. Properties from Vault get mapped to `MyConfiguration` for type-safe access. `@EnableConfigurationProperties(MyConfiguration.class)` enables configuration property mapping and registers a `MyConfiguration` bean.

`Application` includes a `main()` method that autowires an instance of `MyConfiguration`.

## Build an executable JAR
You can run the application from the command line with Gradle or Maven. Or you can build a single executable JAR file that contains all the necessary dependencies, classes, and resources, and run that. This makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.

If you are using Gradle, you can run the application using `./gradlew bootRun`. Or you can build the JAR file using `./gradlew build`. Then you can run the JAR file:

```
java -jar build/libs/gs-accessing-vault-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-accessing-vault-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/) instead.

As our `Application` implements `CommandLineRunner`, the `run` method is invoked automatically when boot starts. You should see something like this:

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

> Vault’s secret backend compares well to a document store that uses URIs to identify documents. Documents are JSON-based that allows convenient object mapping of Vault data.

## Summary
Congratulations! You set up a Vault server and wrote a simple application that uses Spring Vault to read secrets and encrypt data with a strong cipher — all without the headache of implementing key management, a cipher mode, and padding.