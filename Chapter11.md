# Configuring Spring MVC

## Configuring Message Sources

MessageSource is a powerful feature available in Spring applications. This helps application developers handle various complex scenarios with writing much extra code, such as environment-specific configuration, internationalization or configurable values.

One more scenario could be modifying the default validation messages to more user-friendly/custom messages.

### Spring MessageSource example

Reference: (https://zetcode.com/spring/messagesource/)

The following application contains messages in English and German language. It uses the built-in ResourceBundleMessageSource.

1. Create the message properties files.

   ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/8a51e329-e6aa-4e61-998e-5faa2bfcac9f)

   ```
   #labels.properties
   l1=Earth
   l2=Hello {0}, how are you?
   ```
   ```
   #labels_de.properties
   l1=Erde
   l2=Hallo {0}, wie geht's?
   ```

2. Create the configuration class.

   ```java
   package com.example.demo;

   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.context.support.ResourceBundleMessageSource;

   @Configuration
   public class AppConfig {

       @Bean
       public ResourceBundleMessageSource messageSource() {
           var source = new ResourceBundleMessageSource();
           source.setBasenames("messages/labels");
           source.setUseCodeAsDefaultMessage(true);

           return source;
       }
   }
   ```

   The AppConfig configures the ResourceBundleMessageSource. The setBasenames tells where to look for message definitions.

3. Create the application class.

   ```java
   package com.example.demo;

   import java.util.Locale;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.context.MessageSource;
   import org.springframework.context.annotation.AnnotationConfigApplicationContext;

   @SpringBootApplication
   public class Application {
       @Autowired
       private MessageSource messageSource;

       public static void main(String[] args) {
           AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(Application.class);

           Application app = ctx.getBean(Application.class);
           app.run();

           ctx.close();
       }

       public void run() {
           System.out.println("Translated messages:");
           System.out.println(String.format("{%s}", messageSource.getMessage("l1", null, Locale.GERMAN)));
           System.out.println(String.format("{%s}", messageSource.getMessage("l1", null, Locale.ENGLISH)));

           System.out.println("Translated parameterized messages:");
           System.out.println(
				String.format("{%s}", messageSource.getMessage("l2", new Object[] { "Paul Smith" }, Locale.GERMAN)));
           System.out.println(
				String.format("{%s}", messageSource.getMessage("l2", new Object[] { "Paul Smith" }, Locale.ENGLISH)));
       }
   }
   ```

   The application prints plain messages and parameterized messages to the console.

## Spring @ControllerAdvice and @ExceptionHandler Example

