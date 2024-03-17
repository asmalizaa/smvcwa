# The Container

## JavaBeans, Reconsidered

JavaBeans are basically POJOs (Plain Old Java Object), defined according to the norms based on the software component model. 

JavaBeans are classes that encapsulates many objects into a single object (the bean). It is a Java class that should adhere to these following conventions:
- Must implement Serializable interface.
- It should have a public no-argument constructor.
- All properties in the class must be private with public getter and setter methods.

A simple example of JavaBean Class is mentioned below:

```java
// Java program to illustrate the structure of JavaBean class
public class TestBean {
	private String name;

	public void setName(String name) { 
	    this.name = name; 
	}

	public String getName() { return name; }
}
```

### Setter and Getter Methods in Java

Properties for setter methods:
- It should be public in nature.
- The return type a should be void.
- The setter method should be prefixed with the set.
- It should take some argument i.e. it should not be a no-arg method.

Properties for getter methods:
- It should be public in nature.
- The return type should not be void i.e. according to our requirement, return type we have to give the return type.
- The getter method should be prefixed with get.
- It should not take any argument.

For Boolean properties getter method name can be prefixed with either "get" or "is". But recommended to use "is".

```java
// Java program to illustrate the getName() method on boolean type attribute
public class Test {
	private boolean empty;

	public boolean getEmpty(){ 
	    return empty; 
	}
	
	public boolean isempty(){ 
	    return empty; 
	}
}
```

Another example of a JavaBean.

```java
// Java Program of JavaBean class
public class Student implements java.io.Serializable {
	private int id;
	private String name;
	
	// Constructor
	public Student() {}

	// Setter for Id
	public void setId(int id) { this.id = id; }

	// Getter for Id
	public int getId() { return id; }
	
	// Setter for Name
	public void setName(String name) { this.name = name; }

	// Getter for Name
	public String getName() { return name; }
}
```

Code below is an example of the usage of a JavaBean.

```java
// Java program to access JavaBean class

// Driver Class
public class Test {
	// main function
	public static void main(String args[])
	{
		// object is created
		Student s = new Student();
		
		// setting value to the object
		s.setName("John"); 

		System.out.println(s.getName());
	}
}
```

## The Factory Pattern

Let’s delve into the Factory Design Pattern in Java. This pattern falls under the category of creational design patterns and is widely used in various contexts, including frameworks like Spring and Struts.

**Overview**

The Factory Design Pattern aims to create objects without specifying the exact class of the object that will be instantiated. Instead, it delegates the responsibility of creating objects to subclasses. This approach promotes loose coupling and adheres to principles like the **Single Responsibility Principle (SRP)** and the **Open-Closed Principle (OCP)**.

**Factory Method Pattern**

Let’s illustrate this pattern with an example. Imagine we’re developing an application for a vehicle manufacturer. Initially, we only deal with vehicles powered by fuel-only engines. To maintain SRP and OCP, we’ll use the factory method design pattern. Here’s a simplified UML diagram for this pattern: !Factory Method UML Diagram Key concepts related to this pattern:

- The MotorVehicle interface defines a method called build(), responsible for constructing a specific motor vehicle.
- Concrete classes like Motorcycle and Car implement this interface and provide their own build() implementations.

Let’s look at some code snippets:

```java
// MotorVehicle interface
public interface MotorVehicle {
    void build();
}

// Concrete class: Motorcycle
public class Motorcycle implements MotorVehicle {
    @Override
    public void build() {
        System.out.println("Build Motorcycle");
    }
}

// Concrete class: Car
public class Car implements MotorVehicle {
    @Override
    public void build() {
        System.out.println("Build Car");
    }
}

// MotorVehicleFactory (abstract class)
public abstract class MotorVehicleFactory {
    public MotorVehicle create() {
        MotorVehicle vehicle = createMotorVehicle();
        vehicle.build();
        return vehicle;
    }

    protected abstract MotorVehicle createMotorVehicle();
}

// Specific factory: MotorcycleFactory
public class MotorcycleFactory extends MotorVehicleFactory {
    @Override
    protected MotorVehicle createMotorVehicle() {
        return new Motorcycle();
    }
}

// Specific factory: CarFactory
public class CarFactory extends MotorVehicleFactory {
    @Override
    protected MotorVehicle createMotorVehicle() {
        return new Car();
    }
}
```

In this example, the MotorcycleFactory and CarFactory extend the MotorVehicleFactory base class to create specific types of motor vehicles. New vehicle types can be easily added without affecting existing code.

## Inversion of Control

Inversion of Control (IoC) is a design pattern in software engineering that fundamentally changes how control flows within an application. Let’s explore this concept.

