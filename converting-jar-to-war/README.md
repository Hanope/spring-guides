## Converting a Spring Boot JAR Application to a WAR
Spring Boot comes with two powerful plugins:

* `spring-boot-gradle-plugin`
* `spring-boot-maven-plugin`

They both essentially have feature parity and provide the ability to run Spring Boot apps from the command line as well as bundle up runnable JARs. This subject is mentioned in almost every guide at the execution phase towards the end.

A popular subject is that many people still wish to generate WAR files to be deployed inside containers. Both of these plugins support that as well. Essentially, you have to reconfigure your project to produce a WAR file and declare the embedded container dependencies as "provided". This ensures that the relevant embedded container dependencies aren’t included in the WAR file.

For detailed steps on how to configure your application to create a WAR file for your container, please see:

* [Packaging executable jar and war files with Maven](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#build-tool-plugins-maven-packaging)
* [Spring Boot Gradle Plugin](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#build-tool-plugins-gradle-plugin) or
* [Gradle Plugin Reference: Packaging executable wars](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html/#packaging-executable-wars)

> Spring Boot operates on servlet 3.0 spec containers.

## See Also
The following guide may also be helpful:

* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)