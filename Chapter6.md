# The Web Module

## Why we need Servlet and JSPs?

Web servers are good for static contents HTML pages, but they don’t know how to generate dynamic content or how to save data into databases, so we need another tool that we can use to generate dynamic content. 

There are several programming languages for dynamic content like PHP, Python, Ruby on Rails, Java Servlets and JSPs. 

Java Servlet and JSPs are server-side technologies to extend the capability of web servers by providing support for dynamic response and data persistence.

## Servlet and JSP Application Architecture

JSP architecture gives a high-level view of the working of JSP. 
- JSP architecture is a 3-tier architecture.
- It has a Client, Web Server, and Database.
- The client is the web browser or application on the user side.
- Web Server uses a JSP Engine i.e; a container that processes JSP. For example, Apache Tomcat has a built-in JSP Engine.
- JSP Engine intercepts the request for JSP and provides the runtime environment for the understanding and processing of JSP files.
- It reads, parses, build Java Servlet, Compiles and Executes Java code, and returns the HTML page to the client.
- The webserver has access to the Database.

The following diagram shows the architecture of JSP.

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/bbb4e93f-0dd8-40f2-a267-fa198061f259)

Now let us discuss JSP which stands for Java Server Pages. 

- It is a server-side technology.
- It is used for creating web applications.
- It is used to create dynamic web content.
- In this JSP tags are used to insert JAVA code into HTML pages.
- It is an advanced version of Servlet Technology.
- It is a Web-based technology that helps us to create dynamic and platform-independent web pages.
- In this, Java code can be inserted in HTML/ XML pages or both.
- JSP is first converted into a servlet by JSP container before processing the client’s request.
- 
JSP Processing is illustrated and discussed in sequential steps prior to which a pictorial media is provided as a handful pick to understand the JSP processing better which is as follows:

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/19b1a2be-5df2-41d3-b559-91b7adf9f63a)

Step 1: The client navigates to a file ending with the .jsp extension and the browser initiates an HTTP request to the webserver. For example, the user enters the login details and submits the button. The browser requests a status.jsp page from the webserver.

Step 2: If the compiled version of JSP exists in the web server, it returns the file. Otherwise, the request is forwarded to the JSP Engine. This is done by recognizing the URL ending with .jsp extension.

Step 3: The JSP Engine loads the JSP file and translates the JSP to Servlet (Java code). This is done by converting all the template text into println() statements and JSP elements to Java code. This process is called translation.

Step 4: The JSP engine compiles the Servlet to an executable .class file. It is forwarded to the Servlet engine. This process is called compilation or request processing phase.

Step 5: The .class file is executed by the Servlet engine which is a part of the Web Server. The output is an HTML file. The Servlet engine passes the output as an HTTP response to the webserver.

Step 6: The web server forwards the HTML file to the client’s browser.

**Example**

Reference: (https://www.baeldung.com/spring-boot-jsp)

## The MVC Pattern

The Model-View-Controller (MVC) design pattern is a software design pattern that divides the related program logic into three interconnected elements: the model, the view, and the controller.

The MVC pattern is used for developing user interfaces, especially web applications, because it allows the application to be scalable, maintainable, and easy to expand.

Here is a diagram that shows the interactions within one possible take on the MVC pattern:

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/9ab5ea19-b8d1-4d97-861e-369f33e962cd)

The diagram illustrates the following roles and responsibilities of each element:

1. **Model**: The model represents the data and the business logic of the application. It is responsible for managing the data, performing calculations, and providing data to the view. The model does not depend on the view or the controller, and can be tested independently.

2. **View**: The view is the user interface of the application. It displays the data from the model to the user and receives user input. The view does not contain any logic or know how to manipulate the data. It only communicates with the controller, which acts as an intermediary between the view and the model.

3. **Controller**: The controller is the coordinator of the application. It handles user input from the view, updates the model accordingly, and sends data back to the view. The controller acts as a bridge between the model and the view, and controls the flow of information and logic.

The MVC pattern helps to achieve separation of concerns, which means that each element has a distinct and well-defined role and responsibility. This makes the code more modular, reusable, and easy to maintain. It also allows multiple developers to work on different parts of the application simultaneously without interfering with each other.

## The Front Controller Pattern

Reference: (https://www.baeldung.com/java-front-controller-pattern)

Front Controller is defined as “a controller that handles all requests for a Web site”. It stands in front of a web-application and delegates requests to subsequent resources. It also provides an interface to common behavior such as security, internationalization and presenting particular views to certain users.

This enables an application to change its behavior at runtime. Furthermore it helps to read and maintain an application by preventing code duplication.

### How Does it Work?

The Front Controller Pattern is mainly divided into two parts. A single dispatching controller and a hierarchy of commands. The following UML depicts class relations of a generic Front Controller implementation:

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/6c4a0202-fc87-4977-89fc-51dfa7d49af7)

