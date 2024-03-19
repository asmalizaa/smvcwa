# The Web Module

A web application, also known as a web app, is a software application that runs on one or more web servers. It is typically accessed through a web browser over a network, such as the Internet. The advantage over desktop and mobile applications is being platform-independent, as it can be accessed and used on different devices.

In the Jakarta EE platform, web applications are represented by web components as seen in Jakarta Web Application Request Handling. A web component can be represented by JSP, Jakarta Servlet, Jakarta Faces or Jakarta REST.

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/fbea93e0-9151-4350-9838-0cb8764c89bf)

1. The client sends an HTTP request to the web server.
2. The web server module that supports Jakarta Servlet-based web components is called a servlet container.
3. The servlet container converts the HTTP request into an HttpServletRequest object and prepares the HttpServletResponse object.
4. These objects are delivered to a web component, which can interact with beans or a database to generate dynamic content.
5. The web component can fill the HttpServletResponse object with the generated dynamic content or can pass the object to another web component to fill it.
6. The servlet container ultimately converts the HttpServletResponse object to an HTTP response and the web server returns it to the client.

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

JSP Processing is illustrated and discussed in sequential steps prior to which a pictorial media is provided as a handful pick to understand the JSP processing better which is as follows:

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/19b1a2be-5df2-41d3-b559-91b7adf9f63a)

1. The client navigates to a file ending with the .jsp extension and the browser initiates an HTTP request to the webserver. For example, the user enters the login details and submits the button. The browser requests a status.jsp page from the webserver.
2. If the compiled version of JSP exists in the web server, it returns the file. Otherwise, the request is forwarded to the JSP Engine. This is done by recognizing the URL ending with .jsp extension.
3. The JSP Engine loads the JSP file and translates the JSP to Servlet (Java code). This is done by converting all the template text into println() statements and JSP elements to Java code. This process is called translation.
4. The JSP engine compiles the Servlet to an executable .class file. It is forwarded to the Servlet engine. This process is called compilation or request processing phase.
5. The .class file is executed by the Servlet engine which is a part of the Web Server. The output is an HTML file. The Servlet engine passes the output as an HTTP response to the webserver.
6. The web server forwards the HTML file to the client’s browser.

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

### Example

