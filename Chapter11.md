# Configuring Spring MVC

## Configuring Message Sources

MessageSource is a powerful feature available in Spring applications. This helps application developers handle various complex scenarios with writing much extra code, such as environment-specific configuration, internationalization or configurable values.

One more scenario could be modifying the default validation messages to more user-friendly/custom messages.

### Spring MessageSource example

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

   
   

   
