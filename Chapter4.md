# Dependency Injection

Reference: (https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html)

### What Is Dependency Injection?

Dependency injection is a pattern we can use to implement IoC, where the control being inverted is setting an object’s dependencies.

Connecting objects with other objects, or “injecting” objects into other objects, is done by an assembler rather than by the objects themselves.

Here’s how we would create an object dependency in traditional programming:

```java
public class Store {
    private Item item;
 
    public Store() {
        item = new ItemImpl1();    
    }
}
```

In the example above, we need to instantiate an implementation of the Item interface within the Store class itself.

By using DI, we can rewrite the example without specifying the implementation of the Item that we want:

```java
public class Store {
    private Item item;
    public Store(Item item) {
        this.item = item;
    }
}
```

Dependency Injection in Spring can be done through constructors, setters or fields.

### Constructor-Based Dependency Injection

Reference: (https://www.baeldung.com/constructor-injection-in-spring)

In the case of constructor-based dependency injection, the container will invoke a constructor with arguments each representing a dependency we want to set.

Spring resolves each argument primarily by type, followed by name of the attribute, and index for disambiguation. Let’s see the configuration of a bean and its dependencies using annotations:

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan("com.example.di")
public class Config {

	@Bean
	public Engine engine() {
		return new Engine("v8", 5);
	}

	@Bean
	public Transmission transmission() {
		return new Transmission("sliding");
	}
}
```

Here we’re using annotations to notify Spring runtime that this class provides bean definitions (@Bean annotation), and that the package com.example.di needs to perform a context scan for additional beans.

- The @Configuration annotation indicates that the class is a source of bean definitions. We can also add it to multiple configuration classes.
- We use the @Bean annotation on a method to define a bean. If we don’t specify a custom name, then the bean name will default to the method name.
- For a bean with the default singleton scope, Spring first checks if a cached instance of the bean already exists, and only creates a new one if it doesn’t. If we’re using the prototype scope, the container returns a new bean instance for each method call.

Next, we define a Car class.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class Car {
	private final Engine engine;
	private final Transmission transmission;

	@Autowired
	public Car(Engine engine, Transmission transmission) {
		this.engine = engine;
		this.transmission = transmission;
	}

	@Override
	public String toString() {
		return String.format("Engine: %s Transmission: %s", engine, transmission);
	}
}
```

Here are the definitions of Engine and Transmission.

```java
public class Engine {
	private final String type;
	private final int volume;

	public Engine(String type, int volume) {
		this.type = type;
		this.volume = volume;
		System.out.println("Engine constructor");
	}

	@Override
	public String toString() {
		return String.format("%s %d", type, volume);
	}
}
```

```java
public class Transmission {
	private final String type;

	public Transmission(String type) {
		this.type = type;
		System.out.println("Transmission constructor");
	}

	@Override
	public String toString() {
		return String.format("%s", type);
	}
}
```

Finally, we need to bootstrap an ApplicationContext using our POJO configuration.

```java
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

@SpringBootApplication
public class AppRunner {

	public static void main(String[] args) {
		ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);

		Car car = context.getBean(Car.class);
		System.out.println(car);
	}
}
```

Run the application, verify that the output similar to below.

```java
Engine constructor
Transmission constructor
Engine: v8 5 Transmission: sliding
```

### Setter-Based Dependency Injection

Reference: (https://www.javaguides.net/2023/01/spring-boot-setter-injection-example.html)

For setter-based DI, the container will call setter methods of our class after invoking a no-argument constructor or no-argument static factory method to instantiate the bean. 

In order to demonstrate the usage of setter injection, let's create a few interfaces and classes.

```java
package com.example.di.setter;

public interface MessageService {
	void sendMessage(String message);
}
```

```java
package com.example.di.setter;

import org.springframework.stereotype.Component;

@Component
public class EmailService implements MessageService {

	@Override
	public void sendMessage(String message) {
		System.out.println(message);
	}

}
```

We have annotated EmailService class with @Component annotation so the Spring container automatically creates a Spring bean and manages its life cycle.

```java
package com.example.di.setter;

import org.springframework.stereotype.Component;

@Component("smsService")
public class SMSService implements MessageService {

	@Override
	public void sendMessage(String message) {
		 System.out.println(message);
	}

}
```

We have annotated SMSService class with @Component annotation so the Spring container automatically creates a Spring bean and manages its life cycle.

In setter injection, Spring will find the @Autowired annotation and call the setter to inject the dependency.

```java
package com.example.di.setter;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class MessageSender {

	private MessageService messageService;

	@Autowired
	public void setMessageService(@Qualifier("emailService") MessageService messageService) {
		this.messageService = messageService;
		System.out.println("setter based dependency injection 1");
	}

	public void sendMessage(String message) {
		this.messageService.sendMessage(message);
	}
}
```

@Qualifier annotation is used in conjunction with Autowired to avoid confusion when we have two or more beans configured for the same type.

```java
package com.example.di.setter;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = "com.example.di.setter")
public class MessageConfig {

}
```

@Configuration: Used to indicate that a configuration class declares one or more @Bean methods. These classes are processed by the Spring container to generate bean definitions and service requests for those beans at runtime.

@ComponentScan: This annotation is used to specify the base packages to scan for spring beans/components.

Let's create ApplicationContext and test this example.

```java
package com.example.di.setter;

import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

@SpringBootApplication
public class ClientApp {

	public static void main(String[] args) {
		String message = "Hi, good morning have a nice day!.";
		ApplicationContext applicationContext = new AnnotationConfigApplicationContext(MessageConfig.class);

		MessageSender messageSender = applicationContext.getBean(MessageSender.class);
		messageSender.sendMessage(message);
	}
}
```

Run the application and verify the output is similar to the one below.

```java
setter based dependency injection 1
Hi, good morning have a nice day!.
```

To perform multiple dependencies using setter injection, update MessageSender with below codes.

```java
package com.example.di.setter;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class MessageSender {

	private MessageService messageService;
	private MessageService smsService;

	@Autowired
	public void setMessageService(@Qualifier("emailService") MessageService messageService) {
		this.messageService = messageService;
		System.out.println("setter based dependency injection 1");
	}

	@Autowired
	public void setSmsService(MessageService smsService) {
		this.smsService = smsService;
		System.out.println("setter based dependency injection 2");
	}

	public void sendMessage(String message) {
		this.messageService.sendMessage(message);
		this.smsService.sendMessage(message);
	}
}
```

Rerun the application and verify the output.

```java
setter based dependency injection 1
setter based dependency injection 2
Hi, good morning have a nice day!.
Hi, good morning have a nice day!.
```

We can combine constructor-based and setter-based types of injection for the same bean. The Spring documentation recommends using constructor-based injection for mandatory dependencies, and setter-based injection for optional ones.

### Field-Based Dependency Injection

Reference: (https://www.javacodegeeks.com/2019/02/spring-field-dependency-injection-example.html)

In case of Field-Based DI, we can inject the dependencies by marking them with an @Autowired annotation:

Let's start by creating a component/service to be injected.

```java
package com.example.di.field;

import org.springframework.stereotype.Component;

@Component
public class GreetingService {

	public GreetingService() {
		System.out.println("GreetingService constructor called...");
	}

	public String greet() {
		return "Hello from GreetingService!";
	}
}
```

Next, create a controller component where the service will be injected.

```java
package com.example.di.field;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/greet")
public class GreetingController {

	@Autowired
	GreetingService gs;

	@GetMapping
	public String testgreeting() {
		return gs.greet();
	}
}
```

Don't forget the configuration and application classes.

```java
package com.example.di.field;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan("com.example.di.field")
public class GreetConfig {

}
```

```java
package com.example.di.field;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class GreetingApp {

	public static void main(String[] args) {
		SpringApplication.run(GreetingApp.class, args);
	}
}
```

Run the application. Test it in browser by typing (http://localhost:8080/greet)

## Using @Autowired

Reference: (https://docs.spring.io/spring-framework/reference/core/beans/annotation-config/autowired.html)

You can apply the @Autowired annotation to constructors, as the following example shows:

```java
public class MovieRecommender {

	private final CustomerPreferenceDao customerPreferenceDao;

	@Autowired
	public MovieRecommender(CustomerPreferenceDao customerPreferenceDao) {
		this.customerPreferenceDao = customerPreferenceDao;
	}

	// ...
}
```

You can also apply the @Autowired annotation to traditional setter methods, as the following example shows:

```java
public class SimpleMovieLister {

	private MovieFinder movieFinder;

	@Autowired
	public void setMovieFinder(MovieFinder movieFinder) {
		this.movieFinder = movieFinder;
	}

	// ...
}
```

You can also apply the annotation to methods with arbitrary names and multiple arguments, as the following example shows:

```java
public class MovieRecommender {

	private MovieCatalog movieCatalog;

	private CustomerPreferenceDao customerPreferenceDao;

	@Autowired
	public void prepare(MovieCatalog movieCatalog,
			CustomerPreferenceDao customerPreferenceDao) {
		this.movieCatalog = movieCatalog;
		this.customerPreferenceDao = customerPreferenceDao;
	}

	// ...
}
```

You can apply @Autowired to fields as well and even mix it with constructors, as the following example shows:

```java
public class MovieRecommender {

	private final CustomerPreferenceDao customerPreferenceDao;

	@Autowired
	private MovieCatalog movieCatalog;

	@Autowired
	public MovieRecommender(CustomerPreferenceDao customerPreferenceDao) {
		this.customerPreferenceDao = customerPreferenceDao;
	}

	// ...
}
```

By default, autowiring fails when no matching candidate beans are available for a given injection point. In the case of a declared array, collection, or map, at least one matching element is expected.

## Using @PostConstruct and @PreDestroy

Reference: (https://docs.spring.io/spring-framework/reference/core/beans/annotation-config/postconstruct-and-predestroy-annotations.html)

Introduced in Spring 2.5, the support for these annotations offers an alternative to the lifecycle callback mechanism described in initialization callbacks and destruction callbacks. Provided that the CommonAnnotationBeanPostProcessor is registered within the Spring ApplicationContext, a method carrying one of these annotations is invoked at the same point in the lifecycle as the corresponding Spring lifecycle interface method or explicitly declared callback method. In the following example, the cache is pre-populated upon initialization and cleared upon destruction:

```java
package com.example.di.setter;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

import jakarta.annotation.PostConstruct;
import jakarta.annotation.PreDestroy;

@Component
public class MessageSender {

	private MessageService messageService;
	private MessageService smsService;

	@Autowired
	public void setMessageService(@Qualifier("emailService") MessageService messageService) {
		this.messageService = messageService;
		System.out.println("setter based dependency injection 1");
	}

	@Autowired
	public void setSmsService(MessageService smsService) {
		this.smsService = smsService;
		System.out.println("setter based dependency injection 2");
	}

	public void sendMessage(String message) {
		this.messageService.sendMessage(message);
		this.smsService.sendMessage(message);
	}
	
	@PostConstruct
	public void first() {
		System.out.println("PostConstruct first() invoked...");
	}

	@PreDestroy
	public void second() {
		System.out.println("PreDestroy second invoked...");
	}
}
```

Update the application class to below codes.

```java
package com.example.di.setter;

import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

@SpringBootApplication
public class ClientApp {

	public static void main(String[] args) {
		String message = "Hi, good morning have a nice day!.";
		AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(MessageConfig.class);

		MessageSender messageSender = applicationContext.getBean(MessageSender.class);
		messageSender.sendMessage(message);
		
		// closing the context will call destroy()
		applicationContext.close();
	}
}
```

Rerun the application and verify the output.

```java
setter based dependency injection 1
setter based dependency injection 2
PostConstruct first() invoked...
Hi, good morning have a nice day!.
Hi, good morning have a nice day!.
PreDestroy second invoked...
```

## Spring Expression Language (SpEL)

Reference: (https://docs.spring.io/spring-framework/reference/core/expressions.html)

The Spring Expression Language ("SpEL" for short) is a powerful expression language that supports querying and manipulating an object graph at runtime. The language syntax is similar to the Jakarta Expression Language but offers additional features, most notably method invocation and basic string templating functionality.

The following code demonstrates how to use the SpEL API to evaluate the literal string expression, Hello World.

```java
package com.example.spel;

import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.expression.Expression;
import org.springframework.expression.ExpressionParser;
import org.springframework.expression.spel.standard.SpelExpressionParser;

@SpringBootApplication
public class SpelApp {

	public static void main(String[] args) {
		ExpressionParser parser = new SpelExpressionParser();
		Expression exp = parser.parseExpression("'Hello World'");
		String message = (String) exp.getValue();
		System.out.println(message);
	}

}
```
> The value of the message variable is "Hello World".

The SpEL classes and interfaces you are most likely to use are located in the org.springframework.expression package and its sub-packages, such as spel.support.

- The ExpressionParser interface is responsible for parsing an expression string.
- In the preceding example, the expression string is a string literal denoted by the surrounding single quotation marks.
- The Expression interface is responsible for evaluating the defined expression string.
- The two types of exceptions that can be thrown when calling parser.parseExpression(…​) and exp.getValue(…​) are ParseException and EvaluationException, respectively.

In the following method invocation example, we call the concat method on the string literal, Hello World.

```java
package com.example.spel;

import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.expression.Expression;
import org.springframework.expression.ExpressionParser;
import org.springframework.expression.spel.standard.SpelExpressionParser;

@SpringBootApplication
public class SpelApp {

	public static void main(String[] args) {
		ExpressionParser parser = new SpelExpressionParser();
		Expression exp = parser.parseExpression("'Hello World'.concat('!')");
		String message = (String) exp.getValue();
		System.out.println(message);
	}

}
```
> 	The value of message is now "Hello World!".

The following example demonstrates how to access the Bytes JavaBean property of the string literal, Hello World.

```java
ExpressionParser parser = new SpelExpressionParser();

// invokes 'getBytes()'
Expression exp = parser.parseExpression("'Hello World'.bytes");
byte[] bytes = (byte[]) exp.getValue();
System.out.println(bytes);
```
> This line converts the literal to a byte array.

SpEL also supports nested properties by using the standard dot notation (such as prop1.prop2.prop3) as well as the corresponding setting of property values. Public fields may also be accessed.

The following example shows how to use dot notation to get the length of a string literal.

```java
ExpressionParser parser = new SpelExpressionParser();

// invokes 'getBytes().length'
Expression exp = parser.parseExpression("'Hello World'.bytes.length");
int length = (Integer) exp.getValue();
System.out.println(length);
```
> 'Hello World'.bytes.length gives the length of the literal -> 11

The String’s constructor can be called instead of using a string literal, as the following example shows.

```java
ExpressionParser parser = new SpelExpressionParser();
Expression exp = parser.parseExpression("new String('hello world').toUpperCase()");
String message = exp.getValue(String.class);
System.out.println(message);
```
> Construct a new String from the literal and convert it to upper case -> HELLO WORLD

The more common usage of SpEL is to provide an expression string that is evaluated against a specific object instance (called the root object). The following example shows how to retrieve the name property from an instance of the Inventor class and how to reference the name property in a boolean expression.

```java
package com.example.spel;

import java.util.Date;
import java.util.GregorianCalendar;

import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.expression.Expression;
import org.springframework.expression.ExpressionParser;
import org.springframework.expression.spel.standard.SpelExpressionParser;

@SpringBootApplication
public class SpelObjectApp {

	public static void main(String[] args) {
		// Create and set a calendar
		GregorianCalendar c = new GregorianCalendar();
		c.set(1856, 7, 9);

		// The constructor arguments are name, birthday, and nationality.
		Inventor tesla = new Inventor("Nikola Tesla", c.getTime(), "Serbian");

		ExpressionParser parser = new SpelExpressionParser();

		Expression exp = parser.parseExpression("name"); // Parse name as an expression
		String name = (String) exp.getValue(tesla);
		System.out.println(name);

		exp = parser.parseExpression("name == 'Nikola Tesla'");
		boolean result = exp.getValue(tesla, Boolean.class);
		System.out.println(result);
	}

}
```

And here's the class Inventor.java

```java
class Inventor {
	private String name;
	private Date birthdate;
	private String nationality;

	public Inventor(String name, Date birthdate, String nationality) {
		this.name = name;
		this.birthdate = birthdate;
		this.birthdate = birthdate;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public Date getBirthdate() {
		return birthdate;
	}

	public void setBirthdate(Date birthdate) {
		this.birthdate = birthdate;
	}

	public String getNationality() {
		return nationality;
	}

	public void setNationality(String nationality) {
		this.nationality = nationality;
	}
}
```
> Nikola Tesla<br/>
> true

## Expressions in Bean Definitions

You can use SpEL expressions with XML-based or annotation-based configuration metadata for defining BeanDefinition instances. In both cases, the syntax to define the expression is of the form #{ <expression string> }.

### XML Configuration

To continue with next example, add a beans.xml file to your project. Right-click **src/main/resources** folder > New > Other > type 'XML' in the filter box > select 'XML File' then click 'Next' > Enter file  name: beans.xml > click Finish

Once created, copy codes below and paste it into beans.xml file.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

	<bean id="student" class="com.example.spel.Student">
		<property name="name" value="Test"></property>
	</bean>
</beans>
```

A property or constructor argument value can be set by using expressions, as you can see above:

Next, create a Java class named Student.java with below codes.

```java
package com.example.spel;

public class Student {
	private String name;

	public Student() {

	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}
}
```

In order for the xml configurations to work, you need to import it into the configuration class so spring container can locate it.

```java
package com.example.spel;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.ImportResource;

@Configuration
@ImportResource("classpath:beans.xml")
public class SpringBootXmlApplication implements CommandLineRunner {

	@Bean(name = "student")
	public Student getStudent() {
		return new Student();
	}

	public static void main(String[] args) {
		SpringApplication.run(SpringBootXmlApplication.class, args);
	}

	@Override
	public void run(String... args) throws Exception {
		
	}
}
```

Now we can test it. Create the application class as below.

```java
package com.example.spel;

import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

@SpringBootApplication
public class SpelBeanApp {

	public static void main(String[] args) {
		ApplicationContext context = new AnnotationConfigApplicationContext(SpringBootXmlApplication.class);

		Student student = context.getBean(Student.class);
		System.out.println(student.getName());
	}

}
```
> Test

All beans in the application context are available as predefined variables with their common bean name. This includes standard context beans such as environment (of type org.springframework.core.env.Environment) as well as systemProperties and systemEnvironment (of type Map<String, Object>) for access to the runtime environment.

The following example shows access to the systemProperties bean as a SpEL variable:

```xml
<bean id="taxCalculator" class="com.example.spel.TaxCalculator">
	<property name="defaultLocale"	value="#{ systemProperties['user.country'] }" />
</bean>
```

Note that you do not have to prefix the predefined variable with the # symbol here.

Add this codes to SpelBeanApp.java to test it.

```java
TaxCalculator taxCalc = context.getBean(TaxCalculator.class);
System.out.println(taxCalc.getDefaultLocale());
```
> MY

You can also refer to other bean properties by name, as the following example shows:

```xml
<bean id="numberGuess" class="org.spring.samples.NumberGuess">
	<property name="randomNumber" value="#{ T(java.lang.Math).random() * 100.0 }"/>

	<!-- other properties -->
</bean>

<bean id="shapeGuess" class="org.spring.samples.ShapeGuess">
	<property name="initialShapeSeed" value="#{ numberGuess.randomNumber }"/>

	<!-- other properties -->
</bean>
```

### Annotation Configuration
To specify a default value, you can place the @Value annotation on fields, methods, and method or constructor parameters.

The following example sets the default value of a field:

```java


