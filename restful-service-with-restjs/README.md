## rest.js를 이용하여 RESTful 웹 서비스 만들기
이 가이드는 스프링 MVC 기반의 [RESTful web service](http://spring.io/understanding/REST)를 사용하여 간단한 rest.js 클라이언트를 만드는 과정으로 안내합니다.

## 무엇을 만들게 되는가?
여러분은 스프링 기반의 RESTful 웹 서비스를 사용한 rest.js 클라이언트를 만들 것입니다. 특히 이 클라이언트는 [Building a RESTful Web Service with CORS](http://spring.io/guides/gs/rest-service-cors/)로 만들어진 서비스를 사용해서 만들 예정입니다.

이 rest.js 클라이언트를 브라우저에서 열면 `index.html` 파일에 접근하게 됩니다. 그리고 이 클라이언트는 다음의 서비스에 접근을 시도할 것입니다:

```
http://rest-service.guides.spring.io/greeting
```

이 서비스는 greeting 요청에 대한 응답으로 아레와 같은 [JSON](http://spring.io/understanding/JSON)을 돌려줄 것입니다.

```json
{"id":1,"content":"Hello, World!"}
```

이 클라이언트는 서비스의 응답으로 받은 ID와 콘텐츠를 DOM 안에 넣을 것입니다.

여러분은 url 안의 **쿼리 스트링**을 넣어서 greeting에 대한 요청을 임의로 변경할 수 있습니다.

```
http://localhost:8080/?User
```

그리고 코드에서는 REST 파라미터를 보내서 greeting에 대한 응답을 DOM 안에 표시할 것입니다.

## 무엇이 필요한가?
* 약 15분의 시간
* 좋아하는 텍스트 에디터
* 최신의 웹 브라우저
* 인터넷 연결
* Node.js 와 [Git](http://spring.io/understanding/Git)이 설치된 환경
* 글로벌로 설치 된 Bower node.js [JavaScript package](http://spring.io/understanding/javascript-package-managers)

## bower 환경설정 파일 만들기
첫째로 bower 컨트롤 파일인 `.bowerrc`를 만듭니다. 이 파일은 bower가 자바스크립트 의존 파일을 어디 놓을 것인지 알려줍니다. `.bowerrc`파일은 프로젝트의 루트 기준으로 ({project_id}/initial) 폴더 아래에 JSON 포맷으로 생성 후하는 것이 좋습니다:

`.bowerrc`

```json
{
	"directory": "public/lib"
}
```

커맨드 프롬프트에서 프로젝트의 루트 폴더로 이동 후 `bower init`을 입력하여 실행시킵니다. 그러면 프로젝트에서 필요한 [JavaScript package](http://spring.io/understanding/javascript-package-managers)가 쓰인 `bower.json` 파일이 만들어집니다. 또한 Bower는 프로젝트 명이나 라이센스 등 여러 가지 정보를 물어볼 것입니다. 잘 모르겠고 의심스럽다면, `Enter`를 눌러서 기본값으로 지정된 것을 사용하세요.

다음으로 Bower를 사용해서 rest.js와 curl.js 같은 [AMD module](http://spring.io/understanding/javascript-modules)을 설치합니다. 커맨드 프롬프트에서 입력하세요:

```
bower install --save rest#~1
```

```
bower install --save curl#~0.8
```

Bower는 `.bowerrc`에 지정한 디렉토리로 rest.js와 curl.js를 설치할 것입니다. 우리가 설치할 때 옵션을 `--save`로 지정하면 Bower는 그 이후 `bower.json` 파일에 패키지 정보를 저장하게 됩니다.

> Bower는 rest.js가 when.js에 의존하며 호환 가능한 버전을 설치해야 한다는 것을 발견합니다.

완료되면 `bower.json`파일은 "dependencies" 오브젝트의 속성으로 semver 정보가 표시된 "curl"과 "rest"속성을 가질 것입니다:

`bower.json`

```json
{
  "name": "consuming-rest-rest.js",
  "version": "0.0.1",
  "authors": [
    "John Hann <jhann@gopivotal.com>"
  ],
  "license": "http://www.apache.org/licenses/LICENSE-2.0",
  "ignore": [
    "**/.*",
    "node_modules",
    "public/lib",
    "test",
    "tests"
  ],
  "dependencies": {
    "curl": "~0.8",
    "rest": "~1"
  }
}
```

## 화면에 표시될(render) 모듈 만들기
첫째로 HTML 문서에 데이터를 집어넣을 render 함수를 만듭니다.

`public/hello/render.js`

```js
define(function (require) {

    var ready = require('curl/domReady');

    return render;

    function render (entity) {
        ready(function () {
            var idElement, nameElement;

            idElement = document.querySelector('[data-name="id"]');
            nameElement = document.querySelector('[data-name="content"]');

            idElement.textContent += entity.id;
            nameElement.textContent += entity.content;
        });
    }

});
```

이 [AMD module](http://spring.io/understanding/javascript-modules)은 간단한 DOM 쿼리 스트링을 이용해서 문서에 텍스트를 집어넣습니다. DOM이 준비되기 전에 사용하는 것을 막기 위해서 render 모듈은 curl.js 안의 `domReady` 함수 모듈을 사용합니다.

> 실제 서비스에서 여러분은 현재 DOM 조작한 부분에 **데이터 바인딩**이나 **템플릿 작업**을 할 확률이 높습니다.

## 어플리케이션을 구성하는 모듈 만들기
다음에는 어플리케이션을 구성하는 모듈을 만들겠습니다.

`public/hello/main.js`

```js
define(function (require) {

    var rest = require('rest');
    var mime = require('rest/interceptor/mime');
    var entity = require('rest/interceptor/entity');
    var render = require('./render');

    var endpointUrl, name, client;

    endpointUrl = 'http://rest-service.guides.spring.io/greeting';
    name = document.location.search.slice(1);

    client = rest
        .chain(mime, { mime: 'application/json' })
        .chain(entity);

    client({ path: endpointUrl, params: { name:  name } })
        .then(render);

});
```

메인 모듈은 document.location 오브젝트로부터 쿼리 스트링을 읽어 rest.js의 mime 오브젝트를 구성하고 호출에 대한 REST 응답을 호출합니다.

rest.js는 [Promises/A+ promise](http://spring.io/understanding/javascript-promises)를 리턴합니다, 그러므로 나중에 데이터를 받으면 `render` 함수 모듈을 호출할 것입니다. `render` 함수는 즉시 사용 가능한 개체(entity)를 원하는데 rest.js는 보통 response 오브젝트를 반환합니다. 그래서 "rest/interceptor/entity" 인터셉터를 사용해서 response 오브젝트로부터 개체(entity)를 뽑아 `render` 함수로 전달할 것입니다.

## 부트 스크립트 만들기
다음으로는 부트 스크립트인 `run.js`를 만들어 보겠습니다:

`public/run.js`

```js
var curl;
(function () {

    curl.config({
        main: 'hello',
        packages: {
            // Your application's packages
            hello: { location: 'hello' },
            // Third-party packages
            curl: { location: 'lib/curl/src/curl' },
            rest: { location: 'lib/rest', main: 'rest' },
            when: { location: 'lib/when', main: 'when' }
        }
    });

}());
```

이 스크립트는 `curl.config()`로 AMD 로더를 설정합니다. 그리고 curl.js는 메인 모듈의 `main` 속성으로 지정된 경로에서 파일을 찾아 자동으로 불러옵니다. `packages` 속성은 curl.js에게 이 어플리케이션의 패키지와 다른 사람이 제작한 패키지를 참조할 위치를 알려줍니다.

## 어플리케이션 페이지 만들기
마지막으로 `index.html`를 만들어 보겠습니다.

`public/index.html`

```html
<!doctype html>
<html>
    <head>
        <title>Hello rest.js</title>
        <script data-curl-run="run.js" src="lib/curl/src/curl.js"></script>
    </head>
    <body>
        <div>
            <p data-name="id">The ID is </p>
            <p data-name="content">The content is </p>
        </div>
    </body>
</html>
```

`script` 요소는 curl.js를 로드하고 "run.js"라는 **어플리케이션 시작 스크립트**를 로드합니다. 이 시작 스크립트는 AMD 모듈 환경설정을 구성하고 초기화 후 클라이언트의 코드를 실행합니다.

## 클라이언트 실행하기
클라이언트를 실행하려면, 여러분은 웹 서버에서 브라우저로 콘텐츠를 제공해야 합니다. 스프링 부트 CLI(Command Line Interface)는 여러분이 웹 콘텐츠를 간단히 제공할 수 있도록 내장된 톰캣 서버를 포함하고 있습니다. CLI에 대해 많은 정보를 얻으시려면 [Building an Application with Spring Boot](http://spring.io/guides/gs/spring-boot/)를 보세요.

여러분은 내장된 톰캣 서버로 스프링 부트의 정적인 콘텐츠를 제공하기 위해 스프링 부트가 톰캣 서버에서 어플리케이션 코드를 실행하기 위한 최소한의 코드를 제공해야 합니다. 하단의 `app.groovy` 스크립트는 스프링 부트에게 여러분이 톰캣을 실행시키고 싶다고 알려주기 위한 충분한 단어입니다:

`app.groovy`

```groovy
@Controller class JsApp { }
```

이제 여러분은 스프링 부트 CLI로 앱을 실행시킬 수 있습니다:

```
spring run app.groovy
```

앱이 시작하면 브라우저를 열어서 http://localhost:8080를 입력하세요. 그러면 아래와 같은 화면을 불 수 있습니다:

![](http://spring.io/guides/gs/consuming-rest-restjs/images/hello.png)

ID 값은 여러분이 페이지를 새로고침 할 때마다 증가할 것입니다.

## 요약
축하합니다! 여러분은 방금 스프링 기반의 RESTful 웹서비스를 사용한 rest.js 클라이언트를 만드셨습니다.

## 추가로 볼만한 것
아래 가이드를 따라 하면 도움이 될 것입니다:

* [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)
* [Consuming a RESTful Web Service](https://spring.io/guides/gs/consuming-rest/)
* [Consuming a RESTful Web Service with AngularJS](https://spring.io/guides/gs/consuming-rest-angularjs/)
* [Consuming a RESTful Web Service with jQuery](https://spring.io/guides/gs/consuming-rest-jquery/)
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