This document outlines the coding standards / conventions all swagger-api developers _must_ adhere to when writing/committing code. In general - the standard [Google Java Code Conventions](https://google-styleguide.googlecode.com/svn/trunk/javaguide.html) should be adhered to - with the below additions/exceptions:

### Code Formatting
You are free to use any IDE or text editor you like, as long as you conform to the code-formatting standards. Java's syntax allows you to do many things as you want, many of which are controversial or up for discussion. But generally we have decided on a set of team-specific standards that the code-formatting settings will follow when auto-indenting. 

* [Eclipse Formatting Rules](http://swagger.io/wp-content/uploads/2015/05/eclipse-formatting.zip)
* [IntelliJ IDEA Formatting Rules](http://swagger.io/wp-content/uploads/2015/05/idea-settings.zip)

####New scope on same line or new line
**Do this**
```java
   if(bool) {
      //Dostuff
   }
```

Not this
```java
   if(bool)
   {
      //Dostuff
   }
```
Reason: 
It's more common-practice to have the curly-bracket that declares scope on the same line as the statement.

####Block-less loops and if-statements
**Do this**
```java
   for (int i = 0; i < 10; i++){
      //Do stuff inside loop
   }
   //Do stuff outside of loop
```

Not this
```java
   for (int i = 0; i < 10; i++)
      //Do stuff inside loop
   //Do stuff outside loop
```
Reason: 
Readability and consistency, having curly-brackets on statements like this makes it more readable. Especially if it's the only standard being used instead of both.


####Interface-naming
**Do this**
```java
  ChocolateFactory implements CandyFactory  
```

not this
```java
  ChocolateFactory implements ICandyFactory  
```

or this
```java
  ChocolateFactory implements GenericCandyFactory 
```

or this
```java
  ChocolateFactory implements CandyFactoryInterface  
```

or this
```java
  CandyFactoryImpl implements CandyFactory
```

Reason: 
We want to make the code sound as similar to English as possible, so we should avoid prefixes and suffixes when we can. Also, any modern IDE makes it very easy to see if a type is an interface or a class, so we don't need a marker on the type name to demonstrate that to us. 

####Wildcard Imports

**Do this**
```java
   import java.util.Collections;
   import java.util.HashMap;
   import java.util.HashSet;
   import java.util.List;
   import java.awt.List;
```

Don't do this
```java
   import java.util.*;
   import java.awt.List;
```

Reason: 
Generally, it's nice to see exactly what we're importing. 

### Comments
**Do this**

```java
   //This statement is hard to understand, so i should make it clear by a comment 
   if({ v -> v.contains(elem)}.call(1)) 
```

**and this**
```java
   //TODO this part contains a known bug that is too hard to fix right now, it is reported as API-0001
```

Don't do this
```java
   //Here we check bool and it that is true we set var to 1, otherwise we set it to -1 and return it. 
   int var = 0;
   if(bool) {
      var = 1;
   }else{
      var = -1;
   }
   return var;
```
Or this
```java
   //TODO Please fix later, because i don't have time or want to take the effort to fix it. 
   //FIXME even though this comment probably took more time to write than to do it right. 
```

or this
```java 
   //--------------------------------Function-----------------------------------
```

or this
```java 
   //Ask Osten
```
Reason: 
Comment-cluttered code is harder to read. Also comments tend to get outdated and move around, which makes them very confusing.

Generally, a comment should explain __why__ you're doing something in a certain way, not __what__ the code does.

Most of the time the code can be made more clear and readable instead of adding a comment. Explain hard parts only if they really, really need it, but generally try to use good names and the Extract method refactoring to make the code easier to understand. //TODO's and //FIXME's are sometimes useful when they are for yourself only, or if they are connected to a longer explanation or a ticket.