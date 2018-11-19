## Maven으로 Java Projects 빌드하기
이 가이드는 Maven을 사용하여 간단한 Java 프로젝트를 작성하는 과정을 안내합니다.

## 무엇을 만들게 되는가?
현재의 시간을 제공하고 Maven으로 빌드하는 어플리케이션을 만들것입니다. 

## 무엇이 필요한가?
* 약 15분
* 좋아하는 텍스트 편집기 또는 IDE
* [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 또는 그 이상

## 이 가이드를 완료하는 방법
대부분의 Spring[시작가이드](http://spring.io/guides)처럼 처음부터 시작해서 완료하거나 이미 익숙한 기본 설정단계를 건너뛸 수 있습니다. 어느쪽이든 작업코드로 끝납니다.

**처음부터 시작** 하려면 [프로젝트 설정](#set-up-the-project)으로 이동하십시오.

**기초를 생략** 하려면 다음을 수행하십시오:

* [다운로드](https://github.com/spring-guides/gs-maven/archive/master.zip) 및 이 가이드의 소스 repository를 압축 해제하거나, [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-maven.git`을 사용하여 복제하십시오.
* cd 명령어로 `gs-maven/initial` 으로 이동하십시오.
* [initial](#initial)로 바로 이동.

**작업이 끝나면** `gs-maven/complete`에 있는 코드와 결과를 비교할 수 있습니다.

## 프로젝트 설정
먼저 빌드할 Maven용 Java 프로젝트를 설정해야 합니다. Maven에 초점을 맞추려면 가능한 한 프로젝트를 단순하게 만드십시오. 선택한 프로젝트 폴더에 프로젝트를 생성합니다.

## 디렉토리 구조 만들기
선택한 프로젝트 디렉토리에서 다음과 같은 하위 디렉토리 구조를 만듭니다. *nix 시스템에서는 `mkdir -p src/main/java/hello`를 사용합니다:

```
└── src
    └── main
        └── java
            └── hello
```

`src/main/java/hello` 디렉토리 안에 원하는 Java 클래스를 생성할 수 있습니다. 이 가이드의 일관성을 유지하지하려면 다음과 같은 두 클래스를 생성하십시오: `HelloWorld.java` 와 `Greeter.java`.

`src/main/java/hello/HelloWorld.java`

```java
package hello;

public class HelloWorld {
    public static void main(String[] args) {
        Greeter greeter = new Greeter();
        System.out.println(greeter.sayHello());
    }
}
```

`src/main/java/hello/Greeter.java`

```java
package hello;

public class Greeter {
    public String sayHello() {
        return "Hello world!";
    }
}
```

## 초기 설정
이제 Maven으로 빌드할 준비과 된 프로젝트가 생겼으니, 다음 단계는 Maven을 설치하는 것입니다.

Maven은 http://maven.apache.org/download.cgi 에서 zip 파일로 다운로드할 수 있습니다. 바이너리만 필요하므로 apache-maven-{version}-bin.zip 또는 apache-maven-{*version*}-bin.tar.gz에 대한 링크를 찾으십시오.

zip 파일을 다운로드한 다음 컴퓨터에 압축을 풉니다. 그 다음 경로에 bin 폴더를 추가하십시오.

Maven 설치를 테스트하려면 command-line에서 `mvn`을 실행하십시오.

```
mvn -v
```

모든 것이 잘 진행된다면 Maven 설치에 대한 정보를 얻을 수 있습니다. 다음과 같이(아마 약간 다르게라도) 나타날 것입니다. 

```
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-10T16:41:47+00:00)
Maven home: /home/dsyer/Programs/apache-maven
Java version: 1.8.0_152, vendor: Azul Systems, Inc.
Java home: /home/dsyer/.sdkman/candidates/java/8u152-zulu/jre
Default locale: en_GB, platform encoding: UTF-8
OS name: "linux", version: "4.15.0-36-generic", arch: "amd64", family: "unix"
```

축하합니다! 이제 Maven이 설치되었습니다.

정보 : [Maven wrapper](https://github.com/takari/maven-wrapper)를 사용하여 개발자가 Maven의 올바른 버전을 보유하지 못하도록 차단하거나 전체를 설치해야 하는 경우를 고려하십시오. [Spring Initializr](https://start.spring.io/)에서 다운로드한 프로젝트에는 wrapper가 포함되어 있습니다. `mvn` 대신 실행한 프로젝트 최상위 단계에서 `mvnw` 스크립트로 나타납니다.

## 간단한 Maven 빌드 정의
이제 Maven이 설치되었으므로 Maven 프로젝트 정의를 만들어야 합니다. Maven 프로젝트는 *pom.xml* 이라는 XML 파일로 정의됩니다. 이 파일은 외부 라이브러리에 있는 프로젝트의 이름, 버전 및 종속성을 제공합니다.

프로젝트의 root에 pom.xml이라는 파일을 생성하고(즉, `src` 폴더 옆에 배치) 다음 내용을 지정합니다:
`pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-maven</artifactId>
    <packaging>jar</packaging>
    <version>0.1.0</version>

    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.1</version>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                        <configuration>
                            <transformers>
                                <transformer
                                    implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <mainClass>hello.HelloWorld</mainClass>
                                </transformer>
                            </transformers>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

선택적인 `<packaging>` 요소를 제외하고, 이것은 Java 프로젝트를 만드는데 필요한 가장 간단한 *pom.xml* 파일입니다. 여기에는 프로젝트 구성에 대한 다음 세부사항이 포함됩니다:

* `<modelVersion>`. POM 모델 버전 (항상 4.0.0).
* `<groupId>`. 프로젝트가 속한 그룹 또는 조직. 종종 반전된 도메인명으로 표현됩니다.
* `<artifactId>`. 프로젝트의 라이브러리 아티팩트에 부여할 이름 (예, JAR 또는 WAR 파일명)
* `<version>`. 빌드중인 프로젝트의 버전
* `<packaging>` - 프로젝트 패키징 방법. JAR 파일 패키징인경우 기본값은 "jar"입니다. WAR 패키지파일에는 "war"를 사용하십시오.

> 버전 관리체계를 선택할 때, Spring은 [semantic versioning](http://semver.org/) 접근방식을 권장합니다.

이 시점에서 최소의, 그러나 유능한 Maven 프로젝트를 정의했습니다.

## Java 코드 빌드
Maven은 이제 프로젝트를 빌드할 준비가 되었습니다. 프로젝트 코드를 컴파일하고, 라이브러리 패키지(예 : JAR파일)를 생성하고, 로컬 Maven 종속 저장소에 라이브러리를 설치하는 것과 같은 목표를 포함하여 Maven에서 여러 빌드 수명주기 명령을 실행할 수 있습니다.

빌드를 시도하려면 command line에서 다음을 실행하십시오:

```
mvn compile
```

이것은  Maven이 실행되어 컴파일 명령을 실행하게됩니다. 작업이 끝나면 컴파일된 *.class* 파일을 *target/classes* 디렉토리에서 찾을 수 있습니다. 

*.class* 파일을 직접 배포하거나 작업하는 경우가 드물기 때문에 *package* 명령을 실행하는 것이 좋습니다:

```
mvn package
```

*package* 명령은 Java코드를 컴파일하고 테스트를 실행하며, *target* 디렉토리에 내의 JAR파일에 코드를 패키징해서 마무리합니다. JAR파일명은 프로젝트의 `<artifactId>`와 `<version>`을 기반으로 합니다. 이전 예제의 *pom.xml* 파일인 경우 JAR파일명은 *gs-maven-0.1.0.jar* 입니다.

> `<packaging>`값을 "jar"에서 "war"로 변경할 경우 결과는 JAR 파일 대신 *target* 디렉터리 내의 WAR 파일이 됩니다.

또한 Maven은 프로젝트의 종속성에 빠르게 액세스 할 수 있도록 로컬 장비(일반적으로 홈디렉토리의 *.m2/repository* 디렉토리)에 종속성 저장소를 유지 관리합니다. 프로젝트의 JAR파일을 해당 로컬 저장소에 설치하려면 `install`명령을 호출해야 합니다:
```
mvn install
```

*install* 명령은 컴파일, 테스트 및 프로젝트 코드를 패키징한 후 로컬 종속 저장소에 복사하여 다른 프로젝트에서 이를 종속적으로 참조할 수 있도록 합니다. 

의존성에 대해 말하자면, 이제 Maven 빌드에 의존성을 선언할 때입니다.

## 종속성 선언
간단한 Hello World 예제는 완전히 자체적으로 포함되며 다른 추가라이브러리에 의존하지 않습니다. 그러나 대부분의 애플리케이션은 공통 및 복잡한 기능을 수행하기 위해 외부라이브러리에 의존합니다.

예를들어, "Hello World!"라고 말하는 것 이외에도, 애플리케이션이 현재 날짜와 시간을 출력하기를 원한다고 가정해보십시오. 기본 Java 라이브러리의 날짜 및 시간 기능을 사용할 수 있지만 Joda Time 라이브러리를 사용하면 더 재미있는 것을 만들 수 있습니다.

먼저, HelloWorld.java를 다음과 같이 변경합니다:
`src/main/java/hello/HelloWorld.java`

```java
package hello;

import org.joda.time.LocalTime;

public class HelloWorld {
	public static void main(String[] args) {
		LocalTime currentTime = new LocalTime();
		System.out.println("The current local time is: " + currentTime);
		Greeter greeter = new Greeter();
		System.out.println(greeter.sayHello());
	}
}
```

여기서 `HelloWorld`는 Joda Time의 `LocalTime`클래스를 사용하여 현재시간을 가져와 출력합니다.

만약 지금 `mvn compile`을 실행하여 프로젝트를 빌드한다면 Joda Time을 컴파일 의존성으로 선언하지 않았으므로 빌드가 실패할 것입니다. *pom.xml*(`<project>` 요소 내)에 다음 줄을 추가하여 수정할 수 있습니다.
```xml
<dependencies>
		<dependency>
			<groupId>joda-time</groupId>
			<artifactId>joda-time</artifactId>
			<version>2.9.2</version>
		</dependency>
</dependencies>
```

이 XML 블록은 프로젝트에 대한 종속성 목록을 선언합니다. 특히 Joda Time 라이브러리에 대한 단일 종속성을 선언합니다. `<dependency>`요소 내에서 종속성은 세가지 하위요소에 의해 정의됩니다.

* `<groupId>` - 종속성이 속한 그룹 또는 조직입니다.
* `<artifactId>` - 필요한 라이브러리입니다.
* `<version>` - 필요한 라이브러리의 특정 버전입니다.

기본적으로, 모든 종속성은 `compile`종속성으로 범위가 지정됩니다. 즉 컴파일시 사용이 가능해야 합니다(WAR 파일을 빌드하는 경우 WAR의 */WEB-INF/libs* 폴더 포함). 추가적으로 다음 스코프 중 하나를 지정하는 `<scope>`요소를 지정할 수 있습니다.

* `provided` - 프로젝트 코드를 컴파일 하는데 필요하지만, 코드를 실행하는 컨테이너(예: Java Servlet API)에 의해 런타임에 제공되는 종속성입니다.

* `test` - 테스트를 컴파일하고 실행하는데 사용되지만, 프로젝트의 런타임 코드를 작성하거나 실행할 때는 필요하지 않은 종속성입니다.

이제 `mvn complire` 또는 `mvn package`를 실행하면 Maven은  Maven 중앙 저장소로부터 Joda Time 종속성을 가져와 해결할 것이며 빌드는 성공할 것입니다.  

## 테스트 작성하기
처음 JUnit 종속성을 test scope로 pom.xml에 추가합니다:

```xml
<dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
	<version>4.12</version>
	<scope>test</scope>
</dependency>
```

그리고 다음과 같이 테스트케이스를 만듭니다:
`src/test/java/hello/GreeterTest.java`

```java
package hello;

import static org.hamcrest.CoreMatchers.containsString;
import static org.junit.Assert.*;

import org.junit.Test;

public class GreeterTest {

	private Greeter greeter = new Greeter();

	@Test
	public void greeterSaysHello() {
		assertThat(greeter.sayHello(), containsString("Hello"));
	}

}
```

Maven은 "surefire"라는 플러그인을 단위테스트에 사용합니다. 이 플러그인의 기본 구성은 컴파일 및 `src/test/java`의 모든 클래스를  `*Test*`와 일치하는 이름으로 실행합니다. 명령줄에서 다음과 같이 실행할 수 있습니다.
```
mvn test
```

또는 앞서 설명한 대로 `mvn install`단계를 사용하십시오(여기서 "test"는 "install"의 단계로 포함됩니다).

완료된 `pom.xml`파일은 다음과 같습니다:

`pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>org.springframework</groupId>
	<artifactId>gs-maven</artifactId>
	<packaging>jar</packaging>
	<version>0.1.0</version>

	<properties>
		<maven.compiler.source>1.8</maven.compiler.source>
		<maven.compiler.target>1.8</maven.compiler.target>
	</properties>

	<dependencies>
		<!-- tag::joda[] -->
		<dependency>
			<groupId>joda-time</groupId>
			<artifactId>joda-time</artifactId>
			<version>2.9.2</version>
		</dependency>
		<!-- end::joda[] -->
		<!-- tag::junit[] -->
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.12</version>
			<scope>test</scope>
		</dependency>
		<!-- end::junit[] -->
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-shade-plugin</artifactId>
				<version>2.1</version>
				<executions>
					<execution>
						<phase>package</phase>
						<goals>
							<goal>shade</goal>
						</goals>
						<configuration>
							<transformers>
								<transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
									<mainClass>hello.HelloWorld</mainClass>
								</transformer>
							</transformers>
						</configuration>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>

</project>
```

> 완료된 **pom.xml**파일은 간편한 JAR 파일 실행을 위해 [Maven Shade Plugin](https://maven.apache.org/plugins/maven-shade-plugin/)을 사용합니다. 이 가이드는 특정 플러그인을 사용하지 않고 Maven을 사용하는 것에 초점을 두었습니다.

## 요약
축하합니다! Java 프로젝트 빌드시 간단하면서도 효과적인 Maven 프로젝트 정의를 작성했습니다.

## 추가로 볼만한 것
다음 가이드 또한 도움이 될 수 있습니다.
* [Gradle로 Java프로젝트 빌드하기](https://spring.io/guides/gs/gradle/)
