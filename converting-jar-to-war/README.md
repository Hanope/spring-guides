## 스프링 부트 JAR 어플리케이션을 WAR로 변환하기
스프링 부트는 두개의 강력한 플러그인이 있습니다:

* `spring-boot-gradle-plugin`
* `spring-boot-maven-plugin`

두개의 플러그인은 본질적으로 스프링 부트 어플리케이션을 커맨드라인 창에서 실행가능한 JAR로 묶어주는 기능을 제공합니다. 이러한 기능은 대부분 가이드의 마지막에 언급됩니다.

많은 사람들은 웹 컨테이너 안에 배포할 WAR파일을 만들기를 원합니다. 물론 위의 플러그인도 WAR파일을 만드는 기능을 지원합니다. 그래서 기본적으로 여러분의 프로젝트안에서 JAR에 포함된 컨테이너 종속성을 "제공된"것으로 선언해주는 WAR파일로 재구성해야 합니다. 그러면 WAR파일 안에는 JAR에 포함된 컨테이너 종속성이 포함되지 않게됩니다.

여러분의 웹 컨테이너를 위해 어플리케이션을 WAR파일로 만드는 자세한 방법은 다음을 참조하세요:

* [Packaging executable jar and war files with Maven](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#build-tool-plugins-maven-packaging)
* [Spring Boot Gradle Plugin](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#build-tool-plugins-gradle-plugin) or
* [Gradle Plugin Reference: Packaging executable wars](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html/#packaging-executable-wars)

> 스프링 부트는 Servlet 3.0 스펙의 컨테이너에서 동작합니다.

## 추가로 볼만한 것
이 가이드들을 따라 하면 도움이 될 것입니다:

* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)