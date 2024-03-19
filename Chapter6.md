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

