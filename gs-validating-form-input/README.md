## Validating Form Input
This guide walks you through the process of configuring a web application form to support validation.

## What you’ll build
You’ll build a simple Spring MVC application that take user input and checks the input using standard validation annotations. You’ll also see how to display the error message on the screen so the user can re-enter a valid input.

## What you’ll need
* About 15 minutes
* A favorite text editor or IDE
* [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) or later
* [Gradle 4+](http://www.gradle.org/downloads) or [Maven 3.2+](https://maven.apache.org/download.cgi)
* You can also import the code straight into your IDE:
  - [Spring Tool Suite (STS)](http://spring.io/guides/gs/sts)
  - [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea/)
    - RabbitMQ server (installation instructions below)

## How to complete this guide
Like most Spring [Getting Started guides](http://spring.io/guides), you can start from scratch and complete each step, or you can bypass basic setup steps that are already familiar to you. Either way, you end up with working code.

To **start from scratch**, move on to [Build with Gradle](#build-with-gradle).

To **skip the basics**, do the following:

* [Download](https://github.com/spring-guides/gs-validating-form-input/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-validating-form-input.git`
* cd into `gs-validating-form-input/initial`
* Jump ahead to [Create a PersonForm object](#create-a-personform-object).

**When you’re finished**, you can check your results against the code in `gs-validating-form-input/complete`.

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
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-validating-form-input/blob/master/initial/build.gradle).

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

The [Spring Boot Maven plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin) provides many convenient features:

* It collects all the jars on the classpath and builds a single, runnable "über-jar", which makes it more convenient to execute and transport your service.
* It searches for the `public static void main()` method to flag as a runnable class.
* It provides a built-in dependency resolver that sets the version number to match [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml). You can override any version you wish, but it will default to Boot’s chosen set of versions.

## Build with your IDE
* Read how to import this guide straight into [Spring Tool Suite](http://spring.io/guides/gs/sts/).
* Read how to work with this guide in [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea).

## Create a PersonForm object
The application involves validating a user’s name and age, so first you need to create a class to back the form to create a person.

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

The `PersonForm` class has two attributes: `name` and `age`. It is flagged with several standard validation annotations:

* `@Size(min=2, max=30)` will only allow names between 2 and 30 characters long
* `@NotNull` won’t allow a null value, which is what Spring MVC generates if the entry is empty
* `@Min(18)` won’t allow if the age is less than 18

In addition to that, you can also see getters/setters for `name` and `age` as well as a convenient `toString()` method.

## Create a web controller
Now that you have defined a form backing object, it’s time to create a simple web controller.

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

This controller has a GET and a POST method, both mapped to `/`.

The `showForm` method returns the `form` template. It includes a `PersonForm` in its method signature so the template can associate form attributes with a `PersonForm`.

The `checkPersonFormInfo` method accepts two arguments:

* A `personForm` object marked up with `@Valid` to gather the attributes filled out in the form you’re about to build.
* A `bindingResult` object so you can test for and retrieve validation errors.

You can retrieve all the attributes from the form bound to the `PersonForm` object. In the code, you test for errors, and if so, send the user back to the original `form` template. In that situation, all the error attributes are displayed.

If all of the person’s attribute are valid, then it redirects the browser to the final `results` template.

## Build an HTML front end
Now you build the "main" page.

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

The page contains a simple form with each field in a separate slot of a table. The form is geared to post towards `/`. It is marked as being backed up by the `personForm` object that you saw in the GET method in the web controller. This is known as a **bean-backed form**. There are two fields in the `PersonForm` bean, and you can see them tagged `th:field="{name}`" **and** `th:field="{age}"`. Next to each field is a secondary element used to show any validation errors.

Finally, you have a button to submit. In general, if the user enters a name or age that violates the `@Valid` constraints, it will bounce back to this page with the error message on display. If a valid name and age is entered, the user is routed to the next web page.

`src/main/resources/templates/results.html`

```html
<html>
	<body>
		Congratulations! You are old enough to sign up for this site.
	</body>
</html>
```

> In this simple example, these web pages don’t have any sophisticated CSS or JavaScript. But for any production web site, it’s valuable to learn how to style your web pages.

## Create an Application class
For this application, you are using the template language of [Thymeleaf](http://www.thymeleaf.org/doc/tutorials/3.0/thymeleafspring.html). This application needs more than raw HTML.

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

To activate Spring MVC, you would normally add `@EnableWebMvc` to the Application class. But Spring Boot’s `@SpringBootApplication` already adds this annotation when it detects **spring-webmvc** on your classpath. This same annotation allows it to find the annotated `@Controller` class and its methods.

The Thymeleaf configuration is also taken care of by `@SpringBootApplication`: by default templates are located in the classpath under `templates/` and are resolved as views by stripping the '.html' suffix off the file name. Thymeleaf settings can be changed and overridden in a variety of ways depending on what you need to achieve, but the details are not relevant to this guide.

## Build an executable JAR
You can run the application from the command line with Gradle or Maven. Or you can build a single executable JAR file that contains all the necessary dependencies, classes, and resources, and run that. This makes it easy to ship, version, and deploy the service as an application throughout the development lifecycle, across different environments, and so forth.

If you are using Gradle, you can run the application using `./gradlew bootRun`. Or you can build the JAR file using `./gradlew build`. Then you can run the JAR file:

```
java -jar build/libs/gs-validating-form-input-0.1.0.jar
```

If you are using Maven, you can run the application using `./mvnw spring-boot:run`. Or you can build the JAR file with `./mvnw clean package`. Then you can run the JAR file:

```
java -jar target/gs-validating-form-input-0.1.0.jar
```

> The procedure above will create a runnable JAR. You can also opt to [build a classic WAR file](http://spring.io/guides/gs/convert-jar-to-war/) instead.

The application should be up and running within a few seconds.

If you visit http://localhost:8080/, you should see something like this:

![](http://spring.io/guides/gs/validating-form-input/images/valid-01.png)

What happens if you enter **A** for the name and **15** for your age and click on **Submit**?

![](http://spring.io/guides/gs/validating-form-input/images/valid-02.png)

![](http://spring.io/guides/gs/validating-form-input/images/valid-03.png)
Here you can see that because it violated the constraints in the `PersonForm` class, you get bounced back to the "main" page. If you click on Submit with nothing in the entry box, you get a different error.

![](http://spring.io/guides/gs/validating-form-input/images/valid-04.png)
If you enter a valid name and age, you end up on the `results` page!

![](http://spring.io/guides/gs/validating-form-input/images/valid-05.png)

## Summary
Congratulations! You have coded a simple web application with validation built into a domain object. This way you can ensure the data meets certain criteria and that the user inputs it correctly.

## See Also
The following guides may also be helpful:

* [Serving Web Content with Spring MVC](https://spring.io/guides/gs/serving-web-content/)
* [Handling Form Submission](https://spring.io/guides/gs/handling-form-submission/)
* [Securing a Web Application](https://spring.io/guides/gs/securing-web/)
* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)