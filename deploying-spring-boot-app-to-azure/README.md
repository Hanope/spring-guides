## Spring Boot 앱을 Azure에 배포 해 보기
이 글은 어플리케이션을 Azure에 배포하는 방법을 보여줍니다.

## 무엇을 만들게 되는가?
GitHub에서 하나의 샘플 Spring Boot 어플리케이션을 복제한 후, Maven을 이용해서 Azure로 배포하게 됩니다.

## 무엇이 필요한가?
이 글의 단계들을 따라하기 위해선 다음의 전제조건들이 요구됩니다:

* Azure 서비스 가입. 아직 Azure 서비스에 가입하지 않았다면, [무료 Azure 계정](https://azure.microsoft.com/pricing/free-trial/) 에서 가입을 하거나 [MSDN 서비스 가입자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) 를 통한 활성화.
* [Azure 터미널 명령 인터페이스(CLI)](http://docs.microsoft.com/cli/azure/overview).
* 최신의 [Java Development Kit(JDK)](http://www.oracle.com/technetwork/java/javase/downloads/), 버전은 1.8 또는 그 이상.
* [Git](https://github.com/) 클라이언트.

## 샘플 Spring Boot 웹앱 만드는 과정
본 섹션에서는 이미 작성된 Spring Boot 어플리케이션을 복제한 후, 로컬 환경에서 테스트 하게 됩니다:

1. 터미널 창을 엽니다.

2. `mkdir SpringBoot` 명령으로 Spring Boot 어플리케이션이 저장될 로컬 디렉토리를 생성합니다.

3. `cd SpringBoot` 명령으로 해당 디렉토리로 이동합니다.

4. `git clone https://github.com/microsoft/gs-spring-boot` 명령으로 [Spring Boot Getting Started](https://github.com/microsoft/gs-spring-boot)라는 샘플 프로젝트를, 방금 생성한 디렉토리에 복제하여 넣습니다.

5. `cd gs-spring-boot/complete` 명령으로 완료된 프로젝트가 존재하는 디렉토리로 이동합니다.

6. `./mvnw clean package` 명령으로 Maven을 사용하여 JAR 파일을 빌드 합니다.

7. 웹앱이 생성되면, `./mvnw spring-boot:run` 명령으로 해당 웹앱을 실행합니다.

8. http://localhost:8080 을 방문하거나, 또 다른 터미널 창에서 `curl http://localhost:8080` 명령을 수행하여 로컬환경에서 테스트 합니다.

9. 그러면 다음과 같은 메세지가 출력됨이 확인 되어야만 합니다: **Greetings from Spring Boot!**

## Azure service principal를 생성하는 단계
본 섹션에서는 웹앱을 Azure로 배포시 Maven 플러그인이 사용하는 [Azure service principal](https://cmatskas.com/service-principals-in-microsoft-azure/)를 생성하게 됩니다.

1. 터미널 창을 엽니다.

2. `az login` 라는 Azure CLI 명령으로 Azure 계정에 로그인 합니다.

3. `az ad sp create-for-rbac --name "uuuuuuuu" --password "pppppppp" `(`uuuuuuuu`와 `pppppppp`는 service principal에 대한 사용자이름과 비밀번호 입니다) 명령으로 Azure service principal을 생성합니다.

   그러면, Azure는 아래와 닮은 JSON 응답을 출력 해야만 합니다:

    ```json
    {
        "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "displayName": "uuuuuuuu",
        "name": "http://uuuuuuuu",
        "password": "pppppppp",
        "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
    }
    ```

> 값들은 아래쪽에서 계속해서 사용됨을 알아두세요.

## Azure service principal을 사용하기 위한 Maven을 설정하는 단계
본 섹션에서는 웹앱 배포를 위한 Azure service principal 사용을 인증하는데 있어서, Maven을 설정하게 됩니다.

1. Maven의 `settings.xml` 파일을 텍스트 편집기로 엽니다 (보통 `/etc/maven/settings.xml` 또는 `$HOME/.m2/settings.xml`에 위치해 있습니다).

2. 아래 처럼, 본 튜토리얼의 직전 세션내용에 기반하여 Azure service principal 에 대한 설정을 **settings.xml** 파일 내용 중 `<servers>` 컬렉션에 추가하여 줍니다:

```xml
<servers>
   <server>
     <id>azure-auth</id>
      <configuration>
         <client>aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa</client>
         <tenant>tttttttt-tttt-tttt-tttt-tttttttttttt</tenant>
         <key>pppppppp</key>
         <environment>AZURE</environment>
      </configuration>
   </server>
</servers>
```

3. 저장하고, **settings.xml** 파일을 닫습니다.

> [Azure 웹앱을 위한 Maven 플러그인](https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin) 도큐먼트를 확인해 보세요.

## 웹앱을 빌드하고 Azure로 배포하는 단계
앞 섹션들의 모든 설정을 완료 했다면, 웹앱을 Azure로 배포할 준비가 되었습니다.

터미널 창에서, `./mvnw azure-webapp:deploy` 명령으로 웹앱을 Maven을 이용하여 Azure로 배포합니다. (Maven이 복제한 샘플 프로젝트의 빌드 파일에 이미 포함되어 있는, 플러그인을 사용하여 웹앱을 Azure로 배포하게 됩니다. 만약 웹앱이 존재하지 않는다면, 해당 명령이 이를 생성하게 됩니다.)

웹앱이 배포되면, 이를 관리하기 위해서 [Azure 포탈](https://portal.azure.com/)에 접속합니다. 다음과 같이 **App Services** 에 리스트업 될 것입니다:

![](http://spring.io/guides/gs/spring-boot-for-azure/AP01.png)
해당 어플리케이션을 클릭합니다. 그러면, 웹앱을 퍼블릭에서 접근 가능한 URL이 **Overview** 섹션에 리스트업 될 것입니다.

![](http://spring.io/guides/gs/spring-boot-for-azure/AP02.png)
해당 링크를 클릭하면, Spring Boot 어플리케이션을 방문하고 상호작용 할 수 있습니다.

## 요약
축하드립니다! Spring Boot 앱을 빌드하고 Azure로 배포 하였습니다.

## 추가로 볼만한 것
Azure와 Spring사용법에 대한 추가적인 정보는 아래에서 찾아볼 수 있습니다:

* [Azure에서의 Spring](https://docs.microsoft.com/java/azure/spring-framework/)
* [Azure 웹 앱을 위하여 Maven 플러그인을 사용한 Spring Boot 앱의 클라우드 배포 방법](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin)
