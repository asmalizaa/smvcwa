# Working with Forms

## Spring’s form tag library

As of version 2.0, Spring provides a comprehensive set of data binding-aware tags for handling form elements when using JSP and Spring Web MVC. Each tag provides support for the set of attributes of its corresponding HTML tag counterpart, making the tags familiar and intuitive to use. The tag-generated HTML is HTML 4.01/XHTML 1.0 compliant.

Unlike other form/input tag libraries, Spring’s form tag library is integrated with Spring Web MVC, giving the tags access to the command object and reference data your controller deals with. As we show in the following examples, the form tags make JSPs easier to develop, read, and maintain.

## Configuration

The form tag library comes bundled in spring-webmvc.jar. The library descriptor is called spring-form.tld.

To use the tags from this library, add the following directive to the top of your JSP page:

```jsp
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
```
where form is the tag name prefix you want to use for the tags from this library.

## The Form Tag

This tag renders an HTML 'form' element and exposes a binding path to inner tags for binding. It puts the command object in the PageContext so that the command object can be accessed by inner tags. All the other tags in this library are nested tags of the form tag.

Assume that we have a domain object called User. It is a JavaBean with properties such as firstName and lastName. We can use it as the form-backing object of our form controller, which returns form.jsp. The following example shows what form.jsp could look like:

```jsp
<form:form>
	<table>
		<tr>
			<td>First Name:</td>
			<td><form:input path="firstName"/></td>
		</tr>
		<tr>
			<td>Last Name:</td>
			<td><form:input path="lastName"/></td>
		</tr>
		<tr>
			<td colspan="2">
				<input type="submit" value="Save Changes"/>
			</td>
		</tr>
	</table>
</form:form>
```

The firstName and lastName values are retrieved from the command object placed in the PageContext by the page controller. Keep reading to see more complex examples of how inner tags are used with the form tag.

The following listing shows the generated HTML, which looks like a standard form:

```jsp
<form method="POST">
	<table>
		<tr>
			<td>First Name:</td>
			<td><input name="firstName" type="text" value="Harry"/></td>
		</tr>
		<tr>
			<td>Last Name:</td>
			<td><input name="lastName" type="text" value="Potter"/></td>
		</tr>
		<tr>
			<td colspan="2">
				<input type="submit" value="Save Changes"/>
			</td>
		</tr>
	</table>
</form>
```

The preceding JSP assumes that the variable name of the form-backing object is command. If you have put the form-backing object into the model under another name (definitely a best practice), you can bind the form to the named variable, as the following example shows:

```jsp
<form:form modelAttribute="user">
	<table>
		<tr>
			<td>First Name:</td>
			<td><form:input path="firstName"/></td>
		</tr>
		<tr>
			<td>Last Name:</td>
			<td><form:input path="lastName"/></td>
		</tr>
		<tr>
			<td colspan="2">
				<input type="submit" value="Save Changes"/>
			</td>
		</tr>
	</table>
</form:form>
```

## The input Tag

