## Working a Getting Started guide with IntelliJ IDEA
This guide walks you through using IntelliJ IDEA to build one of the Getting Started guides.

## What you’ll build
You’ll pick a Spring guide and import it into IntelliJ IDEA. Then you can read the guide, work on the code, and run the project.

## What you’ll need
* About 15 minutes
* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/)
* [JDK 6](http://www.oracle.com/technetwork/java/javase/downloads/index.html) or later

## Installing IntelliJ IDEA
If you don’t have IntelliJ IDEA (Ultimate Edition) installed yet, visit the link up above. From there, you can download a copy for your platform. To install it simply unpack the downloaded archive.

When you’re done, go ahead and launch IntelliJ IDEA.

## Importing a Getting Started guide
To import an existing project you need some code, so clone or copy one of the Getting Started guides, e.g. the [REST Service](https://spring.io/guides/gs/rest-service/) guide:

```bash
$ git clone https://github.com/spring-guides/gs-rest-service.git
```

With IntelliJ IDEA up and running, click **Import Project** on the **Welcome Screen**, or **File | Open** on the main menu:

![](http://spring.io/guides/gs/intellij-idea/images/spring_guide_welcome_import.png)
In the pop-up dialog make sure to select either [Maven](http://spring.io/guides/gs/maven)'s **pom.xml** or [Gradle](http://spring.io/guides/gs/gradle's **build.gradle** file under the **complete** folder:

![](http://spring.io/guides/gs/intellij-idea/images/spring_guide_select_gradle_file.png)
IntelliJ IDEA will create a project with all the code from the guide ready to run.

## Creating a Project from Scratch
In case you’d like to start with an empty project and copy-and-paste your way through the guide, create a new **Maven** or **Gradle** project in the **Project Wizard**:

![](http://spring.io/guides/gs/intellij-idea/images/spring_guide_new_project.png)

## See Also
The following guide may also be helpful:

* [Working a Getting Started guide with STS](https://spring.io/guides/gs/sts/)