**Traditional Programming vs. Inversion of Control**

- In traditional programming, the main function of an application directly calls specific libraries or functions to perform tasks. For example, an email client might display a menu of commands (like loading new mail, replying, or composing) and wait for user input to execute the chosen command.
- In contrast, IoC flips this approach. Instead of the custom code calling reusable libraries, it’s the framework that orchestrates the flow of control. The framework provides common behavioral elements (like windowing systems, menus, mouse control, etc.), and the custom code fills in the specifics. When a user interacts with the interface, the framework invokes the appropriate custom code.

**Key Points about IoC**

- **Decoupling**: IoC promotes loose coupling between components. Custom-written portions of the program don’t directly depend on specific libraries or services. Instead, they rely on abstractions provided by the framework.
- **Extensibility**: IoC makes the framework extensible. Application programmers define methods or behaviors, and the framework integrates them.
- **Event-Driven Programming**: IoC is commonly used in event-driven programming. The custom code handles events, while the framework manages event loops and dispatch.
- **Dependency Injection (DI)**: In the Java community, the term “IoC” is often associated with DI. DI involves injecting dependencies (objects or services) into application components rather than having the components create their own dependencies.

**Example**

- Imagine building a graphical application. Instead of writing code to handle mouse clicks, window creation, and menu interactions directly, you use a framework (like JavaFX or Swing). The framework provides the infrastructure, and your custom code specifies how to respond to events.
- In this scenario, the framework controls the execution flow, and your code focuses on the specific behavior.

## XML View: Declaring Beans

Before Spring 3.0, XML was the only way to define and configure beans. Spring 3.0 introduced JavaConfig, allowing us to configure beans using Java classes. However, XML configuration files are still used today.

**The @ImportResource Annotation**

The @ImportResource annotation allows us to import one or more resources containing bean definitions.

Let’s say we have a beans.xml file with the definition of a bean:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    
    <bean class="com.baeldung.springbootxml.Pojo">
        <property name="field" value="sample-value"></property>
    </bean>
</beans>
```

To use it in a Spring Boot application, we can use the @ImportResource annotation, telling it where to find the configuration file:

```java
@Configuration
@ImportResource("classpath:beans.xml")
public class SpringBootXmlApplication implements CommandLineRunner {

    @Autowired 
    private Pojo pojo;

    public static void main(String[] args) {
        SpringApplication.run(SpringBootXmlApplication.class, args);
    }
}
```

In this case, the Pojo instance will be injected with the bean defined in beans.xml.

## Java View: Using Beans

@Bean is a method-level annotation and a direct analog of the XML <bean/> element. The annotation supports some of the attributes offered by <bean/>, such as:

- init-method
- destroy-methohd
- autowiring
- name

You can use the @Bean annotation in a @Configuration-annotated or in a @Component-annotated class.

**Declaring a Bean**

To declare a bean, you can annotate a method with the @Bean annotation. You use this method to register a bean definition within an ApplicationContext of the type specified as the method’s return value. By default, the bean name is the same as the method name. 

The following example shows a @Bean method declaration:

```java
@Configuration
public class AppConfig {

	@Bean
	public TransferServiceImpl transferService() {
		return new TransferServiceImpl();
	}
}
```

The preceding configuration is exactly equivalent to the following Spring XML:

```xml
<beans>
	<bean id="transferService" class="com.acme.TransferServiceImpl"/>
</beans>
```

Both declarations make a bean named transferService available in the ApplicationContext, bound to an object instance of type TransferServiceImpl, as the following text image shows:

> transferService -> com.acme.TransferServiceImpl

You can also use default methods to define beans. This allows composition of bean configurations by implementing interfaces with bean definitions on default methods.

```java
public interface BaseConfig {

	@Bean
	default TransferServiceImpl transferService() {
		return new TransferServiceImpl();
	}
}

@Configuration
public class AppConfig implements BaseConfig {

}
```

You can also declare your @Bean method with an interface (or base class) return type, as the following example shows:

```java
@Configuration
public class AppConfig {

	@Bean
	public TransferService transferService() {
		return new TransferServiceImpl();
	}
}
```

However, this limits the visibility for advance type prediction to the specified interface type (TransferService). Then, with the full type (TransferServiceImpl) known to the container only once the affected singleton bean has been instantiated. Non-lazy singleton beans get instantiated according to their declaration order, so you may see different type matching results depending on when another component tries to match by a non-declared type (such as @Autowired TransferServiceImpl, which resolves only once the transferService bean has been instantiated).

## Singletons and Prototypes
