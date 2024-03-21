# Interceptors

Reference: (https://jakarta.ee/learn/docs/jakartaee-tutorial/current/supporttechs/interceptors/interceptors.html)

Interceptors are used in conjunction with Jakarta EE managed classes to allow developers to invoke interceptor methods on an associated target class, in conjunction with method invocations or lifecycle events. Common uses of interceptors are logging, auditing, and profiling.

You can use interceptors with session beans, message-driven beans, and CDI managed beans. In all of these cases, the interceptor target class is the bean class.

An interceptor can be defined within a target class as an interceptor method, or in an associated class called an interceptor class. Interceptor classes contain methods that are invoked in conjunction with the methods or lifecycle events of the target class.

Interceptor classes and methods are defined using metadata annotations, or in the deployment descriptor of the application that contains the interceptors and target classes.

## Interceptor Classes


Interceptor classes may be designated with the optional jakarta.interceptor.Interceptor annotation, but interceptor classes are not required to be so annotated. An interceptor class must have a public, no-argument constructor.

The target class can have any number of interceptor classes associated with it. The order in which the interceptor classes are invoked is determined by the order in which the interceptor classes are defined in the jakarta.interceptor.Interceptors annotation. However, this order can be overridden in the deployment descriptor.

Interceptor classes may be targets of dependency injection. Dependency injection occurs when the interceptor class instance is created, using the naming context of the associated target class, and before any @PostConstruct callbacks are invoked.

## Interceptor Lifecycle

Interceptor classes have the same lifecycle as their associated target class. When a target class instance is created, an interceptor class instance is also created for each declared interceptor class in the target class. That is, if the target class declares multiple interceptor classes, an instance of each class is created when the target class instance is created. The target class instance and all interceptor class instances are fully instantiated before any @PostConstruct callbacks are invoked, and any @PreDestroy callbacks are invoked before the target class and interceptor class instances are destroyed.

## Example

Reference: (https://www.geeksforgeeks.org/spring-boot-interceptor/)

1. Create a new project using Spring Initialzr with below configurations.

   - Project: Maven
   - Language: Java
   - Spring Boot: default
   - Group: default
   - Artifact: interceptordemo
   - Name: interceptordemo
   - Description: default
   - Package Name: com.example.interceptordemo
   - Packaging: jar
   - Java: 21 (or choose the version installed on your machine)
   - Dependencies: Spring Web, Spring Boot DevTools

   Generate the project. Once the generated file downloaded, extract and import into your eclipse.

2. The Model.

   ```java
   package com.example.interceptordemo;

   public class Student {
       private int id;
       private String firstName;
       private String lastName;

       public Student() {
       }

       public Student(int id, String firstName, String lastName) {
           this.id = id;
           this.firstName = firstName;
           this.lastName = lastName;
       }

       public int getId() {
           return id;
       }

       public void setId(int id) {
           this.id = id;
       }

       public String getFirstName() {
           return firstName;
       }

       public void setFirstName(String firstName) {
           this.firstName = firstName;
       }

       public String getLastName() {
           return lastName;
       }

       public void setLastName(String lastName) {
           this.lastName = lastName;
       }

       @Override
       public String toString() {
           return "Id: " + getId() + "\nFirst Name: " + getFirstName() + "\nLast Name: " + getLastName() + "\n";
       }
   }
   ```

3. Configuration class.

   ```java
   package com.example.interceptordemo;

   import org.springframework.context.annotation.Configuration;
   import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
   import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

   @Configuration
   public class RequestInterceptorConfig implements WebMvcConfigurer {

       // Register an interceptor with the registry, Interceptor name : RequestInterceptor
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
           registry.addInterceptor(new RequestInterceptor());
       }
       // * We can register any number of interceptors with our spring application context
   }
   ```

4. Request Interceptor class.

   Request Interceptor is an additional component class that intercepts all the incoming and outgoing requests (before any action is performed). It has the following 3 methods :

   1. preHandle(): When an interceptor is implemented, any request before reaching the desired controller will be intercepted by this interceptor and some pre-processing can be performed like logging, authentication, redirection, etc.

      - This method takes 3 parameters :
        - HttpServletRequest request – represents the request being handled,
        - HttpServletResponse response – represents the HTTP response to be sent back to the client,
        - Object handler – the target controller method that will handle this request.
      - Boolean return type: If the method returns true then the request will be directed towards the target control else the target controller method won’t be invoked if this method returns false and the request will be halted.
    2. postHandle(): This method is executed after the request is served but just before the response is sent back to the client. It intercepts the request in the final stage, giving us a chance to make any final trivial adjustments.

       - We can modify the view response, for certain specific conditions.
       - It takes 4 parameters:
         - 3 are same as previous but there is one more
         - 'ModelAndView'. It contains information about the model (data that is shipped across the parts of our web application) and the view that is rendered by the client.
       - It can be used for debugging, logging, and capturing final response data.

   3. afterCompletion(): This method is executed after the request and response mechanism is completed.
      - This method can turn out to be very useful in cleaning up the resources once the request is served completely.
      - It also takes 4 parameters, but the 'ModelAndView' object is replaced by an Exception object which contains information if any Exceptions occurred while serving the request.

@Component annotation tells the component scanning mechanism that this class should be registered for component scanning.

   ```java
   package com.example.interceptordemo;

   import org.springframework.stereotype.Component;
   import org.springframework.web.servlet.HandlerInterceptor;
   import org.springframework.web.servlet.ModelAndView;
   import jakarta.servlet.http.HttpServletRequest;
   import jakarta.servlet.http.HttpServletResponse;

   @Component
   public class RequestInterceptor implements HandlerInterceptor {
        // Request is intercepted by this method before reaching the Controller
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {

		// * Business logic just when the request is received and intercepted by this
		// interceptor before reaching the controller
		try {
			System.out.println("1 - preHandle() : Before sending request to the Controller");
			System.out.println("Method Type: " + request.getMethod());
			System.out.println("Request URL: " + request.getRequestURI());
		}
		// * If the Exception is caught, this method will return false
		catch (Exception e) {
			e.printStackTrace();
			return false;
		}
		return true;
	}

	// Response is intercepted by this method before reaching the client
	@Override
	public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
			ModelAndView modelAndView) throws Exception {
		// * Business logic just before the response reaches the client and the request
		// is served
		try {
			System.out.println(
					"2 - postHandle() : After the Controller serves the request (before returning back response to the client)");
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

	// This method is called after request & response HTTP communication is done.
	@Override
	public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
			throws Exception {
		// * Business logic after request and response is Completed
		try {
			System.out.println("3 - afterCompletion() : After the request and Response is completed");
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
   ```

