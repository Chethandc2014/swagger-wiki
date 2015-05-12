This page contains the required information to add Swagger to your Jersey 2.X application.

You can find additional information at our main [[set up page|Swagger-Core-JAX-RS-Project-Setup-1.5.X]].

**NOTE: swagger-core 1.5.X produces [Swagger 2.0](https://github.com/wordnik/swagger-spec/blob/master/versions/2.0.md) definition files. For more information, check out the [swagger-spec repository](https://github.com/swagger-api/swagger-spec).**

You need to complete the three steps in order to set up your application with Swagger:

1. [Adding the dependencies to your application](#adding-the-dependencies-to-your-application)
1. [Hooking up Swagger-Core in your Application](#hooking-up-swagger-core-in-your-application)
1. [Configure and Initialize Swagger](#configure-and-initialize-swagger)

<hr>
Once you complete these steps, your Swagger definition would be available at `/swagger.json` and `/swagger.yaml` at the context root of your application.
<hr>

You can check out the [sample application](https://github.com/swagger-api/swagger-core/tree/master/samples/java-jersey2) which is based on the package scanning configuration.

<hr><hr>

## <a name="adding-the-dependencies-to-your-application"></a>1. Adding the dependencies to your application

Check the [[change log|Changelog]] to see information about the latest version and the changes from previous versions.

Use the following maven dependency:
```xml
<dependency>
  <groupId>com.wordnik</groupId>
  <artifactId>swagger-jersey2-jaxrs</artifactId>
  <version>1.5.1-M1</version>
</dependency>
```

The `swagger-jersey2-jaxrs` artifact pulls in a specific version of Jersey 2. Maven's dependency management should resolve the version properly if you use an newer version, however in some cases you may be required to manually exclude it.

<hr><hr>

## <a name="hooking-up-swagger-core-in-your-application"></a>2. Hooking up Swagger-Core in your Application
In order to integrate swagger-core with your application, follow the instructions provided based on the way you configured Jersey in your application. Only one of these methods should apply for your application. 

- [Using Jersey 2 container Servlet or Filter (with web.xml)](#using-jersey-2-container-servlet-or-filter-with-webxml)
    - [Package scanning / Concrete class selection](#package-scanning--concrete-class-selection)
    - [Using a custom Application subclass](#using-a-custom-application-subclass)

<hr>

### Using Jersey 2 container Servlet or Filter (with web.xml)
When you use Jersey's `ServletContainer` servlet, you can either configure it using the web.xml directly or use a custom Application subclass. Follow the instructions below based on the configuration you use in your application.

- [Package scanning / Concrete class selection](#package-scanning--concrete-class-selection)
- [Using a custom Application subclass](#using-a-custom-application-subclass)

<hr>

#### Package scanning / Concrete class selection

In both cases, you can use `<init-param/>` to set which packages or classes Jersey should scan/load when it starts. This is where you'd define your own packages and/or classes.

To hook Swagger into your application, you need to add the some of Swagger's packages and classes.

###### Package scanning
If you're defining it as a servlet:
```xml
    <servlet>
      <servlet-name>jersey</servlet-name>
      <servlet-class>org.glassfish.jersey.servlet.ServletContainer</servlet-class>
      <init-param>
        <param-name>jersey.config.server.provider.packages</param-name>
        <param-value>
            com.wordnik.swagger.jaxrs.listing,
            {your.application.packages}
        </param-value>
      </init-param>
      <load-on-startup>1</load-on-startup>
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
                com.wordnik.swagger.jaxrs.listing,
                {your.application.packages}
            </param-value>
        </init-param>
    </filter>


    <filter-mapping>
        <filter-name>jersey</filter-name>
        <url-pattern>/api/*</url-pattern>
    </filter-mapping>
```

**You can now proceed to the next section - [Configure and Initialize Swagger](#configure-and-initialize-swagger).**

<hr>

###### Concrete class selection
If you're defining it as a servlet:
```xml
    <servlet>
        <servlet-name>jersey</servlet-name>
        <servlet-class>org.glassfish.jersey.servlet.ServletContainer</servlet-class>
        <init-param>
            <param-name>jersey.config.server.provider.classnames</param-name>
            <param-value>
                com.wordnik.swagger.jaxrs.listing.ApiListingResource,
                com.wordnik.swagger.jaxrs.listing.SwaggerSerializers,
                {your.application.classes}
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
            <param-name>jersey.config.server.provider.classnames</param-name>
            <param-value>
                com.wordnik.swagger.jaxrs.listing.ApiListingResource,
                com.wordnik.swagger.jaxrs.listing.SwaggerSerializers,
                {your.application.classes}
            </param-value>
        </init-param>
    </filter>


    <filter-mapping>
        <filter-name>jersey</filter-name>
        <url-pattern>/api/*</url-pattern>
    </filter-mapping>
```

A few things to note:

1. `{your.application.packages}`, `{your.application.classes}` should be replaced by the package(s) or classes of your application, respectively.
2. The mapping of either servlet or filter depends on your own needs. The above is just an example.

**You can now proceed to the next section - [Configure and Initialize Swagger](#configure-and-initialize-swagger).**

<hr>

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

        resources.add(com.wordnik.swagger.jaxrs.listing.ApiListingResource.class);
        resources.add(com.wordnik.swagger.jaxrs.listing.SwaggerSerializers.class);
        
        return resources;
    }
}
```

The commented part is where you'd add your own application's resources and providers.

**You can now proceed to the next section - [Configure and Initialize Swagger](#configure-and-initialize-swagger).**

<hr><hr>

## <a name="configure-and-initialize-swagger"></a>3. Configure and Initialize Swagger

There are two main ways to configure and initialize the Swagger definition within your application. The first, within the web.xml, offers a simplified method of configuration. If you're looking for more control, then the BeanConfig path is the one you should choose.

- [Using Swagger's Servlet in the web.xml](#using-swaggers-servlet-in-the-webxml)
- [Using Swagger's BeanConfig](#using-swaggers-beanconfig)
    - [Using a Servlet](#using-a-servlet)
    - [Using the Application class](#using-the-application-class)

<hr>

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

**You are done with this guide! You should now be able to access the Swagger definition at `/swagger.json` and `/swagger.yaml` at the context root of your application.**

<hr>

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
setSchemes(String[]) | schemes | Sets the schemes for the API URLs (http, https).
setHost(String) | host | Sets the host (including a port) for the API URLs. Does not include the schemes nor context root.
setBasePath(String) | basePath | Sets the context root for the API calls.
setFilterClass(Sting) | filterClass | Sets a security filter for Swagger's documentation.
setResourcePackage(String) | resourcePackage | Sets which package(s) Swagger should scan to pick up resources. If there's more than one package, it can be a list of comma-separated packages.
setScan(boolean) | scan | When set to `true`, Swagger will build the documentation.
setPrettyPrint(boolean) | prettyPrint | Sets whether the `swagger.json` will be pretty printed.

In order for the Swagger to operate properly, you *must* set the base path of the application.

In order for Swagger to actually produce the documentation, you *must* `setScan(true)`.

The BeanConfig should be called when your application starts up. The two common use cases are either using a Servlet or the Application class if you're already using one. Otherwise, any other method you use at your application's initialization could work.

- [Using a Servlet](#using-a-servlet)
- [Using the Application class](#using-the-application-class)

<hr>

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
        beanConfig.setSchemes(new String[]{"http"});
        beanConfig.setHost("localhost:8002");
        beanConfig.setBasePath("/api");
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

**You are done with this guide! You should now be able to access the Swagger definition at `/swagger.json` and `/swagger.yaml` at the context root of your application.**

<hr>

#### Using the Application class

If you're already using an Application class to configure your JAX-RS application, you can use its constructor to set up Swagger:

```java
public class SampleApplication extends Application {

    public SampleApplication() {
        BeanConfig beanConfig = new BeanConfig();
        beanConfig.setVersion("1.0.2");
        beanConfig.setSchemes(new String[]{"http"});
        beanConfig.setHost("localhost:8002");
        beanConfig.setBasePath("/api");
        beanConfig.setResourcePackage("io.swagger.resources");
        beanConfig.setScan(true);
    }

    @Override
    public Set<Class<?>> getClasses() {
        // set your resources here
    }
}
```

**You are done with this guide! You should now be able to access the Swagger definition at `/swagger.json` and `/swagger.yaml` at the context root of your application.**