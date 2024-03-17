# Overview of Spring

## Java EE: The Good, The Bad, and the Ugly

**Jakarta EE**, formerly **Java Platform, Enterprise Edition (Java EE)** and **Java 2 Platform, Enterprise Edition (J2EE)**, is a set of specifications, extending Java SE with specifications for enterprise features such as distributed computing and web services. Jakarta EE applications are run on reference runtimes, which can be microservices or application servers, which handle transactions, security, scalability, concurrency and management of the components they are deploying.

### What is Jakarta EE?

Jakarta EE is a collection of services that help you write enterprise applications that run on the Java Platform. It provides the infrastructure often required for these applications so you that you can focus on core features and business logic.

Enterprise applications support the high levels of security, scalability, and reliability that large organizations typically require. They can be web services, web applications, batch processes, and so on.

The Java Platform consists of the Java Virtual Machine, compiler, standard APIs, and several tools that help you build, deploy, and debug Java applications. Java is the primary programming language, although the Java Platform supports several others, including Kotlin and Scala. Most Jakarta EE applications, however, are written in Java. The Java Platform is also called Java Standard Edition (SE).

A key benefit of Jakarta EE is that it’s a set of open source standards supported by multiple vendors. Each vendor has their own implementation of the standards, and the vendors work together to update the standards over time. This means that you aren’t locked into a particular vendor, and each standard is well-thought-out and based on existing patterns and practices.

Jakarta EE is stable and mature, and used in tens of thousands of mission-critical enterprises applications worldwide; yet it has evolved over time to keep up with modern computing trends, such as cloud computing.


## The Jakarta EE Services

Jakarta EE provides a wide range of services, organized into three main categories. Each category is implemented by one of three profiles. 
- The **Core** profile contains the foundational services.
- The **Web** profile contains the Core profile plus services for writing web applications.
- The **Platform** contains the Core and Web profiles, plus additional services for mail, batch processing, and messaging, and more.

The table below lists each high-level service, the specification that provides it, and the profile that contains it:

<table>
    <tr><th>Service</th><th>Specification</th><th>Profile</th></tr>
    <tr>
        <td>Dependency Injection</td>
        <td>Jakarta Contexts and Dependency Injection (CDI) Lite</td>
        <td>Core</td>
    </tr>
    <tr>
        <td>RESTful web services</td>
        <td>Jakarta REST (formerly JAX-RS)</td>
        <td>Core</td>
    </tr>
    <tr>
        <td>JSON processing</td>
        <td>Jakarta JSON Binding (JSON-B) and Jakarta JSON Processing (JSON-P)</td>
        <td>Core</td>
    </tr>
    <tr>
        <td>Advanced dependency injection</td>
        <td>Jakarta Contexts and Dependency Injection (CDI) Full</td>
        <td>web</td>
    </tr>
    <tr>
        <td>Validation</td>
        <td>Jakarta Validation (Bean Validation)</td>
        <td>Web</td>
    </tr>
    <tr>
        <td>Security</td>
        <td>Jakarta Security</td>
        <td>Web</td>
    </tr>
    <tr>
        <td>HTTP request handling</td>
        <td>Jakarta Servlet</td>
        <td>Web</td>
    </tr>
    <tr>
        <td>Server-side web applications</td>
        <td>Jakarta Faces (formerly JavaServer Faces, or JSF)</td>
        <td>Web</td>
    </tr>
    <tr>
        <td>WebSocket request handling</td>
        <td>Jakarta WebSocket</td>
        <td>Web</td>
    </tr>
    <tr>
        <td>Relational data persistence</td>
        <td>Jakarta Persistence (formerly JPA)</td>
        <td>Web</td>
    </tr>
    <tr>
        <td>Application components</td>
        <td>Jakarta Enterprise Beans Lite (formerly EJB Lite)</td>
        <td>Web</td>
    </tr>
    <tr>
        <td>Transactions</td>
        <td>Jakarta Transactions</td>
        <td>Web</td>
    </tr>
    <tr>
        <td>Managed concurrency</td>
        <td>Jakarta Concurrency</td>
        <td>Web</td>
    </tr>
    <tr>
        <td>Email handling</td>
        <td>Jakarta Mail</td>
        <td>Platform</td>
    </tr>
    <tr>
        <td>Messaging</td>
        <td>Jakarta Messaging</td>
        <td>Platform</td>
    </tr>
    <tr>
        <td>Batch Processing</td>
        <td>Jakarta Batch</td>
        <td>Platform</td>
    </tr>
</table>

As you work with Jakarta EE, you’ll encounter other services that support the ones listed above.

