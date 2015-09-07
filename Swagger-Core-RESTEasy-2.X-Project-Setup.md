---
---
---

# **This document is here for legacy information and refers to an old version of swagger-core. To use the latest, please refer to the [new guide](https://github.com/swagger-api/swagger-core/wiki/Swagger-Core-RESTEasy-2.X-Project-Setup-1.5).**

---
---
---

This page contains the required information to add Swagger to your RESTEasy 2.X application.

You can find additional information at our main [[set up page|Swagger-Core-JAX-RS-Project-Setup]].

You need to complete the three steps in order to set up your application with Swagger:

1. [Adding the dependencies to your application](#adding-the-dependencies-to-your-application)
1. [Hooking up Swagger-Core in your Application](#hooking-up-swagger-core-in-your-application)
1. [Configure and Initialize Swagger](#configure-and-initialize-swagger)

For your convenience there are two RESTEasy sample projects:

1. [java-resteasy](https://github.com/swagger-api/swagger-core/tree/v1.3.12/samples/java-resteasy) - includes a configuration based on the usage of the Application class.
2. [java-resteasy-spring](https://github.com/swagger-api/swagger-core/tree/v1.3.12/samples/java-resteasy-spring) - uses RESTEasy's own method of integrating with Spring.

## Adding the dependencies to your application

Check the [[change log|Changelog]] to see information about the latest version and the changes from previous versions.

Use the following maven dependency:
```xml
<dependency>
  <groupId>com.wordnik</groupId>
  <artifactId>swagger-jaxrs_2.10</artifactId>
  <version>1.3.12</version>
</dependency>
```

## Hooking up Swagger-Core in your Application

RestEasy offers several mechanisms to register resources and providers:

1. [Manual registration using configuration switches](#manual-registration-using-configuration-switches)
1. [Automatic scanning and registration](#automatic-scanning-and-registration)
1. [Using the Application class](#using-a-custom-application-subclass)
1. [Using Spring](#using-spring)

You can use the options mentioned using any form of available web.xml RestEasy initialization (servlets, filter or listener).

Regardless of the configuration method, the Swagger specification will be available at `/api-docs` under the designated context root. If, for example, you mapped RestEasy to serve requests under `/myapi/*`, then Swagger will be available under `/myapi/api-docs`.

### Manual registration using configuration switches

Using RestEasy's `<context-param>` switches, you can set your applications resources manually. The following `<context-param>`s need to be part of your *web.xml*:

```xml
  <context-param>
    <param-name>resteasy.resources</param-name>
    <param-value>
	  com.wordnik.swagger.jaxrs.listing.ApiListingResourceJSON,
	  {your.application.resources}
	  </param-value>
  </context-param>
```

```xml
<context-param>
    <param-name>resteasy.providers</param-name>
    <param-value>
      com.wordnik.swagger.jaxrs.json.JacksonJsonProvider,
      com.wordnik.swagger.jaxrs.listing.ApiDeclarationProvider,
      com.wordnik.swagger.jaxrs.listing.ResourceListingProvider,
      {your.application.providers}
    </param-value>
  </context-param>
```


A few things to note:

1. `{your.application.resources}` is your application's list of comma separated classes acting as resources.
1. `{your.application.providers}` is your application's list of comma separated providers acting as resources.
1. In both cases, if there are no application resources or providers, the last comma and placeholders should be removed.

### Automatic scanning and registration

If you've already configured the following `<context-param>`:

```xml
  <context-param>
    <param-name>resteasy.scan</param-name>
    <param-value>true</param-value>
  </context-param>
```

that will also pick Swagger's resources and providers automatically.

Note that if you only set `resteasy.scan.providers` or `resteasy.scan.resources` to `true`, you will have to either enable the other as well or configure the relevant resource/providers as suggested in the previous configuration option.

When using automatic scanning, swagger-core is unable to detect the resources automatically. To resolve that, you must tell swagger-core which packages to scan. The suggested solution is to use the [BeanConfig](#using-swaggers-beanconfig) method (most likely as a Servlet). Make sure to use the `setResourcePackage(String)` method in order to tell Swagger where it can find the resources.

### Using a custom `Application` subclass

When using a custom `Application` subclass, you would need to add swagger-core's providers to the set up process. For example:

```java
public class SampleApplication extends Application {
    @Override
    public Set<Class<?>> getClasses() {
        Set<Class<?>> resources = new HashSet();

        //resources.add(FirstResource.class);
        //resources.add(SecondResource.class);
        //...

        resources.add(com.wordnik.swagger.jaxrs.listing.ApiListingResource.class);
        resources.add(com.wordnik.swagger.jaxrs.listing.ApiDeclarationProvider.class);
        resources.add(com.wordnik.swagger.jaxrs.listing.ApiListingResourceJSON.class);
        resources.add(com.wordnik.swagger.jaxrs.listing.ResourceListingProvider.class);

        return resources;
    }
}
```

The commented part is where you'd add your own application's resources and providers.

### Using Spring

The following instruction are based on using RESTEasy's own listener for Spring integration (the `org.jboss.resteasy.plugins.spring.SpringContextLoaderListener` listener). 

Swagger Core's classes are framework-agnostic and as such do contain Spring annotations and will not be picked by Spring's scanning feature.

As such, you need to add Swagger-Core's relevant classes manually to your application context configuration file:

```xml
    <bean id="apiListingResourceJSON" class="com.wordnik.swagger.jaxrs.listing.ApiListingResourceJSON"/>
    <bean id="apiDeclarationProvider" class="com.wordnik.swagger.jaxrs.listing.ApiDeclarationProvider"/>
    <bean id="resourceListingProvider" class="com.wordnik.swagger.jaxrs.listing.ResourceListingProvider"/>
```

When using Spring, you must add a [BeanConfig bean](#using-springs-bean-declaration) to initialize Swagger.

## Configure and Initialize Swagger

The following ways are available to configure and initialize Swagger-Core:

1. [Using Swagger's Servlet in the web.xml](#using-swaggers-servlet-in-the-webxml)
1. [Using Swagger's BeanConfig](#using-swaggers-beanconfig)
    1. [Using a Servlet](#using-a-servlet)
    1. [Using the Application class](#using-the-application-class)
    1. [Using Spring's Bean Declaration](#using-springs-bean-declaration)

### Using Swagger's Servlet in the web.xml

Swagger offers a simple Servlet that can be used to initialize it.

Add the following snippet to your web.xml:
```xml
    <servlet>
        <servlet-name>Jersey2Config</servlet-name>
        <servlet-class>com.wordnik.swagger.jaxrs.config.DefaultJaxrsConfig</servlet-class>
        <init-param>
            <param-name>api.version</param-name>
            <param-value>1.0.0</param-value>
        </init-param>
        <init-param>
            <param-name>swagger.api.basepath</param-name>
            <param-value>http://localhost:8080/api</param-value>
        </init-param>
        <load-on-startup>2</load-on-startup>
    </servlet>
```

A few things to note:

1. The `api.version` should reflect the version of your own API.
2. `swagger.api.basepath` should point to the context root of your API. This defers from server to server and how you configured your JAX-RS application.
3. There's no `<servlet-mapping>` for this servlet as it is only used for initialization and doesn't actually expose any interface.

### Using Swagger's BeanConfig

Swagger's `BeanConfig` class allows you to set various properties for Swagger's initialization.

Method | Property Name | Purpose
--- | --- | --- 
setTitle(String) | title | Sets the title of the application.
setDescription(String) | description | Sets the description of the application.
setTermsOfServiceUrl(String) | termsOfServiceUrl | Sets the URL of the application's Terms of Service.
setContact(String) | contact | Sets the contact information for the application.
setLicense(String) | license | Sets the license of the application.
setLicenseUrl(String) | licenseUrl | Sets the licesne url of the application.
setVersion(String) | version | Sets the version of the API.
setBasePath(String) | basePath | Sets the `basePath` for the API calls.
setApiReader(String) | apiReader | Sets an API Reader class for Swagger.
setFilterClass(Sting) | filterClass | Sets a security filter for Swagger's documentation.
setResourcePackage(String) | resourcePackage | Sets which package(s) Swagger should scan to pick up resources. If there's more than one package, it can be a list of comma-separated packages.
setScan(boolean) | scan | When set to `true`, Swagger will build the documentation.

In order for the Swagger to operate properly, you *must* set the base path of the application.

In order for Swagger to actually produce the documentation, you *must* `setScan(true)`.

The BeanConfig should be called when your application starts up. The two common use cases are either using a Servlet or the Application class if you're already using one. Otherwise, any other method you use at your application's initialization could work.

1. [Using a Servlet](#using-a-servlet)
1. [Using the Application class](#using-the-application-class)
1. [Using Spring's Bean Declaration](#using-springs-bean-declaration)

#### Using a Servlet

A sample servlet would be:

```java
package io.swagger.api.util;

import javax.servlet.ServletConfig;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;

public class Bootstrap extends HttpServlet {
    @Override
    public void init(ServletConfig config) throws ServletException {
        super.init(config);

        BeanConfig beanConfig = new BeanConfig();
        beanConfig.setVersion("1.0.2");
        beanConfig.setBasePath("http://localhost:8002/api");
        beanConfig.setResourcePackage("io.swagger.resources");
        beanConfig.setScan(true);
    }
}
```

And adding the following snippet to the web.xml will ensure the initialization of Swagger:
```xml
    <servlet>
        <servlet-name>SwaggerBootstrap</servlet-name>
        <servlet-class>io.swagger.api.util.Bootstrap</servlet-class>
        <load-on-startup>2</load-on-startup>
    </servlet>
```

There's no need for a URL mapping for this servlet as it is only used to initialize the application.

#### Using the Application class

If you're already using an Application class to configure your JAX-RS application, you can use its constructor to set up Swagger:

```java
public class SampleApplication extends Application {

    public SampleApplication() {
        BeanConfig beanConfig = new BeanConfig();
        beanConfig.setVersion("1.0.2");
        beanConfig.setBasePath("http://localhost:8002/api");
        beanConfig.setResourcePackage("io.swagger.resources");
        beanConfig.setScan(true);
    }

    @Override
    public Set<Class<?>> getClasses() {
        // set your resources here
    }
}
```

#### Using Spring's Bean Declaration

When using Spring, you must include a BeanConfig bean declaration in your application context configuration file. When using Spring, Swagger-Core cannot pick up the resources automatically, so must set the value of the `resourcePackage` property to which packages need to be scanned by Swagger. You must also set the `scan` property to be `true` for the scanning to take place.

A sample declaration would be:
```xml
    <bean id="beanConfig" class="com.wordnik.swagger.jaxrs.config.BeanConfig">
        <property name="title" value="Swagger Sample App"/>
        <property name="version" value="1.0.0" />
        <property name="basePath" value="http://localhost:8002/api"/>
        <property name="resourcePackage" value="io.swagger.resources"/>
        <property name="scan" value="true"/>
    </bean>
```

You can use any of the properties available with to the [BeanConfig](#using-swaggers-beanconfig) to customize your Swagger configuration.