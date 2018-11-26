## IntelliJ IDEA로 스프링 가이드 시작하기
이 가이드는 시작 가이드 하나를 IntelliJ IDEA를 사용하여 만드는 과정을 안내합니다.

## 무엇을 만들게 되는가?
여러분은 스프링 가이드를 선택하여 IntelliJ IDEA 안으로 넣을 것입니다. 그러고 나서 가이드를 읽으며 예제 코드를 작업하고 여러분이 생성한 프로젝트를 실행할 수 있습니다.

## 무엇이 필요한가?
* 약 15분의 시간
* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/)
* [JDK 6](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 이상

## IntelliJ IDEA 설치하기
여러분이 아직 IntelliJ IDEA (Ultimate Edition)를 설치하지 않으셨다면, [링크](https://www.jetbrains.com/idea/download/)를 방문해 주십시오. 그리고 여기서 여러분의 플랫폼에 맞게 다운로드 받으세요. 그리고 압축을 해제해주세요.
모두 다 완료되면 IntelliJ IDEA를 실행해주세요.

## 스프링 시작가이드를 불러오기
스프링 가이드 중 여러분이 필요한 프로젝트 하나만 선택해서 불러오겠습니다. 예를들어 [REST Service](https://spring.io/guides/gs/rest-service/) 가이드의 경우는 :

```bash
$ git clone https://github.com/spring-guides/gs-rest-service.git
```

IntelliJ IDEA가 실행되면 **시작 화면**에서 **Import Project**를 선택하시거나 메인 메뉴에서 **File | Open**를 클릭해주세요.

![](http://spring.io/guides/gs/intellij-idea/images/spring_guide_welcome_import.png)

팝업창이 뜨면 **complete**폴더 아래에서 [Maven](http://spring.io/guides/gs/maven)의 **pom.xml**이나 [Gradle](http://spring.io/guides/gs/gradle)의 **build.gradle**중 하나를 클릭해주세요:

![](http://spring.io/guides/gs/intellij-idea/images/spring_guide_select_gradle_file.png)

IntelliJ IDEA는 선택한 가이드를 실행 가능한 상태로 생성할 것입니다.

## 처음부터 프로젝트 생성하기
여러분이 새롭게 빈 프로젝트를 생성해서 가이드를 따라하고 싶은 경우, **Project Wizard**를 선택하셔서 **Maven** 이나 **Gradle** 프로젝트를 만들 수 있습니다.

![](http://spring.io/guides/gs/intellij-idea/images/spring_guide_new_project.png)

## 추가로 볼만한 것
이 가이드들을 따라 하면 도움이 될 것입니다:

* [STS로 스프링 가이드 시작하기](https://spring.io/guides/gs/sts/)