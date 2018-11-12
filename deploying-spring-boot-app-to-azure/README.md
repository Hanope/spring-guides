## Deploying a Spring Boot app to Azure
This article walks you through deploying an application to Azure.

## What you’ll build
You’ll clone a sample Spring Boot application from GitHub and then use Maven to deploy it to Azure.

## What you’ll need
The following prerequisites are required in order to follow the steps in this article:

* An Azure subscription. If you don’t already have an Azure subscription, you can sign up for a [free Azure account](https://azure.microsoft.com/pricing/free-trial/) or activate your [MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).
* The [Azure Command-Line Interface (CLI)](http://docs.microsoft.com/cli/azure/overview).
* An up-to-date [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/), version 1.8 or later.
* A [Git](https://github.com/) client.

## Create a sample Spring Boot web app
In this section, you will clone an already written Spring Boot application and test it locally:

1. Open a terminal window.

2. Create a local directory to hold your Spring Boot application by typing `mkdir SpringBoot`

3. Change to that directory by typing `cd SpringBoot`.

4. Clone the [Spring Boot Getting Started](https://github.com/microsoft/gs-spring-boot) sample project into the directory you created by typing `git clone https://github.com/microsoft/gs-spring-boot`

5. Change to the directory of the completed project by typing `cd gs-spring-boot/complete`

6. Build the JAR file using Maven by typing `./mvnw clean package`

7. When the web app has been created, start it by typing `./mvnw spring-boot:run`

8. Test it locally by either visiting http://localhost:8080 or typing `curl http://localhost:8080` from another terminal window.

9. You should see the following message displayed: **Greetings from Spring Boot!**

## Create an Azure service principal
In this section, you will create an [Azure service principal](https://cmatskas.com/service-principals-in-microsoft-azure/) that the Maven plugin uses when deploying your web app to Azure.

1. Open a terminal window.

2. Sign into your Azure account with the Azure CLI by typing `az login`

3. Create an Azure service principal by typing `az ad sp create-for-rbac --name "uuuuuuuu" --password "pppppppp" `(`uuuuuuuu` is the user name and `pppppppp` is the password for the service principal).

    Azure should print out a JSON response resembling this:

    ```json
    {
        "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "displayName": "uuuuuuuu",
        "name": "http://uuuuuuuu",
        "password": "pppppppp",
        "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
    }
    ```

> Note the values as they are to be used further down.

## Configure Maven to use your Azure service principal
In this section, you will configure Maven to authenticate using your Azure service principal for web app deployment.

1. Open your Maven `settings.xml` file in a text editor (usually found at either `/etc/maven/settings.xml` or `$HOME/.m2/settings.xml`).

2. Add your Azure service principal settings from the previous section of this tutorial to the `<servers>` collection in the **settings.xml** file as shown below:

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

3. Save and close the **settings.xml** file.

> See [Maven Plugin for Azure Web Apps](https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin) for documentation.

## Build and deploy your app to Azure
Once you have configured all of the settings in the preceding sections, you are ready to deploy your web app to Azure.

From the terminal window, deploy your web app to Azure with Maven by typing `./mvnw azure-webapp:deploy`. (Maven will deploy your web app to Azure using a plugin already in the build file of the sample project you cloned earlier. If the web app doesn’t already exist, it will be created.)

When your web app has been deployed, visit the [Azure portal](https://portal.azure.com/) to manage it. It will be listed in **App Services** as show below:

![](http://spring.io/guides/gs/spring-boot-for-azure/AP01.png)
Click on the application. From there, the publicly-facing URL for your web app will be listed in the **Overview** section:

![](http://spring.io/guides/gs/spring-boot-for-azure/AP02.png)
You can click on this link to visit the Spring Boot application and interact with it.

## Summary
Congratulations! You built and deployed a Spring Boot app to Azure.

## See also
Additional information about using Spring with Azure is available here:

* [Spring on Azure](https://docs.microsoft.com/java/azure/spring-framework/)
* [Deploying a Spring Boot app to the cloud using the Maven Plugin for Azure Web Apps](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin)