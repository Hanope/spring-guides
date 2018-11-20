## STS로 스프링 가이드 시작하기
This guide walks you through using Spring Tool Suite (STS) to build one of the Getting Started guides.
이 가이드는 시작 가이드 하나를 STS를 사용하여 만드는 과정을 안내합니다.

## 여러분이 만드실 것
You’ll pick a Spring guide and import it into Spring Tool Suite. Then you can read the guide, work on the code, and run the project.
여러분은 스프링 가이드를 선택하여 Spring Tool Suite 안으로 넣을 것입니다. 그러고 나서 가이드를 읽으며 예제 코드를 작업하고 여러분이 생성한 프로젝트를 실행할 수 있습니다.

## 여러분이 필요한 것
* 약 15분의 시간
* [Spring Tool Suite (STS)](https://spring.io/tools/sts/all)
* [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 이상

## STS 설치하기
If you don’t have STS installed yet, visit the link up above. From there, you can download a copy for your platform. To install it simply unpack the downloaded archive.
아직 STS를 설치하지 않았다면, [링크](http://spring.io/tools)를 방문하십시오. 여기서 여러분의 환경에 맞게 다운로드하셔서 압축만 푸시면 설치가 완료됩니다.

When you’re done, go ahead and launch STS.
모든 과정을 마치셨으면 STS를 실행해봅시다.

## 스프링 시작가이드를 불러오기
With STS up and running, open the **Import Spring Getting Started Content** wizard from the **File** menu.
STS가 켜지면 상단의 **File** 메뉴를 클릭하시고 **Import Spring Getting Started Content**를 선택합니다.

![](http://spring.io/guides/gs/sts/images/1_open_wizard.png)
A pop-up wizard will offer you the chance to search and pick any of the published guides from the Spring website. 
팝업으로 뜬 마법사가 여러분에게 스프링 웹사이트에 나와있는 가이드 중 하나를 선택할 수 있도록 도와줄 것입니다.
You can either skim the list, or enter search words to instantly filter the options.]
목록을 건너뛰거나 검색어를 입력해서 옵션을 바꿀 수 있습니다.

> The criteria is applied to both the title and the description when offering instant search results. Wildcards are supported.
> 검색어는 제목과 설명 둘 다 반영해서 검색됩니다. 그리고 와일드카드 문자를 사용할 수 있습니다.

You can pick either [Maven](http://spring.io/guides/gs/maven) or [Gradle](http://spring.io/guides/gs/gradle) as the build system to use.
여러분은 [Maven](http://spring.io/guides/gs/maven) 이나 [Gradle](http://spring.io/guides/gs/gradle)을 사용해서 프로젝트를 만들 수 있습니다.

You can also decide whether to grab the **initial** code set, **complete** code set, or both. 
그리고 여러분은 또한 **initial** 코드와 **complete** 중 선택해서 가져오거나 둘 다 가져올 수 있습니다. 
For most projects, the **initial** code set is an empty project, making it possible for you to copy-and-paste your way through a guide. 
대부분의 프로젝트에서는 **initial**코드는 비어있는 프로젝트입니만 가이드에 따라서 복사 및 붙여넣기가 가능하게 되어있습니다.
The **complete** code set is all the code from the guide already entered. 
**complete**코드의 경우는 모든 코드가 가이드의 진행 순서에 따라서 미리 입력되어 있습니다.
If you grab both, you can compare your work against the guide’s and see the differences.
만약 둘 다 가져온다면 여러분이 작성한 코드와 완성된 코드를 비교할 수 있을 것입니다.

Finally, you can have STS open a browser tab to the guide on the website. 
모두 마치면 STS는 여러분이 선택한 가이드가 표시된 웹사이트를 열 것입니다.
This will let you work through a guide without having to leave STS.
STS 안에서도 가이드를 보며 여러분은 작업을 할 수 있습니다.

For the purpose of this guide, enter **rest** into the instant search box. Then pick [Consuming Rest](http://spring.io/guides/gs/consuming-rest). Pick **Maven** for building, and **initial** and **complete** code sets. Also opt to open the web page as shown below:
이 가이드에서는 검색창에 **rest**를 입력할 것이며 [Consuming Rest](http://spring.io/guides/gs/consuming-rest)를 선택할 것입니다. 그리고 빌드 툴로 **Maven**을 선택할 것이며 **initial**과 **complete** 코드 셋을 선택할 것입니다.
또한 웹페이지를 열도록 하는 옵션을 선택했습니다:

![](http://spring.io/guides/gs/sts/images/3_wizard.png)
STS will create two new projects in your workspace, import the [Consuming Rest](http://spring.io/guides/gs/consuming-rest) code base (both initial and complete), and open a browser tab inside STS as shown below:
STS는 [Consuming Rest](http://spring.io/guides/gs/consuming-rest)의 initial과 complete의 코드가 들어있는 두 개의 새로운 프로젝트를 여러분이 선택한 폴더 안에 만듭니다. 그리고 STS의 내장 브라우저가 열립니다:

![](http://spring.io/guides/gs/sts/images/4_after-import.png)
From here, you can walk through the guide and navigate to the code files.
여러분은 이 브라우저에서 가이드를 보거나 코드 파일을 찾아갈 수 있습니다.

## 마무리
Congratulations! You have setup Spring Tool Suite, imported the Consuming Rest getting started guide, and opened a browser tab to walk through it.
축하합니다! 여러분은 STS를 설치하고 스프링 시작 가이드 중 Consuming Rest 프로젝트를 만들었으며 해당 가이드의 브라우저를 열었습니다.

## 참고
The following guides may also be helpful:
이 가이드들을 따라 하면 도움이 될 것입니다:
* [IntelliJ IDEA로 스프링 가이드 시작하기](https://spring.io/guides/gs/intellij-idea/)