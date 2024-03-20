# Data Binding

Reference: (https://docs.spring.io/spring-framework/reference/core/validation/beans-beans.html#page-title)

In data binding what we have to do is we have to capture or store the data so that we can bind that data with another resource (for example displaying the data in the frontend part) as per our needs or we can also read the data from a variable and display it as per our requirements. In Spring using the data binding concept, we can do the following two tasks:

1. We can read from a variable
2. We can write to a variable

Data binding is useful for binding user input to a target object where user input is a map with property paths as keys, following JavaBeans conventions. DataBinder is the main class that supports this, and it provides two ways to bind user input:

- Constructor binding - bind user input to a public data constructor, looking up constructor argument values in the user input.
- Property binding - bind user input to setters, matching keys from the user input to properties of the target object structure.

You can apply both constructor and property binding or only one.

## Constructor Binding

To use constructor binding:

1. Create a DataBinder with null as the target object.
2. Set targetType to the target class.
3. Call construct.

The target class should have a single public constructor or a single non-public constructor with arguments. If there are multiple constructors, then a default constructor if present is used.

By default, constructor parameter names are used to look up argument values, but you can configure a NameResolver. Spring MVC and WebFlux both rely to allow customizing the name of the value to bind through an @BindParam annotation on constructor parameters.

Type conversion is applied as needed to convert user input. If the constructor parameter is an object, it is constructed recursively in the same manner, but through a nested property path. That means constructor binding creates both the target object and any objects it contains.

Binding and conversion errors are reflected in the BindingResult of the DataBinder. If the target is created successfully, then target is set to the created instance after the call to construct.

## Property Binding with BeanWrapper

The org.springframework.beans package adheres to the JavaBeans standard.

One quite important class in the beans package is the BeanWrapper interface and its corresponding implementation (BeanWrapperImpl). 
- BeanWrapper offers functionality to set and get property values (individually or in bulk), get property descriptors, and query properties to determine if they are readable or writable.
- BeanWrapper offers support for nested properties, enabling the setting of properties on sub-properties to an unlimited depth.
- BeanWrapper also supports the ability to add standard JavaBeans PropertyChangeListeners and VetoableChangeListeners, without the need for supporting code in the target class.
- BeanWrapper provides support for setting indexed properties.
- BeanWrapper usually is not used by application code directly but is used by the DataBinder and the BeanFactory.

The way the BeanWrapper works is partly indicated by its name: it wraps a bean to perform actions on that bean, such as setting and retrieving properties.

## PropertyEditor's

Spring uses the concept of a PropertyEditor to effect the conversion between an Object and a String. 

It can be handy to represent properties in a different way than the object itself. For example, a Date can be represented in a human readable way (as the String: '2007-14-09'), while we can still convert the human readable form back to the original date (or, even better, convert any date entered in a human readable form back to Date objects). 

This behavior can be achieved by registering custom editors of type java.beans.PropertyEditor. Registering custom editors on a BeanWrapper or, alternatively, in a specific IoC container (as mentioned in the previous chapter), gives it the knowledge of how to convert properties to the desired type. 

A couple of examples where property editing is used in Spring:

- Setting properties on beans is done by using PropertyEditor implementations. When you use String as the value of a property of some bean that you declare in an XML file, Spring (if the setter of the corresponding property has a Class parameter) uses ClassEditor to try to resolve the parameter to a Class object.
- Parsing HTTP request parameters in Spring’s MVC framework is done by using all kinds of PropertyEditor implementations that you can manually bind in all subclasses of the CommandController.

Spring has a number of built-in PropertyEditor implementations to make life easy. They are all located in the org.springframework.beans.propertyeditors package. Most, (but not all, as indicated in the following table) are, by default, registered by BeanWrapperImpl. Where the property editor is configurable in some fashion, you can still register your own variant to override the default one.

The following Java source code for the referenced SomethingBeanInfo class associates a CustomNumberEditor with the age property of the Something class:

```java
package com.example.webdemo.binder;

public class SomethingBeanInfo extends SimpleBeanInfo {

	public PropertyDescriptor[] getPropertyDescriptors() {
		try {
			final PropertyEditor numberPE = new CustomNumberEditor(Integer.class, true);
			PropertyDescriptor ageDescriptor = new PropertyDescriptor("age", Something.class) {
				@Override
				public PropertyEditor createPropertyEditor(Object bean) {
					return numberPE;
				}
			};
			return new PropertyDescriptor[] { ageDescriptor };
		}
		catch (IntrospectionException ex) {
			throw new Error(ex.toString());
		}
	}
}
```

**Something.java class**

```java
package com.example.webdemo.binder;

public class Something {
	private int age;

	public Something() {

	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}
}
```

**BinderApp.java application class**

```java
package com.example.webdemo.binder;

import java.beans.PropertyDescriptor;

import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class BinderApp {

	public static void main(String[] args) {

		SomethingBeanInfo info = new SomethingBeanInfo();
		PropertyDescriptor[] props = info.getPropertyDescriptors();
		System.out.println(props[0]);
		System.out.println(props[0].getName());
		System.out.println(props[0].getDisplayName());
		System.out.println(props[0].getPropertyType());
		System.out.println(props[0].getReadMethod());
		System.out.println(props[0].getWriteMethod());

	}
}
```

## @InitBinder

@Controller or @ControllerAdvice classes can have @InitBinder methods to initialize WebDataBinder instances that in turn can:

- Bind request parameters to a model object.
- Convert request values from string to object property types.
- Format model object properties as strings when rendering HTML forms.

In an @Controller, DataBinder customizations apply locally within the controller, or even to a specific model attribute referenced by name through the annotation. In an @ControllerAdvice customizations can apply to all or a subset of controllers.

@InitBinder methods can have many of the same arguments that @RequestMapping methods have, with the notable exception of @ModelAttribute. Typically, such methods have a WebDataBinder argument (for registrations) and a void return value, for example:

```java
@Controller
public class FormController {

	// Defining an @InitBinder method.
	@InitBinder
	public void initBinder(WebDataBinder binder) {
		SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
		dateFormat.setLenient(false);
		binder.registerCustomEditor(Date.class, new CustomDateEditor(dateFormat, false));
	}

	// ...
}
```

## Validation by Using Spring’s Validator Interface

Spring features a Validator interface that you can use to validate objects. The Validator interface works by using an Errors object so that, while validating, validators can report validation failures to the Errors object.

Consider the following example of a small data object:

```java
package com.example.webdemo.validation;

public class Person {

	private String name;
	private int age;

	public Person() {

	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

}
```

The next example provides validation behavior for the Person class by implementing the following two methods of the org.springframework.validation.Validator interface:

- supports(Class): Can this Validator validate instances of the supplied Class?
- validate(Object, org.springframework.validation.Errors): Validates the given object and, in case of validation errors, registers those with the given Errors object.

Implementing a Validator is fairly straightforward, especially when you know of the ValidationUtils helper class that the Spring Framework also provides. The following example implements Validator for Person instances:

```java
package com.example.webdemo.validation;

import org.springframework.stereotype.Component;
import org.springframework.validation.Errors;
import org.springframework.validation.ValidationUtils;
import org.springframework.validation.Validator;

@Component
public class PersonValidator implements Validator {

	/**
	 * This Validator validates only Person instances
	 */
	public boolean supports(Class clazz) {
		return Person.class.equals(clazz);
	}

	public void validate(Object obj, Errors e) {
		ValidationUtils.rejectIfEmpty(e, "name", "name.empty");
		Person p = (Person) obj;
		if (p.getAge() < 0) {
			e.rejectValue("age", "negativevalue");
			System.out.println("Rejected value: age is negative value");
		} else if (p.getAge() > 110) {
			e.rejectValue("age", "too.darn.old");
			System.out.println("Rejected value: age is greater than 110");
		}
	}
}
```

Here's the PersonController class.

```java
package com.example.webdemo.validation;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.WebDataBinder;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.InitBinder;
import org.springframework.web.bind.annotation.PostMapping;

@Controller
public class PersonController {

	@Autowired
	private PersonValidator personValidator;

	@InitBinder
	public void initBinder(WebDataBinder binder) {
		binder.addValidators(personValidator);
	}

	@GetMapping("/getperson")
	public String getPerson() {
		return "get-person";
	}

	@PostMapping("/getperson")
	public String getPersonDetails(@Validated Person person, Model model, BindingResult result) {
		if (result.hasErrors()) {
			return "error-person";
		}
		model.addAttribute("name", person.getName());
		model.addAttribute("age", person.getAge());
		return "person-details";
	}
}
```

The view components: person-details.jsp and error-person.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Person Details</title>
</head>
<body>
	<h1 align="center">Person Details</h1>
	<hr />
	<p>Name is: ${person.name}</p>
	<p>Age is: ${person.age}</p>
</body>
</html>
```

```jsp
<html>
<head>
</head>
<body>
	<h1 align="center">Validation Failed</h1>
</body>
</html>
```
