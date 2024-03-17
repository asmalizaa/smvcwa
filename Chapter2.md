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

## Inversion of Control

## XML View: Declaring Beans

## Java View: Using Beans

## Singletons and Prototypes