Each service is provided by one or more APIs, which are defined in Jakarta EE specifications. The specifications provide details for users and implementors of the service. You can find a [full list of the Jakarta EE specifications](https://jakarta.ee/specifications/) on the Jakarta EE site.

## Jakarta EE Containers and Servers

The services we discussed in Jakarta EE Services section are provided by container. 
- A Jakarta EE Server is software that runs the container, deploys applications into it, and provides administration and additional features.
- Jakarta EE Servers run stand-alone, but many implementations also support the ability to generate a single "fat", executable Java Archive (JAR) file that includes all the code necessary to run the application.

> Container is a commonly used term in computing, but it basically means "that which holds something". In the case of Jakarta EE, the container "holds," or more accurately, executes your code and provides low-level application services, such as dependency injection, transactions, HTTP request handling, REST support, and so on.

The figure below shows how the application code, container, and Jakarta EE Server are related:

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/76dd17b2-657a-43fd-804d-6c680059306c)

In a simple case, an application might look like this:

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/bdb3a56e-d492-453f-9320-5587ea1f5dda)

However, a single Jakarta EE Server can run multiple applications, and applications can communicate with each other remotely. So you can create more complicated scenarios, as shown below:

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/e4726034-ab28-488c-abef-97ec60f55c6f)

> Even though the figures above show communication between multiple Jakarta EE applications, they can and often do communicate with non-Jakarta EE applications via messaging or streaming servers, REST, and so on.

Regardless of how simple or complicated your system architecture is, a key benefit of Jakarta EE is that container services are configurable. This means the same component can behave differently based on where it’s deployed. The container also manages non-configurable services, such as the lifecycle of components, database connection resource pooling, data persistence, and access to the Jakarta EE APIs.

### Jakarta EE Components

Jakarta EE applications are made up of components. 
- A Jakarta EE component is a self-contained functional unit that makes use of one or more container services.
- That usage could be as simple as injecting a single dependency or responding to REST requests, or as complicated as injecting multiple dependencies, querying a database, firing and event, and participating in distributed transactions.

Jakarta EE supports the following components:
- Web components interact with web standards (HTTPS, HTML, WebSocket, and so on) using Jakarta Servlet, Jakarta Faces, Jakarta WebSocket, and related technologies.
- Business components implement logic necessary to support the business domain using either Enterprise bean components (enterprise beans) or CDI managed beans.
  - For new applications, business components should be implemented using CDI managed beans, unless you need Enterprise bean-specific features such as transactions, role-based security, messaging driven beans, or remote execution. The two technologies play well together.

All of these components run on the server, and are ordinary Java classes written with Jakarta EE annotations (or optionally, external configuration files called deployment descriptors).

### How do I get Jakarta EE?

Since Jakarta EE is an open-source industry standard, there are multiple implementations. A good place to start is the [Jakarta EE Starter](https://start.jakarta.ee/), which lets you quickly generate a sample starter app using one of the supported Jakarta EE servers. You can also find the most recent list of servers on the [Jakarta EE website](https://jakarta.ee/compatibility/).

## Enter the Framework

Prior to the advent of Enterprise Java Beans (EJB), Java developers needed to use JavaBeans to create Web applications. 
- Although JavaBeans helped in the development of user interface (UI) components, they were not able to provide services, such as transaction management and security, which were required for developing robust and secure enterprise applications.
- The advent of EJB was seen as a solution to this problem EJB extends the Java components, such as Web and enterprise components, and provides services that help in enterprise application development.
- However, developing an enterprise application with EJB was not easy, as the developer needed to perform various tasks, such as creating Home and Remote interfaces and implementing lifecycle callback methods which lead to the complexity of providing code for EJBs.
- Due to this complication, developers started looking for an easier way to develop enterprise applications.

## Spring Value Proposition

The Spring framework (which is commonly known as Spring) has emerged as a solution to all these complications. 
- This framework uses various new techniques such as Aspect-Oriented Programming (AOP), Plain Old Java Object (POJO), and dependency injection (DI), to develop enterprise applications, thereby removing the complexities involved while developing enterprise applications using EJB.
- Spring is an open source lightweight framework that allows Jakarta EE developers to build simple, reliable, and scalable enterprise applications.
- This framework mainly focuses on providing various ways to help you manage your business objects.
- It made the development of Web applications much easier as compared to classic Java frameworks and Application Programming Interfaces (APIs), such as Java database connectivity(JDBC), JavaServer Pages(JSP), and Java Servlet.
- The Spring framework can be considered as a collection of sub-frameworks, also called layers, such as Spring AOP, Spring Object-Relational Mapping (Spring ORM), Spring Web Flow, and Spring Web MVC.
- It is a lightweight application framework used for developing enterprise applications.
- You can use any of these modules separately while constructing a Web application.
- The modules may also be grouped together to provide better functionalities in a Web application.
- Spring framework is loosely coupled because of dependency Injection.

A key element of Spring is infrastructural support at the application level: Spring focuses on the "plumbing" of enterprise applications so that teams can focus on application-level business logic, without unnecessary ties to specific deployment environments.

## The Spring Framework Modules

The Spring Framework consists of features organized into about 20 modules. These modules are grouped into Core Container, Data Access/Integration, Web, AOP (Aspect Oriented Programming), Instrumentation, and Test, as shown in the following diagram.

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/d026c166-89e2-49e4-b474-9a67941fdc01)