Reference: (https://howtodoinjava.com/spring-core/spring-exceptionhandler-annotation/)

- Exception handling is a very essential feature of any Java application. Every good open-source framework, such as Spring Boot, allows writing the exception handlers in such a way that we can separate them from our application code. Well, Spring framework also allows us to do so using the annotations @ControllerAdvice and @ExceptionHandler.
- The @ControllerAdvice annotation is used to define a class that will handle exceptions globally across all controllers. Its methods are annotated with @ExceptionHandler, @InitBinder, and @ModelAttribute annotations.
The @ExceptionHandler annotation is used to handle specific exceptions. The annotated method is invoked when the specified exceptions are thrown from a @Controller. We can define these methods either in a @Controller class or in @ControllerAdvice class.

```java
@ControllerAdvice
	public class GlobalExceptionHandler {
		@ExceptionHandler(ApplicationException.class)
		public ResponseEntity<String> handleApplicationException(ApplicationException ex) {
			return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(ex.getMessage());
		}
}
```
   
### Local Exception Handler vs. Global Exception Handler

A local exception handler is created with the @ExceptionHandler method inside a @Controller (or @RestControllerAdvice) class, it will handle the exceptions thrown from the handler methods within the controller class only.

```java
@Controller
public class PageController {

  @RequestMapping(value = "/{id}", method = RequestMethod.GET)
  public ModelAndView getPage(@PathVariable("id") String id) throws Exception {

    Page page = ...;
    if(page == null) {
      throw new RecordNotException("Page not found for id : " + id);
    }
    return new ModelAndView("index");
  }

  @ExceptionHandler(NullPointerException.class)
  public ModelAndView handleNullPointerException(NullPointerException ex) {
    ModelAndView modelAndView = new ModelAndView();
    modelAndView.setViewName("error");
    modelAndView.addObject("message", ex.getMessage());
    return modelAndView;
  }

  @ExceptionHandler(RecordNotException.class)
  public ModelAndView handleException(RecordNotException ex) {
    ...
    return modelAndView;
  }
}
```
   
A global exception handler is defined using the @ControllerAdvice (or @RestControllerAdvice) annotation on a class and @ExceptionHandler annotation on methods. The methods will be invoked when the specified exception is thrown from any of the @Controller (or @RestController) handler methods.

```java
@ControllerAdvice
public class GlobalExceptionHandler {

  @ExceptionHandler(NullPointerException.class)
  public ModelAndView handleNullPointerException(NullPointerException ex) {
    //...
  }

  @ExceptionHandler(RecordNotException.class)
  public ModelAndView handleException(RecordNotException ex) {
    //...
  }
}
```

Now every time, any controller encounters NullPointerException in the request processing, control will automatically come to the handleNullPointerException() method.

### Spring @ExceptionHandler

When we annotate a method with @ExceptionHandler annotation, Spring configuration will detect this annotation and register the method as an exception handler for the argument exception class and its subclasses.

**Method Arguments**

Exception handler methods that are annotated with this annotation are allowed to have very flexible signatures. They can accept arguments of different types. These additional method arguments allow us to access relevant context information related to the exception and the request thus generating more informative error responses or performing additional actions.

For example:
- Specified exception type
- HttpServletRequest and HttpServletResponse
- WebRequest
- Principal and Authentication
- HttpSession
- Locale
- Model

```java
@ExceptionHandler(NullPointerException.class)
public ModelAndView handleException(NullPointerException ex, HttpServletRequest request, Model model) {
	...
}
```

**Return Types**

Similar to arguments, return types can be of different types. The return type determines how the error response will be generated and sent back to the client.

The commonly used return types are:
- ResponseEntity
- ModelAndView
- String (View Name)
- ResponseBody
- Void (writing the response content directly to HttpServletResponse)

In the following example, ErrorResponse is a new feature and exposes HTTP error response details, including HTTP status, response headers, and a body of type ProblemDetail.

```java
@ExceptionHandler(AppException.class)
public ModelAndView handleAppException(AppException ex) {

    ModelAndView modelAndView = new ModelAndView("errorPage");
    modelAndView.addObject("errorMessage", ex.getMessage());
    return modelAndView;
}

@ExceptionHandler(DataNotFoundException.class)
@ResponseBody
public ErrorResponse handleDataNotFoundException(DataNotFoundException ex) {

    return new ErrorResponse(ex.getMessage());
}
```

We may combine the ExceptionHandler annotation with @ResponseStatus for a specific HTTP error status.

```java
@ExceptionHandler(AppException.class)
@ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
public String handleAppException(AppException ex) {

    return "errorPage";
}
```

**Handling Multiple Exceptions**

As mentioned earlier, the above exception handler will handle all exceptions, either instance of a given class or sub-classes of argument exceptions. But, if we want to configure @ExceptionHandler for multiple exceptions of different types, then we can specify all such exceptions in form of an array.

```java
@ExceptionHandler({NullPointerException.class, ArrayIndexOutOfBoundsException.class, IOException.class})
public ModelAndView handleException(Exception ex) {

	ModelAndView modelAndView = new ModelAndView();
	modelAndView.setViewName("generic-error");
	modelAndView.addObject("message", ex.getMessage());
	return modelAndView;
}
```

### Spring @ControllerAdvice

If we want to centralize the exception-handling logic to one class that is capable of handling exceptions thrown from any handler class/controller class – then we can use @ControllerAdvice annotation.

By default, the methods in an @ControllerAdvice apply globally to all controllers. We can create a class and add @ControllerAdvice annotation on top. Then add @ExceptionHandler methods for each type of specific exception class in it.

```java
@ControllerAdvice
public class CustomExceptionHandler extends ResponseEntityExceptionHandler 
{
  @ExceptionHandler(Exception.class)
  public final ResponseEntity<ErrorResponse> handleAllExceptions(Exception ex, WebRequest request) {

    List<String> details = new ArrayList<>();
    details.add(ex.getLocalizedMessage());
    ErrorResponse error = new ErrorResponse(ApplicationConstants.SERVER_ERROR, details);
    return new ResponseEntity<>(error, HttpStatus.INTERNAL_SERVER_ERROR);
  }
 
  @ExceptionHandler(RecordNotFoundException.class)
  public final ResponseEntity<ErrorResponse> handleUserNotFoundException(RecordNotFoundException ex,                       WebRequest request) {

    List<String> details = new ArrayList<>();
    details.add(ex.getLocalizedMessage());
    ErrorResponse error = new ErrorResponse(ApplicationConstants.RECORD_NOT_FOUND, details);
    return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);
  }
}
```

**ResponseEntityExceptionHandler**

In the above example, we extended the exception handler class with ResponseEntityExceptionHandler. It is a convenient base class for @ControllerAdvice classes. It’s designed specifically for handling exceptions in a RESTful API context where we need to return different types of responses based on the exception type.

ResponseEntityExceptionHandler provides exception handlers for internal Spring exceptions. If we don’t extend it, then all the exceptions will be redirected to DefaultHandlerExceptionResolver which returns a ModelAndView object which is typically used in a traditional MVC setup for rendering views.

However, when building RESTful APIs, returning ModelAndView objects might not be suitable, as APIs typically require structured data formats like JSON or XML for error responses. That’s where ResponseEntityExceptionHandler comes in.

## Demo

For the demo, the below handler method is intentionally returning NullPointerException.

```
@RequestMapping(value="/demo/not-exist", method = RequestMethod.GET,  headers="Accept=*/*")
public @ResponseBody ModelAndView oneFaultyMethod()
{
  if(true)
  {
    throw new NullPointerException("This error message if for demo only.");
  }
  return null;
}
```

If we deploy the above application and hit the URL [/SpringApplication/users/demo/not-exist] in the browser, it will show the “error” page as configured in the first section.

```
< %@ taglib prefix="c" uri="http://java.sun.com/jstl/core" %>
< %@ taglib prefix="x" uri="http://java.sun.com/jstl/xml" %>
< %@ taglib prefix="fmt" uri="http://java.sun.com/jstl/fmt" %>
< %@ taglib prefix="sql" uri="http://java.sun.com/jstl/sql" %>
<html>
 
  <head>
    <title>This is sample error page</title>
  </head>
  <body>
    <h1>This is sample error page : <c:out value="${message}"></c:out></h1>
  </body>
<html>
```

Below will be the output in the browser.

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/baa376f1-9f7c-47e7-8f8e-d768a5988517)