This tag renders an HTML input element with the bound value and type='text' by default. For an example of this tag, see [The Form Tag](https://docs.spring.io/spring-framework/reference/web/webmvc-view/mvc-jsp.html#mvc-view-jsp-formtaglib-formtag). You can also use HTML5-specific types, such as email, tel, date, and others.

## The checkbox Tag

This tag renders an HTML input tag with the type set to checkbox.

Assume that our User has preferences such as newsletter subscription and a list of hobbies. The following example shows the Preferences class:

```java
public class Preferences {

	private boolean receiveNewsletter;
	private String[] interests;
	private String favouriteWord;

	public boolean isReceiveNewsletter() {
		return receiveNewsletter;
	}

	public void setReceiveNewsletter(boolean receiveNewsletter) {
		this.receiveNewsletter = receiveNewsletter;
	}

	public String[] getInterests() {
		return interests;
	}

	public void setInterests(String[] interests) {
		this.interests = interests;
	}

	public String getFavouriteWord() {
		return favouriteWord;
	}

	public void setFavouriteWord(String favouriteWord) {
		this.favouriteWord = favouriteWord;
	}
}
```

The corresponding form.jsp could then resemble the following:

```jsp
<form:form>
	<table>
		<tr>
			<td>Subscribe to newsletter?:</td>
			<%-- Approach 1: Property is of type java.lang.Boolean --%>
			<td><form:checkbox path="preferences.receiveNewsletter"/></td>
		</tr>

		<tr>
			<td>Interests:</td>
			<%-- Approach 2: Property is of an array or of type java.util.Collection --%>
			<td>
				Quidditch: <form:checkbox path="preferences.interests" value="Quidditch"/>
				Herbology: <form:checkbox path="preferences.interests" value="Herbology"/>
				Defence Against the Dark Arts: <form:checkbox path="preferences.interests" value="Defence Against the Dark Arts"/>
			</td>
		</tr>

		<tr>
			<td>Favourite Word:</td>
			<%-- Approach 3: Property is of type java.lang.Object --%>
			<td>
				Magic: <form:checkbox path="preferences.favouriteWord" value="Magic"/>
			</td>
		</tr>
	</table>
</form:form>
```

There are three approaches to the checkbox tag, which should meet all your checkbox needs.

- Approach One: When the bound value is of type java.lang.Boolean, the input(checkbox) is marked as checked if the bound value is true. The value attribute corresponds to the resolved value of the setValue(Object) value property.
- Approach Two: When the bound value is of type array or java.util.Collection, the input(checkbox) is marked as checked if the configured setValue(Object) value is present in the bound Collection.
- Approach Three: For any other bound value type, the input(checkbox) is marked as checked if the configured setValue(Object) is equal to the bound value.

Note that, regardless of the approach, the same HTML structure is generated. The following HTML snippet defines some checkboxes:

```jsp
<tr>
	<td>Interests:</td>
	<td>
		Quidditch: <input name="preferences.interests" type="checkbox" value="Quidditch"/>
		<input type="hidden" value="1" name="_preferences.interests"/>
		Herbology: <input name="preferences.interests" type="checkbox" value="Herbology"/>
		<input type="hidden" value="1" name="_preferences.interests"/>
		Defence Against the Dark Arts: <input name="preferences.interests" type="checkbox" value="Defence Against the Dark Arts"/>
		<input type="hidden" value="1" name="_preferences.interests"/>
	</td>
</tr>
```

You might not expect to see the additional hidden field after each checkbox. When a checkbox in an HTML page is not checked, its value is not sent to the server as part of the HTTP request parameters once the form is submitted, so we need a workaround for this quirk in HTML for Spring form data binding to work. The checkbox tag follows the existing Spring convention of including a hidden parameter prefixed by an underscore (_) for each checkbox. By doing this, you are effectively telling Spring that “the checkbox was visible in the form, and I want my object to which the form data binds to reflect the state of the checkbox, no matter what.”

## The checkboxes Tag

This tag renders multiple HTML input tags with the type set to checkbox.

This section build on the example from the previous checkbox tag section. Sometimes, you prefer not to have to list all the possible hobbies in your JSP page. You would rather provide a list at runtime of the available options and pass that in to the tag. That is the purpose of the checkboxes tag. You can pass in an Array, a List, or a Map that contains the available options in the items property. Typically, the bound property is a collection so that it can hold multiple values selected by the user. The following example shows a JSP that uses this tag:

```jsp
<form:form>
	<table>
		<tr>
			<td>Interests:</td>
			<td>
				<%-- Property is of an array or of type java.util.Collection --%>
				<form:checkboxes path="preferences.interests" items="${interestList}"/>
			</td>
		</tr>
	</table>
</form:form>
```

This example assumes that the interestList is a List available as a model attribute that contains strings of the values to be selected from. If you use a Map, the map entry key is used as the value, and the map entry’s value is used as the label to be displayed. You can also use a custom object where you can provide the property names for the value by using itemValue and the label by using itemLabel.

## The radiobutton Tag

This tag renders an HTML input element with the type set to radio.

A typical usage pattern involves multiple tag instances bound to the same property but with different values, as the following example shows:

```jsp
<tr>
	<td>Sex:</td>
	<td>
		Male: <form:radiobutton path="sex" value="M"/> <br/>
		Female: <form:radiobutton path="sex" value="F"/>
	</td>
</tr>
```

## The radiobuttons Tag

This tag renders multiple HTML input elements with the type set to radio.

As with the checkboxes tag, you might want to pass in the available options as a runtime variable. For this usage, you can use the radiobuttons tag. You pass in an Array, a List, or a Map that contains the available options in the items property. If you use a Map, the map entry key is used as the value and the map entry’s value are used as the label to be displayed. You can also use a custom object where you can provide the property names for the value by using itemValue and the label by using itemLabel, as the following example shows:

```jsp
<tr>
	<td>Sex:</td>
	<td><form:radiobuttons path="sex" items="${sexOptions}"/></td>
</tr>
```

## The password Tag

This tag renders an HTML input tag with the type set to password with the bound value.

```jsp
<tr>
	<td>Password:</td>
	<td>
		<form:password path="password"/>
	</td>
</tr>
```

Note that, by default, the password value is not shown. If you do want the password value to be shown, you can set the value of the showPassword attribute to true, as the following example shows:

```jsp
<tr>
	<td>Password:</td>
	<td>
		<form:password path="password" value="^76525bvHGq" showPassword="true"/>
	</td>
</tr>
```

### The select Tag

This tag renders an HTML 'select' element. It supports data binding to the selected option as well as the use of nested option and options tags.

Assume that a User has a list of skills. The corresponding HTML could be as follows:

```jsp
<tr>
	<td>Skills:</td>
	<td><form:select path="skills" items="${skills}"/></td>
</tr>
```

If the User’s skill are in Herbology, the HTML source of the 'Skills' row could be as follows:

```jsp
<tr>
	<td>Skills:</td>
	<td>
		<select name="skills" multiple="true">
			<option value="Potions">Potions</option>
			<option value="Herbology" selected="selected">Herbology</option>
			<option value="Quidditch">Quidditch</option>
		</select>
	</td>
</tr>
```

## The option Tag

This tag renders an HTML option element. It sets selected, based on the bound value. The following HTML shows typical output for it:

```jsp
<tr>
	<td>House:</td>
	<td>
		<form:select path="house">
			<form:option value="Gryffindor"/>
			<form:option value="Hufflepuff"/>
			<form:option value="Ravenclaw"/>
			<form:option value="Slytherin"/>
		</form:select>
	</td>
</tr>
```

If the User’s house was in Gryffindor, the HTML source of the 'House' row would be as follows:

```jsp
<tr>
	<td>House:</td>
	<td>
		<select name="house">
			<option value="Gryffindor" selected="selected">Gryffindor</option>
			<option value="Hufflepuff">Hufflepuff</option>
			<option value="Ravenclaw">Ravenclaw</option>
			<option value="Slytherin">Slytherin</option>
		</select>
	</td>
</tr>
```

## The options Tag

This tag renders a list of HTML option elements. It sets the selected attribute, based on the bound value. The following HTML shows typical output for it:

```jsp
<tr>
	<td>Country:</td>
	<td>
		<form:select path="country">
			<form:option value="-" label="--Please Select"/>
			<form:options items="${countryList}" itemValue="code" itemLabel="name"/>
		</form:select>
	</td>
</tr>
```

If the User lived in the UK, the HTML source of the 'Country' row would be as follows:

```jsp
<tr>
	<td>Country:</td>
	<td>
		<select name="country">
			<option value="-">--Please Select</option>
			<option value="AT">Austria</option>
			<option value="UK" selected="selected">United Kingdom</option>
			<option value="US">United States</option>
		</select>
	</td>
</tr>
```

As the preceding example shows, the combined usage of an option tag with the options tag generates the same standard HTML but lets you explicitly specify a value in the JSP that is for display only (where it belongs), such as the default string in the example: "-- Please Select".

The items attribute is typically populated with a collection or array of item objects. itemValue and itemLabel refer to bean properties of those item objects, if specified. Otherwise, the item objects themselves are turned into strings. Alternatively, you can specify a Map of items, in which case the map keys are interpreted as option values and the map values correspond to option labels. If itemValue or itemLabel (or both) happen to be specified as well, the item value property applies to the map key, and the item label property applies to the map value.

## The textarea Tag

This tag renders an HTML textarea element. The following HTML shows typical output for it:

```jsp
<tr>
	<td>Notes:</td>
	<td><form:textarea path="notes" rows="3" cols="20"/></td>
	<td><form:errors path="notes"/></td>
</tr>
```

## The hidden Tag

This tag renders an HTML input tag with the type set to hidden with the bound value. To submit an unbound hidden value, use the HTML input tag with the type set to hidden. The following HTML shows typical output for it:

```jsp
<form:hidden path="house"/>
```

If we choose to submit the house value as a hidden one, the HTML would be as follows:

```jsp
<input name="house" type="hidden" value="Gryffindor"/>
```

## The errors Tag

This tag renders field errors in an HTML span element. It provides access to the errors created in your controller or those that were created by any validators associated with your controller.

Assume that we want to display all error messages for the firstName and lastName fields once we submit the form. We have a validator for instances of the User class called UserValidator, as the following example shows:

```java
public class UserValidator implements Validator {

	public boolean supports(Class candidate) {
		return User.class.isAssignableFrom(candidate);
	}

	public void validate(Object obj, Errors errors) {
		ValidationUtils.rejectIfEmptyOrWhitespace(errors, "firstName", "required", "Field is required.");
		ValidationUtils.rejectIfEmptyOrWhitespace(errors, "lastName", "required", "Field is required.");
	}
}
```

The form.jsp could be as follows:

```jsp
<form:form>
	<table>
		<tr>
			<td>First Name:</td>
			<td><form:input path="firstName"/></td>
			<%-- Show errors for firstName field --%>
			<td><form:errors path="firstName"/></td>
		</tr>

		<tr>
			<td>Last Name:</td>
			<td><form:input path="lastName"/></td>
			<%-- Show errors for lastName field --%>
			<td><form:errors path="lastName"/></td>
		</tr>
		<tr>
			<td colspan="3">
				<input type="submit" value="Save Changes"/>
			</td>
		</tr>
	</table>
</form:form>
```
If we submit a form with empty values in the firstName and lastName fields, the HTML would be as follows:

```jsp
<form method="POST">
	<table>
		<tr>
			<td>First Name:</td>
			<td><input name="firstName" type="text" value=""/></td>
			<%-- Associated errors to firstName field displayed --%>
			<td><span name="firstName.errors">Field is required.</span></td>
		</tr>

		<tr>
			<td>Last Name:</td>
			<td><input name="lastName" type="text" value=""/></td>
			<%-- Associated errors to lastName field displayed --%>
			<td><span name="lastName.errors">Field is required.</span></td>
		</tr>
		<tr>
			<td colspan="3">
				<input type="submit" value="Save Changes"/>
			</td>
		</tr>
	</table>
</form>
```

What if we want to display the entire list of errors for a given page? The next example shows that the errors tag also supports some basic wildcarding functionality.

- path="*": Displays all errors.
- path="lastName": Displays all errors associated with the lastName field.
- If path is omitted, only object errors are displayed.

The following example displays a list of errors at the top of the page, followed by field-specific errors next to the fields:

```jsp
<form:form>
	<form:errors path="*" cssClass="errorBox"/>
	<table>
		<tr>
			<td>First Name:</td>
			<td><form:input path="firstName"/></td>
			<td><form:errors path="firstName"/></td>
		</tr>
		<tr>
			<td>Last Name:</td>
			<td><form:input path="lastName"/></td>
			<td><form:errors path="lastName"/></td>
		</tr>
		<tr>
			<td colspan="3">
				<input type="submit" value="Save Changes"/>
			</td>
		</tr>
	</table>
</form:form>
```

The HTML would be as follows:

```html
<form method="POST">
	<span name="*.errors" class="errorBox">Field is required.<br/>Field is required.</span>
	<table>
		<tr>
			<td>First Name:</td>
			<td><input name="firstName" type="text" value=""/></td>
			<td><span name="firstName.errors">Field is required.</span></td>
		</tr>

		<tr>
			<td>Last Name:</td>
			<td><input name="lastName" type="text" value=""/></td>
			<td><span name="lastName.errors">Field is required.</span></td>
		</tr>
		<tr>
			<td colspan="3">
				<input type="submit" value="Save Changes"/>
			</td>
		</tr>
	</table>
</form>
```

## Form Processing in Spring MVC

Reference: (https://www.baeldung.com/spring-mvc-form-tutorial)

In this topic, we will discuss Spring forms and data binding to a controller. Also, we will have a look at one of the main annotations in Spring MVC i.e. @ModelAttribute.

### Example

1. The Model

   First – let’s define a simple entity that we’re going to display and bind to the form.
   
   ```java
   package com.example.webdemo.forms;

   public class Employee {
       private long id;
       private String name;
       private String contactNumber;

       public Employee() {
       }

       public Employee(long id, String name, String contactNumber) {
           this.id = id;
           this.name = name;
           this.contactNumber = contactNumber;
       }

       public long getId() {
           return id;
       }

       public void setId(long id) {
           this.id = id;
       }

       public String getName() {
           return name;
       }

       public void setName(String name) {
           this.name = name;
       }

       public String getContactNumber() {
           return contactNumber;
       }

       public void setContactNumber(String contactNumber) {
           this.contactNumber = contactNumber;
       }

       @Override
       public String toString() {
           return "Id: " + getId() + "\nName: " + getName() + "\nContact Number: " + getContactNumber() + "\n";
       }
   }
   ```

   This will be our form-backing object, or also known as Command Object.

2. The View

   Next – let’s define the actual form, and of course, the HTML file that contains it. We’re going to be using a page where a new employee is created/registered.

   ```jsp
   <%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
   <html>
       <head></head>
       <body>
           <h3>Welcome, Enter The Employee Details</h3>
           <form:form method="POST" action="/addEmployee" modelAttribute="employee">
               <table>
                   <tr>
                       <td><form:label path="id">Id</form:label></td>
                       <td><form:input path="id"/></td>
                   </tr>
                   <tr>
                       <td><form:label path="name">Name</form:label></td>
                       <td><form:input path="name"/></td>
                   </tr>
                   <tr>
                       <td><form:label path="contactNumber">Contact Number</form:label></td>
                       <td><form:input path="contactNumber"/></td>
                   </tr>
                   <tr>
                       <td><input type="submit" value="Submit"/></td>
                   </tr>
               </table>
           </form:form>
       </body>
   </html>
   ```

   First – notice that we’re including a tag library into our JSP page – the form taglib – to help with defining our form.

   Next – the <form:form> tag plays an important role here; it’s very similar to the regular HTLM <form> tag but the modelAttribute attribute is the key which specifies a name of the model object that backs this form:

   ```jsp
   <form:form method="POST" action="/addEmployee" modelAttribute="employee">
   ```

   This will correspond to the @ModelAttribute later on in the controller.

   Next – each input fields is using yet another useful tag from the Spring Form taglib – form: prefix. Each of these fields specifies a path attribute – this must correspond to a getter/setter of the model attribute (in this case, the Employee class). When the page is loaded, the input fields are populated by Spring, which calls the getter of each field bound to an input field. When the form is submitted, the setters are called to save the values of the form to the object.

   Finally – when the form is submitted, the POST handler in the controller is invoked and the form is automatically bound to the employee argument that we passed in.

3. The Controller

   Now, let’s look at the Controller that’s going to handle the back end:

   ```java
   package com.example.webdemo.forms;

   import org.springframework.stereotype.Controller;
   import org.springframework.ui.ModelMap;
   import org.springframework.validation.BindingResult;
   import org.springframework.validation.annotation.Validated;
   import org.springframework.web.bind.annotation.ModelAttribute;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RequestMethod;
   import org.springframework.web.servlet.ModelAndView;

   @Controller
   public class EmployeeController {

       @RequestMapping(value = "/employee", method = RequestMethod.GET)
       public ModelAndView showForm() {
           return new ModelAndView("employeeHome", "employee", new Employee());
       }

       @RequestMapping(value = "/addEmployee", method = RequestMethod.POST)
       public String submit(@Validated @ModelAttribute("employee") Employee employee, BindingResult result, ModelMap model) {
           if (result.hasErrors()) {
               return "error";
           }

           model.addAttribute("name", employee.getName());
           model.addAttribute("contactNumber", employee.getContactNumber());
           model.addAttribute("id", employee.getId());
           return "employeeView";
       }
   }
   ```

   The controller defines two simple operations – the GET for displaying data in the form, and the POST for the create operation, via form’s submit.

   To access our form backing object, we need to inject it via the @ModelAttribute annotation.

   An <em>@ModelAttribute </em>on a method argument indicates the argument will be retrieved from the model. If not present in the model, the argument will be instantiated first and then added to the model.

4. Handling Bind Errors

   By default, Spring MVC throws an exception when errors occur during request binding. This usually not what we want, instead, we should be presenting these errors to the user. We’re going to use a BindingResult by adding one as an argument to our controller method:

   ```java
   public String submit(@Validated @ModelAttribute("employee") Employee employee, BindingResult result, ModelMap model)
   ```

   The BindingResult argument needs to be positioned right after our form backing object – it’s one of the rare cases where the order of the method arguments matters. Otherwise, we’ll run into the following exception:

   ```java
   java.lang.IllegalStateException: Errors/BindingResult argument declared without preceding model attribute. Check your handler method signature!
   ```

   Notice how, in case the result contains errors, we’re returning another view to the user in order to display these errors correctly. Let’s take a look at that view – error.jsp:

   ```jsp
   <html>
       <head></head>
       <body>
           <h3>Please enter the correct details</h3>
           <table>
               <tr>
                   <td><a href="employee">Retry</a></td>
               </tr>
           </table>
       </body>
   </html>
   ```

5. Displaying an Employee

   Finally, besides creating a new employee, we can also simply display one – here’s the quick view code for that:

   ```jsp
   <html>
       <head></head>
       <body>
           <h2>Submitted Employee Information</h2>
           <table>
               <tr>
                   <td>ID :</td>
                   <td>${id}</td>
               </tr>
               <tr>
                   <td>Name :</td>
                   <td>${name}</td>
               </tr>
               <tr>
                   <td>Contact Number :</td>
                   <td>${contactNumber}</td>
               </tr>
           </table>
       </body>
   <html>
   ```

   The JSP page is simply using EL expressions to display values of properties of the Employee object in the model.

6. Finally, the configuration and application class.

   ```java
   package com.example.webdemo.forms;

   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.ComponentScan;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.web.servlet.ViewResolver;
   import org.springframework.web.servlet.config.annotation.EnableWebMvc;
   import org.springframework.web.servlet.view.InternalResourceViewResolver;

   @Configuration
   @EnableWebMvc
   @ComponentScan("com.example.webdemo.forms")
   public class EmployeeConfig {
       @Bean
       public ViewResolver viewResolver() {
           InternalResourceViewResolver resolver = new InternalResourceViewResolver();
           resolver.setPrefix("/WEB-INF/jsp/");
           resolver.setSuffix(".jsp");
           resolver.setOrder(1);
           return resolver;
       }
   }
   ```

   ```java
   package com.example.webdemo.forms;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;

   @SpringBootApplication
   public class EmployeeApp {
       public static void main(String[] args) {
           SpringApplication.run(EmployeeApp.class, args);
       }
   }
   ```

7. Test the application.

   Run the application, launch the browser and type this address (http://localhost:8080/employee)

   The register new employe form will be displayed.

   ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/6e4c3756-6d07-4342-98f4-ae910b4d940c)

   Fill up the details then click 'Submit' button. The view employee details page will be displayed.

   ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/1710f854-02ab-4d37-8b28-fd7c78cd1761)


