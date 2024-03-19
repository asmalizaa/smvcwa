# Assembling Object Models

Reference: (https://www.baeldung.com/spring-injecting-collections)

In this topic, we’re going to show how to inject Java collections using the Spring framework.

## List with @Autowired

Let’s create an example bean:

```java
package com.example.collectionsdemo;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;

public class CollectionsBean {

    @Autowired
    private List<String> nameList;

    public void printNameList() {
        System.out.println(nameList);
    }
}
```

After that, we register the CollectionsBean in the configuration setup class:

```java
package com.example.collectionsdemo;

import java.util.Arrays;
import java.util.List;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan("com.example.collectionsdemo")
public class CollectionConfig {

	@Bean
	public CollectionsBean getCollectionsBean() {
		return new CollectionsBean();
	}

	@Bean
	public List<String> nameList() {
		return Arrays.asList("John", "Adam", "Harry");
	}
}
```

Create the application class called CollectionsApp.java with codes below.

```java
package com.example.collectionsdemo;

import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

@SpringBootApplication
public class CollectionsdemoApplication {

	public static void main(String[] args) {
		AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(CollectionsConfig.class);
		CollectionsBean collectionsBean = context.getBean(CollectionsBean.class);
		collectionsBean.printNameList();
		context.close();
	}
}
```

Run the application class by right-click > Run As > Java Application.

> [John, Adam, Harry]

## Set With Constructor Injection

To set up the same example with the Set collection, let’s modify the CollectionsBean class by adding codes below:

```java
private Set<String> nameSet;

public CollectionsBean(Set<String> strings) {
	this.nameSet = strings;
}

public void printNameSet() {
	System.out.println(nameSet);
}
```

This time we want to use a constructor injection for initializing the nameSet property. This requires also changes in configuration class:

```java
@Bean
public CollectionsBean getCollectionsBean() {
	return new CollectionsBean(new HashSet<>(Arrays.asList("John", "Adam", "Harry", "David")));
}
```

Update the application class to call method printNameSet() instead.

```java
collectionsBean.printNameSet();
```

Run the application class and verify the output as below.
> [Adam, Harry, John, David]

## Map With Setter Injection

Following the same logic, let’s add the nameMap field to demonstrate the map injection:

```java
private Map<Integer, String> nameMap;

@Autowired
public void setNameMap(Map<Integer, String> nameMap) {
	this.nameMap = nameMap;
}

public void printNameMap() {
	System.out.println(nameMap);
}
```

This time we have a setter method in order to use a setter dependency injection. We also need to add the Map initializing code in configuration class:

```java
@Bean
public Map<Integer, String> nameMap() {
	Map<Integer, String> nameMap = new HashMap<>();
	nameMap.put(1, "John");
	nameMap.put(2, "Adam");
	nameMap.put(3, "Harry");
	nameMap.put(4, "Rocky");
	return nameMap;
}
```

Call printNameMap() method in the application class. Run the application and verify the outout.
> {1=John, 2=Adam, 3=Harry, 4=Rocky}

## Injecting Bean References

Let’s look at an example where we inject bean references as elements of the collection.

First, let’s create the bean:

```java
package com.example.collectionsdemo;

public class MyBean {
	private String name;
	
	public MyBean(String name) {
		setName(name);
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	@Override
	public String toString() {
		return getName();
	}
}
```

And add a List of MyBean as a property to the CollectionsBean class:

```java
@Autowired(required = false)
private List<MyBean> beanList;

public void printBeanList() {
	System.out.println(beanList);
}
```

Next, we add the Java configuration factory methods for each MyBean element:

```java
@Bean
public MyBean getElement() {
	return new MyBean("Sarah");
}

@Bean
public MyBean getAnotherElement() {
	return new MyBean("Rose");
}

@Bean
public MyBean getOneMoreElement() {
	return new MyBean("Lily");
}
```

The Spring container injects the individual beans of the MyBean type into one collection.

To test this, we invoke the collectionsBean.printBeanList() method. The output shows the bean names as list elements:
> [Sarah, Rose, Lily]

## Using @Order to Sort Beans

We can specify the order of the beans while injecting into the collection.

For that purpose, we use the @Order annotation and specify the index:

```java
@Bean
@Order(2)
public MyBean getElement() {
	return new MyBean("Sarah");
}

@Bean
@Order(3)
public MyBean getAnotherElement() {
	return new MyBean("Rose");
}

@Bean
@Order(1)
public MyBean getOneMoreElement() {
	return new MyBean("Lily");
}
```

Spring container first will inject the bean with the name "Lily", as it has the lowest order value.

It will then inject the "Sarah", and finally, the "Rose" bean:
> [Lily, Sarah, Rose]

## Setting an Empty List as a Default Value

We can set the default value for an injected List property as an empty list by using the Collections.emptyList() static method:

```java
@Value("${names.list:}#{T(java.util.Collections).emptyList()}")
private List<String> nameListWithDefaultValue;

public void printNameListWithDefaults() {
	System.out.println(nameListWithDefaultValue);
}
```

If we run this with the "names.list" key not initialized via properties file:

```java
collectionsBean.printNameListWithDefaults();
```
We’ll get an empty list as output:
> []

## Autowiring Generics in Spring 

Spring support generic types injection. Spring lets us use a generic type as a qualifier without the need for an explicit annotation since version 4.0.

To explore it, first let's create an abstract class called Vehicle.java

```java
package com.example.generics;

public abstract class Vehicle {
	private String name;
	private String manufacturer;

	public Vehicle(String name, String manufacturer) {
		this.name = name;
		this.manufacturer = manufacturer;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getManufacturer() {
		return manufacturer;
	}

	public void setManufacturer(String manufacturer) {
		this.manufacturer = manufacturer;
	}

	@Override
	public String toString() {
		return "Name: " + getName() + "\nManufacturer: " + getManufacturer();
	}
}
```

Next, create the subclass called Car.java with below codes.

```java
package com.example.generics;

public class Car extends Vehicle {
	private String engineType;

	public Car(String name, String manufacturer, String engineType) {
		super(name, manufacturer);
		this.engineType = engineType;
	}

	public String getEngineType() {
		return engineType;
	}

	public void setEngineType(String engineType) {
		this.engineType = engineType;
	}

	@Override
	public String toString() {
		return super.toString() + "\nEngine Type: " + getEngineType();
	}
}
```

Create the configuration class. In this class, we will inject a list of vehicles using generic type.

```java
package com.example.generics;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan("com.example.generics")
public class GenericsConfig {
	
	@Autowired
	private List<Car> vehicles;
	
	@Bean
	public Car getMercedes() {
		return new Car("E280", "Mercedes", "Diesel");
	}
}
```

Finally, the application class.

```java
package com.example.generics;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class GenericsApp {

	public static void main(String[] args) {
		AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(GenericsConfig.class);

		Car car = context.getBean(Car.class);
		System.out.println(car);

		context.close();
	}

}
```

Run the application and verify the output.

> Name: E280<br/>
> Manufacturer: Mercedes<br/>
> Engine Type: Diesel
