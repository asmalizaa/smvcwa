# Working with Forms

## Form Processing in Spring MVC

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
   
