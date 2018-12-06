## jQuery로 RESTful 웹서비스 사용하기
이 가이드는 스프링-MVC 기반의 [RESTful web service](http://spring.io/understanding/REST)를 사용하는 간단한 jQuery 클라이언트를 만드는 과정으로 안내합니다.

## 무엇을 만들게 되는가?
여러분은 스프링 기반의 RESTful 웹서비스를 사용하는 jQuery 클라이언트를 만들 것입니다. 특히 클라이언트는 [Building a RESTful Web Service with CORS](http://spring.io/guides/gs/rest-service-cors/)로 만들어진 서비스를 사용해 볼 것입니다.

jQuery 클라이언트는 여러분의 브라우저에서 `index.html`파일에 접근해서 다음의 서비스를 요청할 것입니다:

```
http://rest-service.guides.spring.io/greeting
```

해당 서비스는 greeting 요청에 대해 [JSON](http://spring.io/understanding/JSON)으로 대답할 것입니다.

```json
{"id":1,"content":"Hello, World!"}
```

그리고 이 jQuery 클라이언트는 ID와 content를 DOM에 넣을 것입니다.

## 무엇이 필요한가?
* 약 15분의 시간
* 좋아하는 텍스트 편집기
* 최근에 나온 웹 브라우저
* 인터넷 연결

## jQuery 컨트롤러 만들기 
첫 번째로, 여러분은 REST 서비스를 사용하는 jQuery 컨트롤러 모듈을 만들어야 합니다.

`public/hello.js`

```js
$(document).ready(function() {
    $.ajax({
        url: "http://rest-service.guides.spring.io/greeting"
    }).then(function(data) {
       $('.greeting-id').append(data.id);
       $('.greeting-content').append(data.content);
    });
});
```

컨트롤러 모듈은 간단한 자바스크립트 함수로 표현되었습니다. 그리고 jQuery의 `$.ajax()` 메소드를 사용하여 http://rest-service.guides.spring.io/greeting의 REST 서비스를 사용합니다. 성공적으로 가져오면, JSON으로 받은 `data`를 활용하여 `Greeting` 모델 객체를 만듭니다. 그리고 `id`와 `content`는 각각 `greeting-id`과 `greeting-content` DOM 요소에 추가됩니다.

jQuery가 `.then()`이라는 프로미스를 사용하는 것을 주목하세요. 이 메소드는 jQuery에서 익명 함수를 실행 시 사용된 `$.ajax()`라는 비동기 메소드가 완료될 때 AJAX 응답이 끝나면 `data`라는 결과를 넘겨줍니다.


## 어플리케이션 페이지 만들기 
여러분은 jQuery 컨트롤러를 만들었습니다. 이제는 웹브라우저 안으로 불러올 HTML 페이지를 만들어 보겠습니다.

`public/index.html`

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Hello jQuery</title>
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js"></script>
        <script src="hello.js"></script>
    </head>

    <body>
        <div>
            <p class="greeting-id">The ID is </p>
            <p class="greeting-content">The content is </p>
        </div>
    </body>
</html>
```

`<head>` 태그 안에 들어있는 두 개의 스크립트를 확인해 보겠습니다.

```html
<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js"></script>
<script src="hello.js"></script>
```

첫 번째 스크립트는 CDN으로부터 압축된 jQuery 라이브러리(jquery.min.js)를 불러옵니다. 그래서 여러분은 jQuery를 다운로드 후 프로젝트 안에 놓을 필요가 없습니다.

이 스크립트는 또한 여러분의 어플리케이션 경로 안에 있는 컨트롤러 코드(hello.js)를 불러옵니다.

이번에는 `<p>`태그 안에 포함된 `class` 속성을 확인해 보겠습니다.

```html
<p class="greeting-id">The ID is </p>
<p class="greeting-content">The content is </p>
```

이 `class`속성은 jQuery가 HTML 요소를 참조하는 것과 REST 서비스에서 받아온 JSON의 `id`와 `content`값을 사용해 업데이트하는 것을 도와줍니다.

## 클라이언트 실행시키기
클라이언트를 실행하려면, 여러분은 웹 서버에서 브라우저로 콘텐츠를 제공해야 합니다. 스프링 부트 CLI(Command Line Interface)는 여러분이 웹 콘텐츠를 간단히 제공할 수 있도록 내장된 톰캣 서버를 포함하고 있습니다. CLI에 대해 많은 정보를 얻으시려면 [Building an Application with Spring Boot](http://spring.io/guides/gs/spring-boot/)를 보세요.

여러분은 내장된 톰캣 서버로 스프링 부트의 정적인 콘텐츠를 제공하기 위해 스프링 부트가 톰캣 서버에서 어플리케이션 코드를 실행하기 위한 최소한의 코드를 제공해야 합니다. 하단의 `app.groovy` 스크립트는 스프링 부트에게 여러분이 톰캣을 실행시키고 싶다고 알려주기 위한 충분한 단어입니다.

`app.groovy`

```groovy
@Controller class JsApp { }
```

이제 여러분은 스프링 부트 CLI를 실행시킬 수 있습니다.

```
spring run app.groovy
```

앱이 시작하면 브라우저를 열어서 http://localhost:8080를 입력하세요. 그러면 아래와 같은 화면을 불 수 있습니다:

![](http://spring.io/guides/gs/consuming-rest-jquery/images/hello.png)

ID 값은 여러분이 페이지를 새로고침 할 때마다 증가할 것입니다.

## 요약
축하합니다! 여러분은 방금 스프링 기반의 RESTful 웹서비스를 사용한 jQuery 클라이언트를 만들었습니다.

## 추가로 볼만한 것
아래 가이드를 따라 하면 도움이 될 것입니다:

* [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)
* [Consuming a RESTful Web Service](https://spring.io/guides/gs/consuming-rest/)
* [Consuming a RESTful Web Service with AngularJS](https://spring.io/guides/gs/consuming-rest-angularjs/)
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