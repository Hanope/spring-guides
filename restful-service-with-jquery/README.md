## Consuming a RESTful Web Service with jQuery
This guide walks you through writing a simple jQuery client that consumes a Spring MVC-based [RESTful web service](http://spring.io/understanding/REST).

## What you will build
You will build a jQuery client that consumes a Spring-based RESTful web service. Specifically, the client will consume the service created in [Building a RESTful Web Service with CORS](http://spring.io/guides/gs/rest-service-cors/).

The jQuery client will be accessed by opening the `index.html` file in your browser, and will consume the service accepting requests at:

```
http://rest-service.guides.spring.io/greeting
```

The service will respond with a [JSON](http://spring.io/understanding/JSON) representation of a greeting:

```json
{"id":1,"content":"Hello, World!"}
```

The jQuery client will render the ID and content into the DOM.

## What you will need
* About 15 minutes
* A favorite text editor
* A modern web browser
* An internet connection

## Create a jQuery Controller
First, you will create the jQuery controller module that will consume the REST service:

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

This controller module is represented as a simple JavaScript function. It uses jQuery’s `$.ajax()` method to consume the REST service at http://rest-service.guides.spring.io/greeting. If successful, it will assign the JSON received to `data`, effectively making it a `Greeting` model object. The `id` and `content` are then appended to the `greeting-id` and `greeting-content` DOM elements respectively.

Note the use of the jQuery promise `.then()`. This directs jQuery to execute the anonymous function when the `$.ajax()` method completes, passing the `data` result from the completed AJAX request.

## Create the Application Page
Now that you have a jQuery controller, you will create the HTML page that will load the client into the user’s web browser:

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

Note the following two script tags within the `<head>` section.

```html
<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js"></script>
<script src="hello.js"></script>
```

The first script tag loads the minified jQuery library (jquery.min.js) from a content delivery network (CDN) so that you don’t have to download jQuery and place it in your project. It also loads the controller code (hello.js) from the application’s path.

Also note that the `<p>` tags include `class` attributes.

```html
<p class="greeting-id">The ID is </p>
<p class="greeting-content">The content is </p>
```

These `class` attributes help jQuery to reference the HTML elements and update the text with the values from the `id` and `content` properties of the JSON received from the REST service.

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

![](http://spring.io/guides/gs/consuming-rest-jquery/images/hello.png)

The ID value will increment each time you refresh the page.

## Summary
Congratulations! You’ve just developed a jQuery client that consumes a Spring-based RESTful web service.

## See Also
The following guides may also be helpful:

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