This single controller dispatches requests to commands in order to trigger behavior associated with a request.

**Example**

## DispatcherServlet

Dispatcher Servlet is the front controller that manages the entire HTTP request and response handling process. 

Now, the question is What is Front Controller? It is quite simple, as the name suggests, when any web requests are made, the requests will come first to the front Controller which is nothing but the Dispatcher Servlet. The Front Controller stands first that is why it’s name is like this. After the requests comes into this, the dispatcher servlet accepts the requests and decides which controller will be suitable to handle these requests. Then it dispatches the HTTP requests to specific controller.

![image](https://github.com/asmalizaa/javaspring/assets/23090837/1bae210f-cfab-4f89-8487-34b56ccc6306)

1. All the incoming requests are intercepted by the DispatcherServlet that works as the front controller.
2. The DispatcherServlet then gets an entry of handler mapping from the XML file and forwards the request to the controller.
3. The object of ModelAndView is returned by the controller.
4. The DispatcherServlet checks the entry of the view resolver in the XML file and invokes the appropriate view component.

![image](https://github.com/asmalizaa/javaspring/assets/23090837/c8cc88ad-6406-4ff8-9e5d-eb2cd11c5f54)

## Activity: Spring Web MVC

In this activity, we are going to create a form to add a new tutorial.

**Steps**

1. Add dependencies to your existing project.

   - Thymeleaf dependency to your project (if needed).

     ```xml
     <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-thymeleaf</artifactId>
     </dependency>
     ```

   - Spring Boot Devtools (if needed).
     ```xml
     <dependency>
     	<groupId>org.springframework.boot</groupId>
     	<artifactId>spring-boot-devtools</artifactId>
     	<scope>runtime</scope>
     	<optional>true</optional>
     </dependency>
     ```

2. Add a welcome page in this location src/main/resources/static

   ```html
   <!DOCTYPE html>
   <html>
       <head>
           <meta charset="UTF-8">
           <title>Welcome Page</title>
       </head>
   <body>
       <h1>Welcome Page</h1>
       <p>Add new tutorial click <a href="/addnew">here</a></p>
   </body>
   </html>
   ```
   
3. Create a TutorialService class that will act as the Controller.

   ```java
   import org.springframework.stereotype.Controller;
   import org.springframework.ui.Model;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.ModelAttribute;
   import org.springframework.web.bind.annotation.PostMapping;

   @Controller
   public class TutorialService {
       @GetMapping("/addnew")
       public String addNewForm(Model model) {
           model.addAttribute("tutorial", new Tutorial());
           return "addnew";
       }

       @PostMapping("/addnew")
       public String addNewSubmit(@ModelAttribute Tutorial tutorial, Model model) {
           model.addAttribute("tutorial", tutorial);
           return "result";
       }
   }
   ```

   This controller is concise and simple, but a lot is going on. The rest of this section analyzes it step by step.
   - The mapping annotations let you map HTTP requests to specific controller methods. The two methods in this controller are both mapped to /addnew.
   - You can use @RequestMapping (which, by default, maps all HTTP operations, such as GET, POST, and so forth).
   - However, in this case, the addNewForm() method is specifically mapped to GET by using @GetMapping, while addNewSubmit() is mapped to POST with @PostMapping.
   - This mapping lets the controller differentiate the requests to the /addnew endpoint.
   - The addNewForm() method uses a Model object to expose a new Tutorial to the view template.
   - The implementation of the method body relies on a view technology to perform server-side rendering of the HTML by converting the view name (addnew) into a template to render.
   - In this case, we use Thymeleaf, which parses the addnew.html template and evaluates the various template expressions to render the form.
   - The following listing (from src/main/resources/templates/addnew.html) shows the addnew template.

5. Create the 'Add New Tutorial' form page in this location src/main/resources/templates

   ```html
   <!DOCTYPE HTML>
   <html xmlns:th="https://www.thymeleaf.org">
   <head>
      <title>Add New Tutorial</title>
      <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
   </head>
   <body>
      <h1>Add New Tutorial</h1>
      <form action="#" th:action="@{/addnew}" th:object="${tutorial}" method="post">
         <p>Title: <input type="text" th:field="*{title}" /></p>
         <p>Description: <input type="text" th:field="*{description}" /></p>
         <p><input type="submit" value="Submit" /> <input type="reset" value="Reset" /></p>
      </form>
   </body>
   </html>
   ```

   - The th:action="@{/addnew}" expression directs the form to POST to the /addnew endpoint, while the th:object="${tutorial}" expression declares the model object to use for collecting the form data.
   - The three form fields, expressed with th:field="{id}", th:field="{title}" and th:field="{description}", correspond to the fields in the Tutorial object.
   - That covers the controller, model, and view for presenting the form. Now we can review the process of submitting the form.
   - As noted earlier, the form submits to the /addnew endpoint by using a POST call.
   - The addNewSubmit() method receives the Tutorial object that was populated by the form.
   - The Tutorial is a @ModelAttribute, so it is bound to the incoming form content.
   - Also, the submitted data can be rendered in the result view by referring to it by name (by default, the name of the method parameter, so tutorial in this case).
   - The id is rendered in the ```<p th:text="'id: ' + ${tutorial.id}" />``` expression.
   - Likewise, the title is rendered in the ```<p th:text="'title: ' + ${tutorial.title}" />``` expression.
   - The following listing (from src/main/resources/templates/result.html) shows the result template.

4. Create the 'Result' page in this location src/main/resources/templates

   ```html
   <!DOCTYPE HTML>
   <html xmlns:th="https://www.thymeleaf.org">
   <head>
      <title>Result</title>
      <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
   </head>
   <body>
      <h1>Result</h1>
      <p th:text="'Id: ' + ${tutorial.id}" />
      <p th:text="'Title: ' + ${tutorial.title}" />
      <p th:text="'Description: ' + ${tutorial.description}" />
      <p th:text="'Published: ' + ${tutorial.published}" />
      <a href="/addnew">Add another</a>
   </body>
   </html>
   ```

   For clarity, this example uses two separate view templates for rendering the form and displaying the submitted data. However, you can use a single view for both purposes.

5. Test the service.

   Now that the web site is running, visit http://localhost:8080/addnew, where you see the following form.

   <img width="171" alt="image" src="https://github.com/asmalizaa/javaspring/assets/23090837/cefbb518-6015-4f3d-a60e-1f282def3460">

   Submit an ID, Title and Description to see the results.

   <img width="119" alt="image" src="https://github.com/asmalizaa/javaspring/assets/23090837/4164c91b-0d90-40bb-bf81-6c58305eac98">

   Congratulations! You have just used Spring to create and submit a form.

6. To save the new record into database table, update the controller with below codes.

   ```java
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Controller;
   import org.springframework.ui.Model;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.ModelAttribute;
   import org.springframework.web.bind.annotation.PostMapping;

   @Controller
   public class TutorialService {
       // to save record to db
       // first inject the repository object
       @Autowired
       TutorialRepository tutorialRepository;

       @GetMapping("/addnew")
       public String addNewForm(Model model) {
           model.addAttribute("tutorial", new Tutorial());
           return "addnew";
       }

       @PostMapping("/addnew")
       public String addNewSubmit(@ModelAttribute Tutorial tutorial, Model model) {
           // save new tutorial into db and the return result view
           Tutorial _tutorial = tutorialRepository
               .save(new Tutorial(tutorial.getTitle(), tutorial.getDescription(), tutorial.isPublished()));
           model.addAttribute("tutorial", _tutorial);
           return "result";
       }
   }
   ```

   Make sure to check the database table to ensure the new record was successfully added.
   
## A Request/Response Cycle

Reference: (https://tutorialspedia.com/how-java-spring-mvc-works-spring-mvc-request-flow-explained-step-by-step/#google_vignette)

Spring MVC request flow is depicted in the diagram below:

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/c5f81286-19e3-463d-a777-f8132b327a50)

1. Client requests for a page by specifying the Web URL for the page. E.g. https://www.iverson.com.my
2. Client request is intercepted by the Dispatcher Servlet also known as Front Controller. Dispatcher Servlet is a servlet specified in Web.XML file (for XML Based configurations) or in the Web Configuration class (for java based configurations).
3. Dispatcher Servlet uses URL Mapping Handler to find out the relevant controller class to which request should be passed for subsequent processing. For example, If you have a Controller defined for all requests by specifying “/” in the URL, all requests will be entertained by that controller.
4. Once Dispatcher Servlet has identified the Controller to be considered, it passes the client request to the controller.
5. The controller class is the main class controlling the business logic flow once request has been dispatched it it by dispatcher servlet. This class will implement the methods for different type of http requests (e.g. GET, POST) and all logic to call Service layer methods will reside in this controller class.
6. The controller class will also be responsible for returning the ModelAndView object back to the dispatcher servlet after getting all business logic executed and any data returned from DAO layer. ModelAndView object returned by the controller back to the controller specified both view and model objects.
7. Controller class is annotated by @Controller annotation.
8. After receiving ModelAndView object from the controller, Dispatcher Servlet now sends model object to view resolver to get the name of the view which needs to be rendered.
9. Once the view to be rendered has been identified, Dispatcher Servlet passes model object to the view. Model object contains the data which needs to be displayed in the view. View will be rendered with the model data. Views can be designed in any front-end technology.
10. This view is returned to the client and client can see the view and associated data on his browser.

