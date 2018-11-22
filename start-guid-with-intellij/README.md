## IntelliJ IDEA로 스프링 가이드 시작하기
This guide walks you through using IntelliJ IDEA to build one of the Getting Started guides.
이 가이드는 시작 가이드 하나를 IntelliJ IDEA를 사용하여 만드는 과정을 안내합니다.

## 여러분이 만드실 것
You’ll pick a Spring guide and import it into IntelliJ IDEA. Then you can read the guide, work on the code, and run the project.
여러분은 스프링 가이드를 선택하여 IntelliJ IDEA 안으로 넣을 것입니다. 그러고 나서 가이드를 읽으며 예제 코드를 작업하고 여러분이 생성한 프로젝트를 실행할 수 있습니다.

## 여러분이 필요한 것
* 약 15분의 시간
* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/)
* [JDK 6](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 이상

## IntelliJ IDEA 설치하기
If you don’t have IntelliJ IDEA (Ultimate Edition) installed yet, visit the link up above. From there, you can download a copy for your platform. To install it simply unpack the downloaded archive.
여러분이 아직 IntelliJ IDEA (Ultimate Edition)를 설치하지 않으셨다면, [링크](https://www.jetbrains.com/idea/download/)를 방문해 주십시오. 그리고 여기서 여러분의 플랫폼에 맞게 다운로드 받으세요. 그리고 압축을 해제해주세요.
When you’re done, go ahead and launch IntelliJ IDEA.
모두 다 완료되면 IntelliJ IDEA를 실행해주세요.

## 스프링 시작가이드를 불러오기
To import an existing project you need some code, so clone or copy one of the Getting Started guides, e.g. the [REST Service](https://spring.io/guides/gs/rest-service/) guide:
스프링 가이드 중 여러분이 필요한 코드 하나만 선택해서 현재 존재하는 프로젝트로 불러오겠습니다. 예를들어 [REST Service](https://spring.io/guides/gs/rest-service/) 가이드의 경우는 :

```bash
$ git clone https://github.com/spring-guides/gs-rest-service.git
```

With IntelliJ IDEA up and running, click **Import Project** on the **Welcome Screen**, or **File | Open** on the main menu:
IntelliJ IDEA가 실행되면 **시작 화면**에서 **Import Project**를 선택하시거나 메인 메뉴에서 **File | Open**를 클릭해주세요.

![](http://spring.io/guides/gs/intellij-idea/images/spring_guide_welcome_import.png)
In the pop-up dialog make sure to select either [Maven](http://spring.io/guides/gs/maven)'s **pom.xml** or [Gradle](http://spring.io/guides/gs/gradle's **build.gradle** file under the **complete** folder:
팝업창이 뜨면 **complete**폴더 아래에서 [Maven](http://spring.io/guides/gs/maven)의 **pom.xml**이나 [Gradle](http://spring.io/guides/gs/gradle)의 **build.gradle**중 하나를 클릭해주세요.

![](http://spring.io/guides/gs/intellij-idea/images/spring_guide_select_gradle_file.png)
IntelliJ IDEA will create a project with all the code from the guide ready to run.
IntelliJ IDEA는 선택한 가이드를 실행 가능한 상태로 생성할 것입니다.

## 처음부터 프로젝트 생성하기
In case you’d like to start with an empty project and copy-and-paste your way through the guide, create a new **Maven** or **Gradle** project in the **Project Wizard**:
여러분이 새롭게 빈 프로젝트를 생성해서 가이드를 따라하고 싶은 경우, **Project Wizard**를 선택하셔서 **Maven** 이나 **Gradle** 프로젝트를 만들 수 있습니다.
![](http://spring.io/guides/gs/intellij-idea/images/spring_guide_new_project.png)

## 참고
이 가이드들을 따라 하면 도움이 될 것입니다:

* [STS로 스프링 가이드 시작하기](https://spring.io/guides/gs/sts/)