Note: This example was taken loosely from this tutorial (https://www.javaguides.net/2020/05/spring-boot-jsp-example-tutorial.html)

In this example, we are going to create a simple Spring MVC application that will display Hello message on the browser.

1. Create a new project using [Spring Initialzr](https://start.spring.io/) with below configurations.

   - Project: Maven
   - Language: Java
   - Spring Boot: default
   - Group: default
   - Artifact: webdemo
   - Name: webdemo
   - Description: default
   - Package Name: com.example.webdemo
   - Packaging: jar
   - Java: 21 (or choose the version installed on your machine)
   - Dependencies: Spring Web, Spring Boot DevTools

   Generate the project. Once the generated file downloaded, extract and import into your eclipse.

2. Add maven dependencies into pom.xml

   ```xml
   <!--
		https://mvnrepository.com/artifact/jakarta.servlet.jsp.jstl/jakarta.servlet.jsp.jstl-api -->
   <dependency>
       <groupId>jakarta.servlet.jsp.jstl</groupId>
       <artifactId>jakarta.servlet.jsp.jstl-api</artifactId>
   </dependency>

   <!--
		https://mvnrepository.com/artifact/org.apache.tomcat.embed/tomcat-embed-jasper -->
   <dependency>
       <groupId>org.apache.tomcat.embed</groupId>
       <artifactId>tomcat-embed-jasper</artifactId>
   </dependency>
   ```
   We need to include the tomcat-embed-jasper dependency to allow our application to compile and render JSP pages. Finally, we need to include the jstl library, which will provide the JSTL tags support required in our JSP pages.

3. Create MVC Controller called HelloController.java and add codes below.

   ```java
   package com.example.webdemo;

   import org.springframework.stereotype.Controller;
   import org.springframework.ui.Model;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.RequestParam;

   @Controller
   public class HelloController {

       @GetMapping({ "/", "/hello" })
       public String hello(@RequestParam(value = "name", defaultValue = "World") String name, Model model) {
           model.addAttribute("name", name);
           return "hello";
       }
   }
   ```

4. Create the JSP ViewResolver configuration class called  AppConfig.java

   ```java
   package com.example.webdemo;

   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.ComponentScan;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.web.servlet.ViewResolver;
   import org.springframework.web.servlet.config.annotation.EnableWebMvc;
   import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
   import org.springframework.web.servlet.view.InternalResourceViewResolver;

   @Configuration
   @EnableWebMvc
   @ComponentScan("com.example.webdemo")
   public class AppConfig implements WebMvcConfigurer {

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

   This configuration is very important in order to make sure Spring container able to locate the view component to be returned for each request.

5. Create the JSP page. This file should be created in webapp/WEB-INF/jsp folder. If webapp folder not exists then create it under src/main/webapp/WEB-INF/jsp and then create JSP pages under it.

   ```jsp
   <%@ page language="java" contentType="text/html; charset=UTF-8"	pageEncoding="UTF-8"%>
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="UTF-8">
       <title>Hello World</title>
   </head>
   <body>
       <h1 align="center">Hello ${name}!</h1>
   </body>
   </html>
   ```
   
6. Let's run this spring boot application from IDE -> Right click -> Run As -> Java Application and to test it, launch your browser and type this address (http://localhost:8080/hello)

### Understanding ViewResolver in Spring MVC

In the context of Spring MVC, the ViewResolver interface assists in mapping logical view names, returned by the Controller, to actual view objects, such as JSPs or Thymeleaf templates. It forms a bridge between the controller and the view component of the MVC architecture, determining which view to display based on the logical view name.

### The Role of ViewResolver in the Spring MVC Flow

When a request is processed by a Spring MVC application, the request is received by the DispatcherServlet, which then routes it to the appropriate controller. The controller performs the required business logic and returns a ModelAndView object, containing the model data and the logical name of the view

This logical view name is passed onto the ViewResolver, which then resolves it to a specific view technology, such as JSP, Thymeleaf, or FreeMarker. The DispatcherServlet will render this view, along with the model data, back to the client.

## The Front Controller Pattern

Reference: (https://www.baeldung.com/java-front-controller-pattern)

Front Controller is defined as "a controller that handles all requests for a Web site". It stands in front of a web-application and delegates requests to subsequent resources. It also provides an interface to common behavior such as security, internationalization and presenting particular views to certain users.

This enables an application to change its behavior at runtime. Furthermore it helps to read and maintain an application by preventing code duplication.

The Front Controller consolidates all request handling by channeling requests through a single handler object.

### How Does it Work?

The Front Controller Pattern is mainly divided into two parts. A single dispatching controller and a hierarchy of commands. The following UML depicts class relations of a generic Front Controller implementation:

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/6c4a0202-fc87-4977-89fc-51dfa7d49af7)

This single controller dispatches requests to commands in order to trigger behavior associated with a request.

## DispatcherServlet

Dispatcher Servlet is the front controller that manages the entire HTTP request and response handling process. 

Now, the question is What is Front Controller? It is quite simple, as the name suggests, when any web requests are made, the requests will come first to the front Controller which is nothing but the Dispatcher Servlet. The Front Controller stands first that is why it’s name is like this. After the requests comes into this, the dispatcher servlet accepts the requests and decides which controller will be suitable to handle these requests. Then it dispatches the HTTP requests to specific controller.

![image](https://github.com/asmalizaa/javaspring/assets/23090837/1bae210f-cfab-4f89-8487-34b56ccc6306)

1. All the incoming requests are intercepted by the DispatcherServlet that works as the front controller.
2. The DispatcherServlet then gets an entry of handler mapping from the XML file and forwards the request to the controller.
3. The object of ModelAndView is returned by the controller.
4. The DispatcherServlet checks the entry of the view resolver in the XML file and invokes the appropriate view component.

![image](https://github.com/asmalizaa/javaspring/assets/23090837/c8cc88ad-6406-4ff8-9e5d-eb2cd11c5f54)

### Example

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/51d9a781-df7d-44cb-94ad-e59494d9ec44)

- FrontController - The FrontController is the initial contact point for handling requests in the system. It delegates to an ApplicationController to perform action and view management.
- ApplicationController - An ApplicationController is responsible for action and view management, including locating and routing to the specific actions that will service a request, and finding and dispatching to the appropriate view.
- Command - A Command performs the action that handles the request.
- View - A View represents the display returned to the client.

1. Create Views: HomeView, StudentView, ErrorView and DashboardView java classes.

   ```java
   package com.example.webdemo.front;

   public class HomeView {
   	public void show() {
   		System.out.println("Displaying Home Page");
   	}
   }
   ```
   ```java
   package com.example.webdemo.front;

   public class StudentView {
   	public void show() {
   		System.out.println("Displaying Student Page");
   	}
   }
   ```
   ```java
   package com.example.webdemo.front;

   public class ErrorView {
   	public void show() {
   		System.out.println("Displaying Error Page");
   	}
   }
   ```
   ```java
   package com.example.webdemo.front;

   public class DashboardView {
   	public void show() {
   		System.out.println("Displaying Dashboad Page");
   	}
   }
   ```

2. Create Dispatcher class: Dispatcher.java

   ```java
   package com.example.webdemo.front;

   public class Dispatcher {
   	private StudentView studentView;
   	private HomeView homeView;
   	private DashboardView dashboardView;
   	private ErrorView errorView;

   	public Dispatcher() {
   		studentView = new StudentView();
   		homeView = new HomeView();
   		dashboardView = new DashboardView();
   		errorView = new ErrorView();
   	}

   	public void dispatch(String request) {
   		if (request.equalsIgnoreCase("STUDENT")) {
   			studentView.show();
   		} else if (request.equalsIgnoreCase("DASHBOARD")) {
   			dashboardView.show();
   		} else if (request.equalsIgnoreCase("HOME")) {
   			homeView.show();
   		} else {
   			errorView.show();
   		}
   	}
   }
   ```

3. Create Front Controller class: FrontController.java

   ```java
   package com.example.webdemo.front;

   public class FrontController {
   	private Dispatcher dispatcher;

   	public FrontController() {
   		dispatcher = new Dispatcher();
   	}

   	private boolean isAuthenticUser() {
   		System.out.println("User is authenticated successfully.");
   		return true;
   	}

   	private void trackRequest(String request) {
   		System.out.println("Page requested: " + request);
   	}

   	public void dispatchRequest(String request) {
   		//log each request
   		trackRequest(request);

   		//authenticate the user
   		if (isAuthenticUser()) {
   			dispatcher.dispatch(request);
   		}
   	}
   }
   ```

4. Use the FrontController to demonstrate Front Controller Design Pattern: FrontControllerPatternDemo.java

   ```java
   package com.example.webdemo.front;

   public class FrontControllerPatternDemo {
   	public static void main(String[] args) {
   		FrontController frontController = new FrontController();
   		frontController.dispatchRequest("HOME");
   		frontController.dispatchRequest("STUDENT");
   		frontController.dispatchRequest("DASHBOARD");
   		frontController.dispatchRequest("ERROR");
   	}
   }
   ```
   
## A Request/Response Cycle

Reference: (https://tutorialspedia.com/how-java-spring-mvc-works-spring-mvc-request-flow-explained-step-by-step/#google_vignette)

Spring MVC request flow is depicted in the diagram below:

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/c5f81286-19e3-463d-a777-f8132b327a50)

1. Client requests for a page by specifying the Web URL for the page. E.g. (https://spring.io/)
2. Client request is intercepted by the Dispatcher Servlet also known as Front Controller. Dispatcher Servlet is a servlet specified in Web.XML file (for XML Based configurations) or in the Web Configuration class (for java based configurations).
3. Dispatcher Servlet uses URL Mapping Handler to find out the relevant controller class to which request should be passed for subsequent processing. For example, If you have a Controller defined for all requests by specifying “/” in the URL, all requests will be entertained by that controller.
4. Once Dispatcher Servlet has identified the Controller to be considered, it passes the client request to the controller.
5. The controller class is the main class controlling the business logic flow once request has been dispatched it it by dispatcher servlet. This class will implement the methods for different type of http requests (e.g. GET, POST) and all logic to call Service layer methods will reside in this controller class.
6. The controller class will also be responsible for returning the ModelAndView object back to the dispatcher servlet after getting all business logic executed and any data returned from DAO layer. ModelAndView object returned by the controller back to the controller specified both view and model objects.
7. Controller class is annotated by @Controller annotation.
8. After receiving ModelAndView object from the controller, Dispatcher Servlet now sends model object to view resolver to get the name of the view which needs to be rendered.
9. Once the view to be rendered has been identified, Dispatcher Servlet passes model object to the view. Model object contains the data which needs to be displayed in the view. View will be rendered with the model data. Views can be designed in any front-end technology.
10. This view is returned to the client and client can see the view and associated data on his browser.

## The Strategy Pattern

Reference: (https://www.geeksforgeeks.org/strategy-pattern-set-1/)

In computer programming, the strategy pattern (also known as the policy pattern) is a behavioral software design pattern that enables selecting an algorithm at runtime. Instead of implementing a single algorithm directly, code receives run-time instructions as to which in a family of algorithms to use.

In simpler terms, The Strategy Pattern allows you to define a family of algorithms, encapsulate each one of them, and make them interchangeable. This pattern lets the algorithm vary independently from clients that use it.

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/da86bc49-e162-422d-87a5-7e99bafa4b45)

### Characteristics of the Strategy Design Pattern?

The Strategy Design Pattern exhibits several key characteristics that make it distinctive and effective for managing algorithm variations in software systems:

- It defines a family of algorithms: The pattern allows you to encapsulate multiple algorithms or behaviors into separate classes, known as strategies.
- It encapsulates behaviors: Each strategy encapsulates a specific behavior or algorithm, providing a clean and modular way to manage different variations or implementations.
- It enables dynamic behavior switching: The pattern enables clients to switch between different strategies at runtime, allowing for flexible and dynamic behavior changes.
- It promotes object collaboration: The pattern encourages collaboration between a context object and strategy objects, where the context delegates the execution of a behavior to a strategy object.

Overall, the Strategy pattern is a useful design pattern that allows the behavior of an object to be selected dynamically at runtime, providing flexibility, modularity, and testability.

### Components of the Strategy Design Pattern

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/685a5b23-27cb-4aed-b7d0-7765bb1a4dd6)

1. Context

   The Context is a class or object that holds a reference to a strategy object and delegates the task to it.
   - It acts as the interface between the client and the strategy, providing a unified way to execute the task without knowing the details of how it’s done.
   - The Context maintains a reference to a strategy object and calls its methods to perform the task, allowing for interchangeable strategies to be used.

2. Strategy Interface

   The Strategy Interface is an interface or abstract class that defines a set of methods that all concrete strategies must implement.
   - It serves as a contract, ensuring that all strategies adhere to the same set of rules and can be used interchangeably by the Context.
   - By defining a common interface, the Strategy Interface allows for decoupling between the Context and the concrete strategies, promoting flexibility and modularity in the design.

3. Concrete Strategy

   Concrete Strategies are the various implementations of the Strategy Interface. Each concrete strategy provides a specific algorithm or behavior for performing the task defined by the Strategy Interface.
   - Concrete strategies encapsulate the details of their respective algorithms and provide a method for executing the task.
   - They are interchangeable and can be selected and configured by the client based on the requirements of the task.
     
4. Client

   The Client is responsible for selecting and configuring the appropriate strategy and providing it to the Context.
   - It knows the requirements of the task and decides which strategy to use based on those requirements.
   - The client creates an instance of the desired concrete strategy and passes it to the Context, enabling the Context to use the selected strategy to perform the task.

### Strategy Design Pattern Example

> Let’s consider a sorting application where we need to sort a list of integers. However, the sorting algorithm to be used may vary depending on factors such as the size of the list and the desired performance characteristics.

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/11a1d96f-13c8-4370-adcb-667414a54b27)

1. Context (SortingContext)

   ```java
   package com.example.webdemo.strategy;

   public class SortingContext {
   	private SortingStrategy sortingStrategy;

   	public SortingContext(SortingStrategy sortingStrategy) {
   		this.sortingStrategy = sortingStrategy;
   	}

   	public void setSortingStrategy(SortingStrategy sortingStrategy) {
   		this.sortingStrategy = sortingStrategy;
   	}

   	public void performSort(int[] array) {
   		sortingStrategy.sort(array);
   	}
   }
   ```

2. Strategy Interface (SortingStrategy)

   ```java
   package com.example.webdemo.strategy;

   public interface SortingStrategy {
   	void sort(int[] array);
   }
   ```

3. Concrete Strategies: BubbleSortStrategy, MergeSortStrategy, QuickSortStrategy 

   ```java
   package com.example.webdemo.strategy;

   public class BubbleSortStrategy implements SortingStrategy {

   	@Override
   	public void sort(int[] array) {
   		// Implement Bubble Sort algorithm
   		System.out.println("Sorting using Bubble Sort");
   	}
   }
   ```
   ```java
   package com.example.webdemo.strategy;

   public class MergeSortStrategy implements SortingStrategy {
   	@Override
   	public void sort(int[] array) {
   		// Implement Merge Sort algorithm
   		System.out.println("Sorting using Merge Sort");
   	}
   }
   ```
   ```java
   package com.example.webdemo.strategy;

   public class QuickSortStrategy implements SortingStrategy {
   	@Override
   	public void sort(int[] array) {
   		// Implement Quick Sort algorithm
   		System.out.println("Sorting using Quick Sort");
   	}
   }
   ```

4. Client Component

   ```java
   package com.example.webdemo.strategy;

   public class Client {
   	public static void main(String[] args) {
   		// Create SortingContext with BubbleSortStrategy
   		SortingContext sortingContext = new SortingContext(new BubbleSortStrategy());
   		int[] array1 = { 5, 2, 9, 1, 5 };
   		sortingContext.performSort(array1); // Output: Sorting using Bubble Sort

   		// Change strategy to MergeSortStrategy
   		sortingContext.setSortingStrategy(new MergeSortStrategy());
   		int[] array2 = { 8, 3, 7, 4, 2 };
   		sortingContext.performSort(array2); // Output: Sorting using Merge Sort

   		// Change strategy to QuickSortStrategy
   		sortingContext.setSortingStrategy(new QuickSortStrategy());
   		int[] array3 = { 6, 1, 3, 9, 5 };
   		sortingContext.performSort(array3); // Output: Sorting using Quick Sort
   	}
   }
   ```

5. Run the program and review the output.
