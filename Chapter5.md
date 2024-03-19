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

```
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
