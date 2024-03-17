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

Enter the Framework

Spring Value Proposition

The Spring Container

Aspect-Oriented Programming
 
