## Spring Boot with Docker
This guide walks you through the process of building a [Docker](https://docker.com/) image for running a Spring Boot application.

## What you’ll build
[Docker](https://docker.com/) is a Linux container management toolkit with a "social" aspect, allowing users to publish container images and consume those published by others. A Docker image is a recipe for running a containerized process, and in this guide we will build one for a simple Spring boot application.

## What you’ll need
* About 15 minutes
* A favorite text editor or IDE
* [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) or later
* [Gradle 4+](http://www.gradle.org/downloads) or [Maven 3.2+](https://maven.apache.org/download.cgi)
* You can also import the code straight into your IDE:
  - [Spring Tool Suite (STS)](http://spring.io/guides/gs/sts)
  - [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea/)

If you are NOT using a Linux machine, you will need a virtualized server. By installing VirtualBox, other tools like the Mac’s boot2docker, can seamlessly manage it for you. Visit [VirtualBox’s download site](https://www.virtualbox.org/wiki/Downloads) and pick the version for your machine. Download and install. Don’t worry about actually running it.

You will also need [Docker](https://docker.com/), which only runs on 64-bit machines. See https://docs.docker.com/installation/#installation for details on setting Docker up for your machine. Before proceeding further, verify you can run `docker` commands from the shell. If you are using boot2docker you need to run that **first**.

## How to complete this guide
Like most Spring [Getting Started guides](http://spring.io/guides), you can start from scratch and complete each step, or you can bypass basic setup steps that are already familiar to you. Either way, you end up with working code.

To **start from scratch**, move on to [Build with Gradle](#build-with-gradle).

To **skip the basics**, do the following:

* [Download](https://github.com/spring-guides/gs-spring-boot-docker/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-spring-boot-docker.git`
* cd into `gs-spring-boot-docker/initial`
* Jump ahead to [Set up a Spring Boot app](#set-up-a-spring-boot-app).

**When you’re finished**, you can check your results against the code in `gs-spring-boot-docker/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-spring-boot-docker/blob/master/initial/build.gradle).

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
    baseName = 'gs-spring-boot-docker'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    testCompile("org.springframework.boot:spring-boot-starter-test")
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
    <artifactId>gs-spring-boot-docker</artifactId>
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
            <artifactId>spring-boot-starter-web</artifactId>
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

</project>
```

The [Spring Boot Maven plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin) provides many convenient features:

* It collects all the jars on the classpath and builds a single, runnable "über-jar", which makes it more convenient to execute and transport your service.
* It searches for the `public static void main()` method to flag as a runnable class.
* It provides a built-in dependency resolver that sets the version number to match [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml). You can override any version you wish, but it will default to Boot’s chosen set of versions.

## Build with your IDE
* Read how to import this guide straight into [Spring Tool Suite](http://spring.io/guides/gs/sts/).
* Read how to work with this guide in [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea).

## Set up a Spring Boot app
Now you can create a simple application.

`src/main/java/hello/Application.java`

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@RestController
public class Application {

    @RequestMapping("/")
    public String home() {
        return "Hello Docker World";
    }

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```

The class is flagged as a `@SpringBootApplication` and as a `@RestController`, meaning it’s ready for use by Spring MVC to handle web requests. `@RequestMapping` maps `/` to the `home()` method which just sends a 'Hello World' response. The `main()` method uses Spring Boot’s `SpringApplication.run()` method to launch an application.

Now we can run the application without the Docker container (i.e. in the host OS).

If you are using Gradle, execute:

```
./gradlew build && java -jar build/libs/gs-spring-boot-docker-0.1.0.jar
```

If you are using Maven, execute:

```
./mvnw package && java -jar target/gs-spring-boot-docker-0.1.0.jar
```

and go to [localhost:8080](http://localhost:8080/) to see your "Hello Docker World" message.

## Containerize It
Docker has a simple [Dockerfile](https://docs.docker.com/reference/builder/) file format that it uses to specify the "layers" of an image. So let’s go ahead and create a Dockerfile in our Spring Boot project:

`Dockerfile`

```dockerfile
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ARG JAR_FILE
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

This Dockerfile is very simple, but that’s all you need to run a Spring Boot app with no frills: just Java and a JAR file. The project JAR file is `ADDed` to the container as "app.jar" and then executed in the `ENTRYPOINT`.

> We added a `VOLUME` pointing to "/tmp" because that is where a Spring Boot application creates working directories for Tomcat by default. The effect is to create a temporary file on your host under "/var/lib/docker" and link it to the container under "/tmp". This step is optional for the simple app that we wrote here, but can be necessary for other Spring Boot applications if they need to actually write in the filesystem.

> To reduce [Tomcat startup time](https://wiki.apache.org/tomcat/HowTo/FasterStartUp#Entropy_Source) we added a system property pointing to "/dev/urandom" as a source of entropy. This is not necessary with more recent versions of Spring Boot, if you use the "standard" version of Tomcat (or any other web server).

To take advantage of the clean separation between dependencies and application resources in a Spring Boot fat jar file, we will use a slightly different implementation of the Dockerfile:

`Dockerfile`

```dockerfile
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ARG DEPENDENCY=target/dependency
COPY ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY ${DEPENDENCY}/META-INF /app/META-INF
COPY ${DEPENDENCY}/BOOT-INF/classes /app
ENTRYPOINT ["java","-cp","app:app/lib/*","hello.Application"]
```

This Dockerfile has a `DEPENDENCY` parameter pointing to a directory where we have unpacked the fat jar. If we get that right, it already contains a `BOOT-INF/lib` directory with the dependency jars in it, and a `BOOT-INF/classes` directory with the application classes in it. Notice that we are using the application’s own main class `hello.Application` (this is faster than using the indirection provided by the fat jar launcher).

> if you are using boot2docker you need to run it **first** before you do anything with the Docker command line or with the build tools (it runs a daemon process that handles the work for you in a virtual machine).

To build the image you can use some tooling for Maven or Gradle from the community (big thanks to [Palantir](https://github.com/palantir/gradle-docker) and [Spotify](https://github.com/spotify/dockerfile-maven) for making those tools available).

### Build a Docker Image with Maven
In the Maven `pom.xml` you should add a new plugin like this (see [the plugin documentation](https://github.com/spotify/dockerfile-maven) for more options): :

`pom.xml`

```xml
<properties>
   <docker.image.prefix>springio</docker.image.prefix>
</properties>
<build>
    <plugins>
        <plugin>
            <groupId>com.spotify</groupId>
            <artifactId>dockerfile-maven-plugin</artifactId>
            <version>1.3.6</version>
            <configuration>
                <repository>${docker.image.prefix}/${project.artifactId}</repository>
            </configuration>
        </plugin>
    </plugins>
</build>
```

The configuration specifies 1 mandatory thing: the repository with the image name, which will end up here as `springio/gs-spring-boot-docker`.

Some other properties are optional:
* The name of the directory where the fat jar is going to be unpacked, exposing the Maven configuration as a build argument for docker. It can be specified using the `<buildArgs/>` of the plugin configuration.
* The image tag, which ends up as "latest" if not specified. It can be set with the `<tag/>` element,

> Before proceeding with the following steps (which use Docker’s CLI tools), make sure Docker is properly running by typing `docker ps`. If you get an error message, something may not be set up correctly. Using a Mac? Add `$(boot2docker shellinit 2> /dev/null)` to the bottom of your `.bash_profile` (or similar env-setting configuration file) and refresh your shell to ensure proper environment variables are configured.

To ensure the jar is unpacked before the docker image is created we add some configuration for the dependency plugin:

`pom.xml`

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <executions>
        <execution>
            <id>unpack</id>
            <phase>package</phase>
            <goals>
                <goal>unpack</goal>
            </goals>
            <configuration>
                <artifactItems>
                    <artifactItem>
                        <groupId>${project.groupId}</groupId>
                        <artifactId>${project.artifactId}</artifactId>
                        <version>${project.version}</version>
                    </artifactItem>
                </artifactItems>
            </configuration>
        </execution>
    </executions>
</plugin>
```

You can build a tagged docker image using the command line like this:

```
$ ./mvnw install dockerfile:build
```

And you can push the image to dockerhub with `./mvnw dockerfile:push`.

> You don’t have to push your newly minted Docker image to actually run it. Moreover the "push" command will fail if you aren’t a member of the "springio" organization on Dockerhub. Change the build configuration and the command line to your own username instead of "springio" to make it actually work.

> you can make `dockerfile:push` automatically run in the install or deploy lifecycle phases by adding it to the plugin configuration.

`pom.xml`

```xml
<executions>
	<execution>
		<id>default</id>
		<phase>install</phase>
		<goals>
			<goal>build</goal>
			<goal>push</goal>
		</goals>
	</execution>
</executions>
```

### Build a Docker Image with Gradle
If you are using Gradle you need to add a new plugin like this:

`build.gradle`

```gradle
buildscript {
    ...
    dependencies {
        ...
        classpath('gradle.plugin.com.palantir.gradle.docker:gradle-docker:0.13.0')
    }
}

group = 'springio'

...
apply plugin: 'com.palantir.docker'

task unpack(type: Copy) {
    dependsOn bootJar
    from(zipTree(tasks.bootJar.outputs.files.singleFile))
    into("build/dependency")
}
docker {
    name "${project.group}/${bootJar.baseName}"
    copySpec.from(tasks.unpack.outputs).into("dependency")
    buildArgs(['DEPENDENCY': "dependency"])
}
```

The configuration specifies 4 things:
* a task to unpack the fat jar file
* the image name (or tag) is set up from the jar file properties, which will end up here as `springio/gs-spring-boot-docker`
* the location of the unpacked jarfile, which we could have hard coded in the `Dockerfile`
* a build argument for docker pointing to the jar file

You can build a tagged docker image and then push it to a remote repository with Gradle in one command:

```bash
$ ./gradlew build docker
```

### After the Push
A "docker push" will fail for you (unless you are part of the "springio" organization at Dockerhub), but if you change the configuration to match your own docker ID then it should succeed, and you will have a new tagged, deployed image.

> You do NOT have to register with docker or publish anything to run a docker image. You still have a locally tagged image, and you can run it like this:
> ```bash
> $ docker run -p 8080:8080 -t springio/gs-spring-boot-docker
> ....
> 2015-03-31 13:25:48.035  INFO 1 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
> 2015-03-31 13:25:48.037  INFO 1 --- [           main] hello.Application                        : Started Application in 5.613 seconds (JVM running for 7.293)
> ```

The application is then available on http://localhost:8080 (visit that and it says "Hello Docker World"). To make sure the whole process is really working, change the prefix from "springio" to something else (e.g. `${env.USER}`) and go through it again from the build through to the docker run.

> When using a Mac with boot2docker, you typically see things like this at startup:
> ```bash
> Docker client to the Docker daemon, please set:
>     export DOCKER_CERT_PATH=/Users/gturnquist/.boot2docker/certs/boot2docker-vm
>     export DOCKER_TLS_VERIFY=1
>     export DOCKER_HOST=tcp://192.168.59.103:2376
> ```
> To see the app, you must visit the IP address in DOCKER_HOST instead of localhost. In this case, http://192.168.59.103:8080, the public facing IP of the VM.

When it is running you can see in the list of containers, e.g:

```bash
$ docker ps
CONTAINER ID        IMAGE                                   COMMAND                  CREATED             STATUS              PORTS                    NAMES
81c723d22865        springio/gs-spring-boot-docker:latest   "java -Djava.secur..."   34 seconds ago      Up 33 seconds       0.0.0.0:8080->8080/tcp   goofy_brown
```

and to shut it down again you can `docker stop` with the container ID from the listing above (yours will be different):

```bash
$ docker stop 81c723d22865
81c723d22865
```

If you like you can also delete the container (it is persisted in your filesystem under `/var/lib/docker` somewhere) when you are finished with it:

```
$ docker rm 81c723d22865
```

### Using Spring Profiles
Running your freshly minted Docker image with Spring profiles is as easy as passing an environment variable to the Docker run command

```bash
$ docker run -e "SPRING_PROFILES_ACTIVE=prod" -p 8080:8080 -t springio/gs-spring-boot-docker
```

or

```bash
$ docker run -e "SPRING_PROFILES_ACTIVE=dev" -p 8080:8080 -t springio/gs-spring-boot-docker
```

### Debugging the application in a Docker container

To debug the application [JPDA Transport](https://docs.oracle.com/javase/8/docs/technotes/guides/jpda/conninv.html#Invocation) can be used. So we’ll treat the container like a remote server. To enable this feature pass a java agent settings in JAVA_OPTS variable and map agent’s port to localhost during a container run. With the [Docker for Mac](https://www.docker.com/products/docker#/mac) there is limitation due to that we can’t access container by IP without [black magic usage](https://github.com/docker/for-mac/issues/171).

```bash
$ docker run -e "JAVA_OPTS=-agentlib:jdwp=transport=dt_socket,address=5005,server=y,suspend=n" -p 8080:8080 -p 5005:5005 -t springio/gs-spring-boot-docker
```

## Summary
Congratulations! You’ve just created a Docker container for a Spring Boot app! Spring Boot apps run on port 8080 inside the container by default and we mapped that to the same port on the host using "-p" on the command line.

## See Also
The following guides may also be helpful:

* [Serving Web Content with Spring MVC](https://spring.io/guides/gs/serving-web-content/)
* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)