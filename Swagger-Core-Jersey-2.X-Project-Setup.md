---
---
---

# **This document is here for legacy information and refers to an old version of swagger-core. To use the latest, please refer to the [new guide](https://github.com/swagger-api/swagger-core/wiki/Swagger-Core-Jersey-2.X-Project-Setup-1.5).**

---
---
---

This page contains the required information to add Swagger to your Jersey 2.X application.

You can find additional information at our main [[set up page|Swagger-Core-JAX-RS-Project-Setup]].

You need to complete the three steps in order to set up your application with Swagger:

1. [Adding the dependencies to your application](#adding-the-dependencies-to-your-application)
1. [Hooking up Swagger-Core in your Application](#hooking-up-swagger-core-in-your-application)
1. [Configure and Initialize Swagger](#configure-and-initialize-swagger)

You can check out the [sample application](https://github.com/swagger-api/swagger-core/tree/v1.3.12/samples/java-jersey2) which is based on the package scanning configuration.

## Adding the dependencies to your application

Check the [[change log|Changelog]] to see information about the latest version and the changes from previous versions.

Use the following maven dependency:
```xml
<dependency>
  <groupId>com.wordnik</groupId>
  <artifactId>swagger-jersey2-jaxrs_2.10</artifactId>
  <version>1.3.12</version>
</dependency>
```

If you use a version older than 1.3.4 (which you shouldn't), you need to add an explicit exclusion for the dependency to work properly:
```xml
<dependency>
  <groupId>com.wordnik</groupId>
  <artifactId>swagger-jersey2-jaxrs_2.10</artifactId>
  <version>1.3.2</version>
  <exclusions>
    <exclusion>
      <groupId>javax.ws.rs</groupId>
      <artifactId>jsr311-api</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

The `swagger-jersey2-jaxrs` artifact pulls in a specific version of Jersey2. Maven's dependency management should resolve the version properly if you use an newer version, however in some cases you may be required to manually exclude it.


## Hooking up Swagger-Core in your Application

1. [Using Jersey 2 container Servlet or Filter (with web.xml)](#using-jersey-2-container-servlet-or-filter-with-webxml)
    1. [Package scanning / Concrete class selection](#package-scanning--concrete-class-selection)
    1. [Using a custom Application subclass](#using-a-custom-application-subclass)

### Using Jersey 2 container Servlet or Filter (with web.xml)
You can set Jersey 2's `ServletContainer` as either a servlet or a filter.

#### Package scanning / Concrete class selection

In both cases, you can use `<init-param/>` to set which packages or classes Jersey should scan/load when it starts. This is where you'd define your own packages and/or classes.

To hook Swagger into your application, you need to add the some of Swagger's packages and classes.

If you're defining it as a servlet:
```xml
    <servlet>
        <servlet-name>jersey</servlet-name>
        <servlet-class>org.glassfish.jersey.servlet.ServletContainer</servlet-class>
        <init-param>
            <param-name>jersey.config.server.provider.packages</param-name>
            <param-value>
                com.wordnik.swagger.jaxrs.json,
                {your.application.packages}
            </param-value>
        </init-param>
        <init-param>
            <param-name>jersey.config.server.provider.classnames</param-name>
            <param-value>
                com.wordnik.swagger.jersey.listing.ApiListingResourceJSON,
                com.wordnik.swagger.jersey.listing.JerseyApiDeclarationProvider,
                com.wordnik.swagger.jersey.listing.JerseyResourceListingProvider
            </param-value>
        </init-param>
    </servlet>


    <servlet-mapping>
        <servlet-name>jersey</servlet-name>
        <url-pattern>/api/*</url-pattern>
    </servlet-mapping>
```

And if as a filter:
```xml
    <filter>
        <filter-name>jersey</filter-name>
        <filter-class>org.glassfish.jersey.servlet.ServletContainer</filter-class>
        <init-param>
            <param-name>jersey.config.server.provider.packages</param-name>
            <param-value>
                com.wordnik.swagger.jaxrs.json,
                {your.application.packages}
            </param-value>
        </init-param>
        <init-param>
            <param-name>jersey.config.server.provider.classnames</param-name>
            <param-value>
                com.wordnik.swagger.jersey.listing.ApiListingResourceJSON,
                com.wordnik.swagger.jersey.listing.JerseyApiDeclarationProvider,
                com.wordnik.swagger.jersey.listing.JerseyResourceListingProvider
            </param-value>
        </init-param>
    </filter>


    <filter-mapping>
        <filter-name>jersey</filter-name>
        <url-pattern>/api/*</url-pattern>
    </filter-mapping>
```

A few things to note:

1. The init-param `jersey.config.server.provider.classnames` can obviously contain your own classes.
2. `{your.application.packages}` should be replaced by the package(s) of your application.
3. The mapping of either servlet or filter depends on your own needs. The above is just an example.

#### Using a custom `Application` subclass

When using a custom `Application` subclass, you would need to add swagger-core's providers to the set up process. For example:

```java
public class SampleApplication extends Application {
    @Override
    public Set<Class<?>> getClasses() {
        Set<Class<?>> resources = new HashSet();

        //resources.add(FirstResource.class);
        //resources.add(SecondResource.class);
        //...

        resources.add(com.wordnik.swagger.jersey.listing.ApiListingResourceJSON.class);
        resources.add(com.wordnik.swagger.jersey.listing.JerseyApiDeclarationProvider.class);
        resources.add(com.wordnik.swagger.jersey.listing.JerseyResourceListingProvider.class);

        return resources;
    }
}
```

The commented part is where you'd add your own application's resources and providers.


## Configure and Initialize Swagger

1. [Using Swagger's Servlet in the web.xml](#using-swaggers-servlet-in-the-webxml)
1. [Using Swagger's BeanConfig](#using-swaggers-beanconfig)
    1. [Using a Servlet](#using-a-servlet)
    1. [Using the Application class](#using-the-application-class)

### Using Swagger's Servlet in the web.xml

Swagger offers a simple Servlet that can be used to initialize it.

Add the following snippet to your web.xml:
```xml
    <servlet>
        <servlet-name>Jersey2Config</servlet-name>
        <servlet-class>com.wordnik.swagger.jersey.config.JerseyJaxrsConfig</servlet-class>
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