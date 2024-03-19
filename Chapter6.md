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

