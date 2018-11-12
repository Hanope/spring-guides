## Consuming a RESTful Web Service with rest.js
This guide walks you through the process of consuming a simple rest.js client that consumes a Spring MVC-based [RESTful web service](http://spring.io/understanding/REST).

## What you’ll build
You will build a rest.js client that consumes a Spring-based RESTful web service. Specifically, the client will consume the service created in [Building a RESTful Web Service with CORS](http://spring.io/guides/gs/rest-service-cors/).

The rest.js client will be accessed by opening the `index.html` file in your browser, and will consume the service accepting requests at:

```
http://rest-service.guides.spring.io/greeting
```

The service will respond with a [JSON](http://spring.io/understanding/JSON) representation of a greeting:

```json
{"id":1,"content":"Hello, World!"}
```

The client will render the ID and content into the DOM.

You can customize the greeting with an optional **query string** in the url:

```
http://localhost:8080/?User
```

The code will send a parameter to the REST endpoint and render a custom greeting into the DOM.

## What you’ll need
* About 15 minutes
* A favorite text editor
* A modern web browser
* An internet connection
* Node.js and [Git](http://spring.io/understanding/Git) pre-installed
* Bower installed as a global node.js [JavaScript package](http://spring.io/understanding/javascript-package-managers)

## Create bower configuration files
First, create a bower control file, `.bowerrc`. This file tells bower where to put the JavaScript dependencies. The `.bowerrc` file should be located at the root of the project ({project_id}/initial) and formatted as JSON:

`.bowerrc`

```json
{
	"directory": "public/lib"
}
```

From a command prompt at the root of the project, run `bower init`. This will create a `bower.json` file that describes each [JavaScript package](http://spring.io/understanding/javascript-package-managers) required by the project. Bower will ask for several bits of information such as a project name, license, etc. If in doubt, just press `Enter` to accept the defaults.

Next, use Bower to install rest.js and an [AMD module](http://spring.io/understanding/javascript-modules) loader such as curl.js. From the command prompt, type:

```
bower install --save rest#~1
```

```
bower install --save curl#~0.8
```

Bower will install rest.js and curl.js into the directory we listed in `.bowerrc`. Since we specified the `--save` option, bower will store the package information in the `bower.json` file.

> Bower should discover that rest.js depends on when.js and install a compatible version.

When done, the `bower.json` file should have a "dependencies" object property that lists "curl" and "rest" as property names and their semver information as values:

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

## Create a render module
First, create a render function to inject data into an HTML document.

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

This [AMD module](http://spring.io/understanding/javascript-modules) uses simple DOM querying and manipulation to inject text into the document. To ensure that the DOM is not used before it is ready, the render module imports and uses curl.js’s `domReady` function-module.

> In a real application, you’ll want to use **data binding** or **templating**, rather than DOM manipulation as shown here.

## Create an application composition module
Next, create a module that will compose the application.

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

The main module reads the query string from the document’s location object, configures a rest.js mime client, and calls the REST endpoint.

rest.js returns a [Promises/A+ promise](http://spring.io/understanding/javascript-promises), which will call the `render` function-module when the endpoint returns data. The `render` function expects the entity, but the rest.js client normally returns a response object. The "rest/interceptor/entity" interceptor plucks the entity from the response and forwards that onto the `render` function.

## Create a boot script
Next, create the boot script, `run.js`:

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

This script configures the AMD loader: `curl.config()`. The `main` configuration property tells curl.js where to find the application’s main module, which will be fetched and evaluated automatically. The `packages` config object tells curl.js where to find modules in our application’s packages or in third-party packages.

## Create the application page
Finally, create an `index.html` file and add the following HTML:

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

The `script` element loads curl.js and then loads an **application boot script** named "run.js". The boot script initializes and configures an AMD module environment and then starts the client-side application code.

## Run the client
To run the client, you’ll need to serve it from a web server to your browser. The Spring Boot CLI (Command Line Interface) includes an embedded Tomcat server, which offers a simple approach to serving web content. See [Building an Application with Spring Boot](http://spring.io/guides/gs/spring-boot/) for more information about installing and using the CLI.

In order to serve static content from Spring Boot’s embedded Tomcat server, you’ll also need to create a minimal amount of web application code so that Spring Boot knows to start Tomcat. The following `app.groovy` script is sufficient for letting Spring Boot know that you want to run Tomcat:

`app.groovy`

```groovy
@Controller class JsApp { }
```

You can now run the app using the Spring Boot CLI:

```
spring run app.groovy
```

Once the app starts, open http://localhost:8080 in your browser, where you see:

![](http://spring.io/guides/gs/consuming-rest-restjs/images/hello.png)

The ID value will increment each time you refresh the page.

## Summary
Congratulations! You’ve just developed a rest.js client that consumes a Spring-based RESTful web service.

## See Also
The following guides may also be helpful:

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