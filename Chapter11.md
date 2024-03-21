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

## Example

In this example, we are going to customize error message using MessageSource, the use Local Exception Handler.

1. Create error messages properties files: errormessages.properties and errormessages_de.properties

   ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/9f4b1d27-d039-4cf3-b16a-43813467afe0)

   ```
   #errormessages.properties
   nullpointerexception=Testing NullPointerException in Local Exception Handler
   ```

   ```
   #errormessages_de.properties
   nullpointerexception=Testen von NullPointerException im lokalen Ausnahmehandler
   ```

2. Update the configuration class to load the resource bundle message source.

   ```java
   package com.example.model;

   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.ComponentScan;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.context.support.ResourceBundleMessageSource;
   import org.springframework.web.servlet.ViewResolver;
   import org.springframework.web.servlet.config.annotation.EnableWebMvc;
   import org.springframework.web.servlet.view.InternalResourceViewResolver;

   @Configuration
   @EnableWebMvc
   @ComponentScan("com.example.model")
   public class BookConfig {
   	@Bean
   	public ViewResolver viewResolver() {
   		InternalResourceViewResolver resolver = new InternalResourceViewResolver();
   		resolver.setPrefix("/WEB-INF/jsp/");
   		resolver.setSuffix(".jsp");
   		resolver.setOrder(1);
   		return resolver;
   	}

   	@Bean
   	public ResourceBundleMessageSource messageSource() {
   		var source = new ResourceBundleMessageSource();
   		source.setBasenames("messages/errormessages");
   		source.setUseCodeAsDefaultMessage(true);
   		return source;
   	}
   }
   ```
3. Create the application class.

   ```java
   package com.example.model;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;

   @SpringBootApplication
   public class BookApp {
   	public tatic void main(String[] args) {
   		SpringApplication.run(BookApp.class, args);
   	}
   }
   ```
4. Create the controller class. In this class, we added codes to simulate exception being thrown.

   ```java
   package com.example.model;

   import java.util.Locale;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.context.MessageSource;
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.ExceptionHandler;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.servlet.ModelAndView;

   @Controller
   public class BookController {
   	@Autowired
   	private MessageSource messageSource;

   	@GetMapping("/test")
   	public void test() throws NullPointerException {
   		throw new NullPointerException(messageSource.getMessage("nullpointerexception", null, Locale.ENGLISH));
   	}

   	// configure exception handler that only applicable to this controller
   	@ExceptionHandler(NullPointerException.class)
   	public ModelAndView handleNullPointerException(NullPointerException ex) {
   		System.out.println("executing handleNullPointerException...");
   		ModelAndView modelAndView = new ModelAndView();
   		modelAndView.setViewName("error");
   		modelAndView.addObject("message", ex.getMessage());
   		return modelAndView;
   	}
   }
   ```
5. Updated the error.jsp page to display the error message.

   ```
   <html>
   <head></head>
   <body>
   	<h3>Please enter the correct details</h3>
   	<table>
   		<tr>
   			<td>${message}</td>
   		</tr>
   		<tr>
   			<td><a href="employee">Retry</a></td>
   		</tr>
   	</table>
   </body>
   </html>
   ```
6. Run the application. Launch browser and type (http://localhost:8080/test) Verify the error page displayed.

   ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/0185ccb9-cecf-4d25-b91b-2430aab286c3)
