## AngularJS로 RESTful 웹서비스 사용하기
이 가이드는 스프링 MVC 기반의 [RESTful web service](http://spring.io/understanding/REST)를 사용해서 간단한 AngularJS 클라이언트를 만드는 과정으로 안내합니다.

## 무엇을 만들게 되는가?
여러분은 스프링 기반의 RESTful 웹서비스를 사용하는 AngularJS 클라이언트를 만들 것입니다. 특히 클라이언트는 [Building a RESTful Web Service with CORS](http://spring.io/guides/gs/rest-service-cors/)로 만들어진 서비스를 사용해 볼 것입니다.

이 AngularJS 클라이언트는 여러분의 브라우저에서 `index.html`파일에 접근해서 다음의 서비스를 요청할 것입니다:

```
http://rest-service.guides.spring.io/greeting
```

이 서비스는 greeting 요청에 대해 [JSON](http://spring.io/understanding/JSON)으로 대답할 것입니다.

```json
{"id":1,"content":"Hello, World!"}
```

그리고 이 AngularJS 클라이언트는 ID와 content를 DOM에 넣을 것입니다.

> 이 rest-service.guides.spring.io 서비스는 [CORS guide](http://spring.io/guides/gs/rest-service-cors/)에서 약간 수정을 거쳤고 `/greeting` 접근에 `@CrossOrigin`이 사용되어 공개적으로 접근할 수 있도록 되어있습니다.

## 무엇이 필요한가?
* 약 15분의 시간
* 좋아하는 텍스트 에디터
* 최근에 나온 웹 브라우저
* 인터넷 연결

## AngularJS 컨트롤러 만들기
첫번째로, 여러분은 REST 서비스를 사용하는 AngularJS 컨트롤러 모듈을 만들어 볼 것입니다.

`public/hello.js`

```js
angular.module('demo', [])
.controller('Hello', function($scope, $http) {
    $http.get('http://rest-service.guides.spring.io/greeting').
        then(function(response) {
            $scope.greeting = response.data;
        });
});
```

This controller module is represented as a simple JavaScript function that 
is given AngularJS’s `$scope` and `$http` components. 
It uses the `$http` component to consume the REST service at "/greeting".
이 컨트롤러 모듈은 AngularJS의 `$scope`와 `$http` 컴포넌트를 가지는 간단한 자바스크립트 함수를 제공받습니다.
그리고 "/greeting" 요청의 REST 서비스를 사용하기 위해 `$http` 컴포넌트를 사용합니다.

If successful, it will assign the JSON returned back from the service to `$scope.greeting`, effectively setting a model object named "greeting". By setting that model object, AngularJS can bind it to the application page’s DOM, rendering it for the user to see.
성공적으로 응답을 받으면, 이 컨트롤러는 서비스 요청에 대한 응답으로 온 JSON을 `$scope.greeting`에 넣고 "greeting"이라는 모델 오브젝트를 설정할 것입니다.
모델 오브젝트가 설정되면 AngularJS는 어플리케이션 페이지 DOM에 바인딩 할 수 있으며 유저에게 보여주도록 렌더링 할 수 있습니다.

## 어플리케이션 페이지 만들기
Now that you have an AngularJS controller, you will create the HTML page that will load the controller 
into the user’s web browser:
컨트롤러를 사용자의 웹 브라우저에 로드할 HTML 페이지를 만들어 보겠습니다.

`public/index.html`

```html
<!doctype html>
<html ng-app="demo">
	<head>
		<title>Hello AngularJS</title>
		<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.4.3/angular.min.js"></script>
    	<script src="hello.js"></script>
	</head>

	<body>
		<div ng-controller="Hello">
			<p>The ID is {{greeting.id}}</p>
			<p>The content is {{greeting.content}}</p>
		</div>
	</body>
</html>
```

Note the following two script tags within the `head` section.
`head` 태그 안에 두개의 스크립트 태그를 확인해보겠습니다.

```html
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.4.3/angular.min.js"></script>
<script src="hello.js"></script>
```

The first script tag loads the minified AngularJS library (**angular.min.js**) from a content delivery network (CDN) 
so that you don’t have to download AngularJS and place it in your project. 
It also loads the controller code (**hello.js**) from the application’s path.
첫 번째 스크립트 태그는 압축된 AngularJS 라이브러리(**angular.min.js**)를 CDN으로 부터 불러오는 태그입니다. 그래서 여러분은 직접 라이브러리를 다운받아 프로젝트에 넣을 필요가 없습니다.
그리고 나머지 태그는 어플리케이션 폴더안에 컨트롤러 코드(**hello.js**)를 로드됩니다.

The AngularJS library enables several custom attributes for use with standard HTML tags. 
In index.html, two such attributes are in play:
AngularJS 라이브러리는 표준 HTML 태그에서 몇개의 커스텀 속성을 이용할 수 있게 합니다.
index.html에서는 두개의 속성을 사용합니다:

* The `<html>` tag has the `ng-app` attribute to indicate that this page is an AngularJS application.
* `<html>` 태그안의 `ng-app` 속성은 이 페이지가 AngularJS 어플리케이션 페이지 인것을 알려줍니다.
* The `<div>` tag has the `ng-controller` attribute set to reference `Hello`, the controller module.
* `<div>` 태그안의 `ng-controller` 속성은 컨트롤러 모듈에서 `Hello` 속성을 참조하도록 설정합니다.

Also note the two `<p>` tags which use placeholders (identified by double-curly-braces).
또한 두개의 `<p>` 태그는 데이터를 바인딩하기 위한 이중중괄호({{}}) 구문(placeholders)을 사용합니다.

```html
<p>The ID is {{greeting.id}}</p>
<p>The content is {{greeting.content}}</p>
```

The placeholders reference the `id` and `content` properties of the `greeting` model object which will be set upon successfully consuming the REST service.
바인딩 하기 위한 구문(placeholders)은 REST 서비스의 응답을 성공적으로 받은 후 설정 돠는 `greeting` 모델 객체의`id`와 `content` 속성을 참조하게 됩니다.

## 클라이언트 실행시키기
To run the client, you’ll need to serve it from a web server to your browser. The Spring Boot CLI (Command Line Interface) includes an embedded Tomcat server, which offers a simple approach to serving web content. See [Building an Application with Spring Boot](http://spring.io/guides/gs/spring-boot/) for more information about installing and using the CLI.
클라이언트를 실행하려면, 여러분은 웹 서버에서 브라우저로 콘텐츠를 제공해야 합니다. 스프링 부트 CLI(Command Line Interface)는 여러분이 웹 콘텐츠를 간단히 제공할 수 있도록 내장된 톰캣 서버를 포함하고 있습니다. CLI에 대해 많은 정보를 얻으시려면 [Building an Application with Spring Boot](http://spring.io/guides/gs/spring-boot/)를 보세요.

In order to serve static content from Spring Boot’s embedded Tomcat server, you’ll also need to create a minimal amount of web application code so that Spring Boot knows to start Tomcat. The following `app.groovy` script is sufficient for letting Spring Boot know that you want to run Tomcat:
여러분은 내장된 톰캣 서버로 스프링 부트의 정적인 콘텐츠를 제공하기 위해 스프링 부트가 톰캣 서버에서 어플리케이션 코드를 실행하기 위한 최소한의 코드를 제공해야 합니다. 하단의 `app.groovy` 스크립트는 스프링 부트에게 여러분이 톰캣을 실행시키고 싶다고 알려주기 위한 충분한 단어입니다.

`app.groovy`

```groovy
@Controller class JsApp { }
```

You can now run the app using the Spring Boot CLI:
이제 여러분은 스프링 부트 CLI로 앱을 실행시킬 수 있습니다:

```
spring run app.groovy
```

Once the app starts, open http://localhost:8080 in your browser, where you see:
앱이 시작하면 브라우저를 열어서 http://localhost:8080를 입력하세요. 그러면 아래와 같은 화면을 불 수 있습니다:

![](http://spring.io/guides/gs/consuming-rest-angularjs/images/hello.png)

The ID value will increment each time you refresh the page.
ID 값은 여러분이 페이지를 새로고침 할 때마다 증가할 것입니다.

## 요약
Congratulations! You’ve just developed an AngularJS client that consumes a Spring-based RESTful web service.
축하합니다! 여러분은 방금 스프링 기반의 RESTful 웹서비스를 사용한 AngularJS 클라이언트를 만들었습니다.

## 추가로 볼만한 것
The following guides may also be helpful:
아래 가이드를 따라 하면 도움이 될 것입니다:

* [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)
* [Consuming a RESTful Web Service](https://spring.io/guides/gs/consuming-rest/)
* [Consuming a RESTful Web Service with jQuery](https://spring.io/guides/gs/consuming-rest-jquery/)
* [Consuming a RESTful Web Service with rest.js](https://spring.io/guides/gs/consuming-rest-restjs/)
* [Accessing GemFire Data with REST](https://spring.io/guides/gs/accessing-gemfire-data-rest/)
* [Accessing MongoDB Data with REST](https://spring.io/guides/gs/accessing-mongodb-data-rest/)
* [Accessing data with MySQL](https://spring.io/guides/gs/accessing-data-mysql/)
* [Accessing JPA Data with REST](https://spring.io/guides/gs/accessing-data-rest/)
* [Accessing Neo4j Data with REST](https://spring.io/guides/gs/accessing-neo4j-data-rest/)
* [Securing a Web Application](https://spring.io/guides/gs/securing-web/)
* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)
* [Creating API Documentation with Restdocs](https://spring.io/guides/gs/testing-restdocs/)
* [Enabling Cross Origin Requests for a RESTful Web Service](https://spring.io/guides/gs/rest-service-cors/)
* [Building a Hypermedia-Driven RESTful Web Service](https://spring.io/guides/gs/rest-hateoas/)