For more details, please go to [this official page](https://docs.spring.io/spring-framework/docs/3.0.x/spring-framework-reference/html/overview.html).

### Container overview

The interface org.springframework.context.ApplicationContext represents the Spring IoC container and is responsible for instantiating, configuring, and assembling the aforementioned beans. 
- The container gets its instructions on what objects to instantiate, configure, and assemble by reading configuration metadata.
- The configuration metadata is represented in XML, Java annotations, or Java code.
- It allows you to express the objects that compose your application and the rich interdependencies between such objects.

The following diagram is a high-level view of how Spring works. Your application classes are combined with configuration metadata so that after the ApplicationContext is created and initialized, you have a fully configured and executable system or application.

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/661fc117-dc9b-4d26-9e2d-76e7a58e411c)

## Aspect-Oriented Programming
 
Aspect-Oriented Programming (AOP) complements Object-Oriented Programming (OOP) by providing another way of thinking about program structure. 
- The key unit of modularity in OOP is the class, whereas in AOP the unit of modularity is the aspect.
- **Aspects** enable the modularization of concerns such as transaction management that cut across multiple types and objects.
- Such concerns are often termed **crosscutting** concerns in AOP literature.

One of the key components of Spring is the AOP framework. While the Spring IoC container does not depend on AOP, meaning you do not need to use AOP if you don't want to, AOP complements Spring IoC to provide a very capable middleware solution.

### AOP Concepts

<table>
    <tr><th>Concept</th><th>Description</th></tr>
    <tr>
        <td>Aspect</td>
        <td>A modularization of a concern that cuts across multiple classes. Transaction management is a good example of a crosscutting concern in enterprise Java applications. In Spring AOP, aspects are implemented using regular classes (the schema-based approach) or regular classes annotated with the @Aspect annotation (the @AspectJ style).</td>
    </tr>
    <tr>
        <td>Join point</td>
        <td>A point during the execution of a program, such as the execution of a method or the handling of an exception. In Spring AOP, a join point always represents a method execution.</td>
    </tr>
    <tr>
        <td>Advice</td>
        <td>Action taken by an aspect at a particular join point. Different types of advice include "around," "before" and "after" advice. (Advice types are discussed below.) Many AOP frameworks, including Spring, model an advice as an interceptor, maintaining a chain of interceptors around the join point.</td>
    </tr>
    <tr>
        <td>Pointcut</td>
        <td>A predicate that matches join points. Advice is associated with a pointcut expression and runs at any join point matched by the pointcut (for example, the execution of a method with a certain name). The concept of join points as matched by pointcut expressions is central to AOP, and Spring uses the AspectJ pointcut expression language by default.</td>
    </tr>
    <tr>
        <td>Introduction</td>
        <td>Declaring additional methods or fields on behalf of a type. Spring AOP allows you to introduce new interfaces (and a corresponding implementation) to any advised object. For example, you could use an introduction to make a bean implement an IsModified interface, to simplify caching. (An introduction is known as an inter-type declaration in the AspectJ community.)</td>
    </tr>
    <tr>
        <td>Target object</td>
        <td>Object being advised by one or more aspects. Also referred to as the advised object. Since Spring AOP is implemented using runtime proxies, this object will always be a proxied object.</td>
    </tr>
    <tr>
        <td>AOP proxy</td>
        <td>An object created by the AOP framework in order to implement the aspect contracts (advise method executions and so on). In the Spring Framework, an AOP proxy will be a JDK dynamic proxy or a CGLIB proxy.</td>
    </tr>
    <tr>
        <td>Weaving</td>
        <td>Linking aspects with other application types or objects to create an advised object. This can be done at compile time (using the AspectJ compiler, for example), load time, or at runtime. Spring AOP, like other pure Java AOP frameworks, performs weaving at runtime.</td>
    </tr>
</table>

Types of advice.

<table>
    <tr><th>Type</th><th>Description</th></tr>
    <tr>
        <td>Before advice</td>
        <td>Advice that executes before a join point, but which does not have the ability to prevent execution flow proceeding to the join point (unless it throws an exception).</td>
    </tr>
    <tr>
        <td>After returning advice</td>
        <td>Advice to be executed after a join point completes normally: for example, if a method returns without throwing an exception.</td>
    </tr>
    <tr>
        <td>After throwing advice</td>
        <td>Advice to be executed if a method exits by throwing an exception.</td>
    </tr>
    <tr>
        <td>After (finally) advice</td>
        <td>Advice to be executed regardless of the means by which a join point exits (normal or exceptional return).</td>
    </tr>
    <tr>
        <td>Around advice</td>
        <td>Advice that surrounds a join point such as a method invocation. This is the most powerful kind of advice. Around advice can perform custom behavior before and after the method invocation. It is also responsible for choosing whether to proceed to the join point or to shortcut the advised method execution by returning its own return value or throwing an exception.</td>
    </tr>
</table>
