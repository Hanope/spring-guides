## Creating CRUD UI with Vaadin
This guide walks you through the process of building an application that uses a [Vaadin based UI](https://vaadin.com/) on a Spring Data JPA based backend.

## What you’ll build
You’ll build a Vaadin UI for a simple JPA repository. What you’ll get is an app with full CRUD (Create, Read, Update, Delete) functionality and a filtering example that uses a custom repository method.

You can start from two different parts, either by starting from the "initial" project you have set up or from a fresh start. The differences are discussed below.

## How to complete this guide
Like most Spring [Getting Started guides](http://spring.io/guides), you can start from scratch and complete each step, or you can bypass basic setup steps that are already familiar to you. Either way, you end up with working code.

To **start from scratch**, move on to [Build with Gradle](#build-with-gradle).

To **skip the basics**, do the following:

* [Download](https://github.com/spring-guides/gs-crud-with-vaadin/archive/master.zip) and unzip the source repository for this guide, or clone it using [Git](http://spring.io/understanding/Git): `git clone https://github.com/spring-guides/gs-crud-with-vaadin.git`
* cd into `gs-crud-with-vaadin/initial`
* Jump ahead to [Create the backend services](#create-the-backend-services).

**When you’re finished**, you can check your results against the code in `gs-crud-with-vaadin/complete`.

## Build with Gradle
First you set up a basic build script. You can use any build system you like when building apps with Spring, but the code you need to work with [Gradle](http://gradle.org/) and [Maven](https://maven.apache.org/) is included here. If you’re not familiar with either, refer to [Building Java Projects with Gradle](http://spring.io/guides/gs/gradle) or [Building Java Projects with Maven](http://spring.io/guides/gs/maven).

### Create the directory structure
In a project directory of your choosing, create the following subdirectory structure; for example, with `mkdir -p src/main/java/hello` on *nix systems:

```
└── src
    └── main
        └── java
            └── hello
```

### Create a Gradle build file
Below is the [initial Gradle build file](https://github.com/spring-guides/gs-crud-with-vaadin/blob/master/initial/build.gradle).

`build.gradle`

```gradle
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:2.0.5.RELEASE")
    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

bootJar {
    baseName = 'gs-crud-with-vaadin'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
    maven { url "https://maven.vaadin.com/vaadin-addons" }
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-data-jpa")
    compile("com.h2database:h2")
    testCompile("junit:junit")
}
```

The [Spring Boot gradle plugin](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html) provides many convenient features:

* It collects all the jars on the classpath and builds a single, runnable "über-jar", which makes it more convenient to execute and transport your service.
* It searches for the `public static void main()` method to flag as a runnable class.
* It provides a built-in dependency resolver that sets the version number to match [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml). You can override any version you wish, but it will default to Boot’s chosen set of versions.

## Build with Maven
First you set up a basic build script. You can use any build system you like when building apps with Spring, but the code you need to work with [Maven](https://maven.apache.org/) is included here. If you’re not familiar with Maven, refer to [Building Java Projects with Maven](http://spring.io/guides/gs/maven).

### Create the directory structure
In a project directory of your choosing, create the following subdirectory structure; for example, with `mkdir -p src/main/java/hello` on *nix systems:

```
└── src
    └── main
        └── java
            └── hello
```

`pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-crud-with-vaadin</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.5.RELEASE</version>
    </parent>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

The [Spring Boot Maven plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin) provides many convenient features:

* It collects all the jars on the classpath and builds a single, runnable "über-jar", which makes it more convenient to execute and transport your service.
* It searches for the `public static void main()` method to flag as a runnable class.
* It provides a built-in dependency resolver that sets the version number to match [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-dependencies/pom.xml). You can override any version you wish, but it will default to Boot’s chosen set of versions.

## Build with your IDE
* Read how to import this guide straight into [Spring Tool Suite](http://spring.io/guides/gs/sts/).
* Read how to work with this guide in [IntelliJ IDEA](http://spring.io/guides/gs/intellij-idea).

## Create the backend services
This example is a continuation from [Accessing Data with JPA](http://spring.io/gs/accessing-data-jpa). The only difference is that the entity class has getters and setters and the custom search method in the repository is a bit more graceful for end users. You don’t have to read that guide to walk through this one, but you can if you wish.

If you started with a fresh project, then add the following entity and repository objects and you’re good to go. In case you started with from the "initial" step, these are already available for you.

`src/main/java/hello/Customer.java`

```java
package hello;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
public class Customer {

	@Id
	@GeneratedValue
	private Long id;

	private String firstName;

	private String lastName;

	protected Customer() {
	}

	public Customer(String firstName, String lastName) {
		this.firstName = firstName;
		this.lastName = lastName;
	}

	public Long getId() {
		return id;
	}

	public String getFirstName() {
		return firstName;
	}

	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

	public String getLastName() {
		return lastName;
	}

	public void setLastName(String lastName) {
		this.lastName = lastName;
	}

	@Override
	public String toString() {
		return String.format("Customer[id=%d, firstName='%s', lastName='%s']", id,
				firstName, lastName);
	}

}
```

`src/main/java/hello/CustomerRepository.java`

```java
package hello;

import org.springframework.data.jpa.repository.JpaRepository;

import java.util.List;

public interface CustomerRepository extends JpaRepository<Customer, Long> {

	List<Customer> findByLastNameStartsWithIgnoreCase(String lastName);
}
```

You can leave the Spring Boot based application intact as it will fill our DB with some example data.

`src/main/java/hello/Application.java`

```java
package hello;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class Application {

	private static final Logger log = LoggerFactory.getLogger(Application.class);

	public static void main(String[] args) {
		SpringApplication.run(Application.class);
	}

	@Bean
	public CommandLineRunner loadData(CustomerRepository repository) {
		return (args) -> {
			// save a couple of customers
			repository.save(new Customer("Jack", "Bauer"));
			repository.save(new Customer("Chloe", "O'Brian"));
			repository.save(new Customer("Kim", "Bauer"));
			repository.save(new Customer("David", "Palmer"));
			repository.save(new Customer("Michelle", "Dessler"));

			// fetch all customers
			log.info("Customers found with findAll():");
			log.info("-------------------------------");
			for (Customer customer : repository.findAll()) {
				log.info(customer.toString());
			}
			log.info("");

			// fetch an individual customer by ID
			Customer customer = repository.findById(1L).get();
			log.info("Customer found with findOne(1L):");
			log.info("--------------------------------");
			log.info(customer.toString());
			log.info("");

			// fetch customers by last name
			log.info("Customer found with findByLastNameStartsWithIgnoreCase('Bauer'):");
			log.info("--------------------------------------------");
			for (Customer bauer : repository
					.findByLastNameStartsWithIgnoreCase("Bauer")) {
				log.info(bauer.toString());
			}
			log.info("");
		};
	}

}
```

## Vaadin dependencies
If you checked out the "initial" state project, you have all necessary dependencies already set up, but lets look at what you need to do to add Vaadin support to a fresh Spring project. Vaadin Spring integration contains a Spring boot starter dependency collection, so all you must do is to add this Maven snippet or a similar Gradle configuration:

```xml
<dependency>
    <groupId>com.vaadin</groupId>
    <artifactId>vaadin-spring-boot-starter</artifactId>
</dependency>
```

The example uses a newer version of Vaadin, than the default one brought in by the starter module. To use a newer version, define the Vaadin Bill of Materials (BOM) like this:

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.vaadin</groupId>
            <artifactId>vaadin-bom</artifactId>
            <version>10.0.6</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

Gradle doesn’t support "BOMs" by default, but there is a handy [plugin for that](https://plugins.gradle.org/plugin/io.spring.dependency-management). Check out the [build.gradle build file for an example on how to accomplish the same thing](https://github.com/spring-guides/gs-crud-with-vaadin/blob/master/complete/build.gradle).

## Define the MainView class
The MainView class is the entry point for Vaadin’s UI logic. In Spring Boot applications you just need to annotate it with `@Route` and it will be automatically picked up by Spring and shown at the root of your web app. You can customize the URL where the view is shown by giving a parameter to the Route annotation. A simple "hello world" could look like this:

```java
package hello;

import com.vaadin.flow.component.button.Button;
import com.vaadin.flow.component.notification.Notification;
import com.vaadin.flow.component.orderedlayout.VerticalLayout;
import com.vaadin.flow.router.Route;

@Route
public class MainView extends VerticalLayout {

	public MainView() {
		add(new Button("Click me", e -> Notification.show("Hello Spring+Vaadin user!")));
	}
}
```

## List entities in a data grid
For a nice layout, use the `Grid` component. The list of entities from a constructor-injected `CustomerRepository` can simply be passed to the Grid using the setItems method. The body of your `MainView` would expand like this:

```java
@Route
public class MainView extends VerticalLayout {

	private final CustomerRepository repo;
	final Grid<Customer> grid;

	public MainView(CustomerRepository repo) {
		this.repo = repo;
		this.grid = new Grid<>(Customer.class);
		add(grid);
		listCustomers();
	}

	private void listCustomers() {
		grid.setItems(repo.findAll());
	}

}
```

> If you have large tables and lots of concurrent users, you most likely don’t want to bind the whole dataset to your UI components.

Although Vaadin Grid lazy load the data from the server to the browser, this solution above keeps the whole list of data in the server memory. To save some memory, you could show only the topmost results, use paging or provide a lazy loading data provider using the `setDataProvider(DataProvider)` method.

## Filtering the data
Before the large data set becomes a problem to your server, it will cause a headache for your users trying to find the relevant row he or she wants to edit. Use a `TextField` component to create a filter entry. First, modify the `listCustomer()` method to support filtering:

```java
void listCustomers(String filterText) {
	if (StringUtils.isEmpty(filterText)) {
		grid.setItems(repo.findAll());
	}
	else {
		grid.setItems(repo.findByLastNameStartsWithIgnoreCase(filterText));
	}
}
```

> This is where Spring Data’s declarative queries come in real handy. Writing `findByLastNameStartsWithIgnoringCase` is a single line definition in `CustomerRepository`.

Hook a listener to the `TextField` component and plug its value into that filter method. The `ValueChangeListener` is called automatically during typing as we define the `ValueChangeMode.EAGER` to the filter text field.

```java
TextField filter = new TextField();
filter.setPlaceholder("Filter by last name");
filter.setValueChangeMode(ValueChangeMode.EAGER);
filter.addValueChangeListener(e -> listCustomers(e.getValue()));
add(filter, grid);
```

## Define the editor component
As Vaadin UIs are just plain Java code, there is no excuse to not write re-usable code from the beginning. Define an editor component for your Customer entity. You’ll make it a Spring-managed bean so you can directly inject the `CustomerRepository` to the editor and tackle the C, U, and D parts or our CRUD functionality.

`src/main/java/hello/CustomerEditor.java`

```java
package hello;

import com.vaadin.flow.component.Key;
import com.vaadin.flow.component.KeyNotifier;
import com.vaadin.flow.component.button.Button;
import com.vaadin.flow.component.icon.VaadinIcon;
import com.vaadin.flow.component.orderedlayout.HorizontalLayout;
import com.vaadin.flow.component.orderedlayout.VerticalLayout;
import com.vaadin.flow.component.textfield.TextField;
import com.vaadin.flow.data.binder.Binder;
import com.vaadin.flow.spring.annotation.SpringComponent;
import com.vaadin.flow.spring.annotation.UIScope;
import org.springframework.beans.factory.annotation.Autowired;

/**
 * A simple example to introduce building forms. As your real application is probably much
 * more complicated than this example, you could re-use this form in multiple places. This
 * example component is only used in MainView.
 * <p>
 * In a real world application you'll most likely using a common super class for all your
 * forms - less code, better UX.
 */
@SpringComponent
@UIScope
public class CustomerEditor extends VerticalLayout implements KeyNotifier {

	private final CustomerRepository repository;

	/**
	 * The currently edited customer
	 */
	private Customer customer;

	/* Fields to edit properties in Customer entity */
	TextField firstName = new TextField("First name");
	TextField lastName = new TextField("Last name");

	/* Action buttons */
	// TODO why more code?
	Button save = new Button("Save", VaadinIcon.CHECK.create());
	Button cancel = new Button("Cancel");
	Button delete = new Button("Delete", VaadinIcon.TRASH.create());
	HorizontalLayout actions = new HorizontalLayout(save, cancel, delete);

	Binder<Customer> binder = new Binder<>(Customer.class);
	private ChangeHandler changeHandler;

	@Autowired
	public CustomerEditor(CustomerRepository repository) {
		this.repository = repository;

		add(firstName, lastName, actions);

		// bind using naming convention
		binder.bindInstanceFields(this);

		// Configure and style components
		setSpacing(true);

		save.getElement().getThemeList().add("primary");
		delete.getElement().getThemeList().add("error");

		addKeyPressListener(Key.ENTER, e -> save());

		// wire action buttons to save, delete and reset
		save.addClickListener(e -> save());
		delete.addClickListener(e -> delete());
		cancel.addClickListener(e -> editCustomer(customer));
		setVisible(false);
	}

	void delete() {
		repository.delete(customer);
		changeHandler.onChange();
	}

	void save() {
		repository.save(customer);
		changeHandler.onChange();
	}

	public interface ChangeHandler {
		void onChange();
	}

	public final void editCustomer(Customer c) {
		if (c == null) {
			setVisible(false);
			return;
		}
		final boolean persisted = c.getId() != null;
		if (persisted) {
			// Find fresh entity for editing
			customer = repository.findById(c.getId()).get();
		}
		else {
			customer = c;
		}
		cancel.setVisible(persisted);

		// Bind customer properties to similarly named fields
		// Could also use annotation or "manual binding" or programmatically
		// moving values from fields to entities before saving
		binder.setBean(customer);

		setVisible(true);

		// Focus first name initially
		firstName.focus();
	}

	public void setChangeHandler(ChangeHandler h) {
		// ChangeHandler is notified when either save or delete
		// is clicked
		changeHandler = h;
	}

}
```

In a larger application you could then use this editor component in multiple places. Also note, that in large applications, you might want to apply some common patterns like MVP to structure your UI code (which is outside the scope of this guide).

## Wire the editor
In the previous steps you have already seen some basics of component-based programming. Using a `Button` and selection listener to `Grid`, you can fully integrate our editor to the main view. The final version of the MainView class looks like this:

`src/main/java/hello/MainView.java`

```java
package hello;

import com.vaadin.flow.component.button.Button;
import com.vaadin.flow.component.grid.Grid;
import com.vaadin.flow.component.icon.VaadinIcon;
import com.vaadin.flow.component.orderedlayout.HorizontalLayout;
import com.vaadin.flow.component.orderedlayout.VerticalLayout;
import com.vaadin.flow.component.textfield.TextField;
import com.vaadin.flow.data.value.ValueChangeMode;
import com.vaadin.flow.router.Route;
import com.vaadin.flow.spring.annotation.UIScope;
import org.springframework.util.StringUtils;

@Route
public class MainView extends VerticalLayout {

	private final CustomerRepository repo;

	private final CustomerEditor editor;

	final Grid<Customer> grid;

	final TextField filter;

	private final Button addNewBtn;

	public MainView(CustomerRepository repo, CustomerEditor editor) {
		this.repo = repo;
		this.editor = editor;
		this.grid = new Grid<>(Customer.class);
		this.filter = new TextField();
		this.addNewBtn = new Button("New customer", VaadinIcon.PLUS.create());

		// build layout
		HorizontalLayout actions = new HorizontalLayout(filter, addNewBtn);
		add(actions, grid, editor);

		grid.setHeight("300px");
		grid.setColumns("id", "firstName", "lastName");
		grid.getColumnByKey("id").setWidth("50px").setFlexGrow(0);

		filter.setPlaceholder("Filter by last name");

		// Hook logic to components

		// Replace listing with filtered content when user changes filter
		filter.setValueChangeMode(ValueChangeMode.EAGER);
		filter.addValueChangeListener(e -> listCustomers(e.getValue()));

		// Connect selected Customer to editor or hide if none is selected
		grid.asSingleSelect().addValueChangeListener(e -> {
			editor.editCustomer(e.getValue());
		});

		// Instantiate and edit new Customer the new button is clicked
		addNewBtn.addClickListener(e -> editor.editCustomer(new Customer("", "")));

		// Listen changes made by the editor, refresh data from backend
		editor.setChangeHandler(() -> {
			editor.setVisible(false);
			listCustomers(filter.getValue());
		});

		// Initialize listing
		listCustomers(null);
	}

	// tag::listCustomers[]
	void listCustomers(String filterText) {
		if (StringUtils.isEmpty(filterText)) {
			grid.setItems(repo.findAll());
		}
		else {
			grid.setItems(repo.findByLastNameStartsWithIgnoreCase(filterText));
		}
	}
	// end::listCustomers[]

}
```

## Summary
Congratulations! You’ve written a full featured CRUD UI application using Spring Data JPA for persistence. And you did it without exposing any REST services or having to write a single line of JavaScript or HTML.

## See Also
The following guides may also be helpful:

* [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)
* [Accessing Data with JPA](https://spring.io/guides/gs/accessing-data-jpa/)
* [Accessing Data with MongoDB](https://spring.io/guides/gs/accessing-data-mongodb/)
* [Accessing Data with GemFire](https://spring.io/guides/gs/accessing-data-gemfire/)
* [Accessing Data with Neo4j](https://spring.io/guides/gs/accessing-data-neo4j/)
* [Accessing data with MySQL](https://spring.io/guides/gs/accessing-data-mysql/)