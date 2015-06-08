This page contains the required information to add Swagger to your RESTEasy 2.X application.

You can find additional information at our main [[set up page|Swagger-Core-JAX-RS-Project-Setup-1.5.X]].

**NOTE: swagger-core 1.5.X produces [Swagger 2.0](https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md) definition files. For more information, check out the [swagger-spec repository](https://github.com/swagger-api/swagger-spec).**

You need to complete the three steps in order to set up your application with Swagger:

1. [Adding the dependencies to your application](#adding-the-dependencies-to-your-application)
1. [Hooking up Swagger-Core in your Application](#hooking-up-swagger-core-in-your-application)
1. [Configure and Initialize Swagger](#configure-and-initialize-swagger)

<hr>
Once you complete these steps, your Swagger definition would be available at `/swagger.json` and `/swagger.yaml` at the context root of your application.
<hr>

---

As part of the 1.5 release, we've repackaged the project under the **io.swagger** package. If you're migrating, you need to update your imports accordingly. This will mostly affect your annotation usage. A simple find/replace should cover this change easily.

The groupId in maven has also changed to `io.swagger`.

---

For your convenience there are two RESTEasy sample projects:

1. [java-resteasy](https://github.com/swagger-api/swagger-samples/tree/master/java/java-resteasy) - includes a configuration based on the usage of the Application class.
2. [java-resteasy-spring](https://github.com/swagger-api/swagger-samples/tree/master/java/java-resteasy-spring) - uses RESTEasy's own method of integrating with Spring.

<hr><hr>

## <a name="adding-the-dependencies-to-your-application"></a>1. Adding the dependencies to your application

Check the [[change log|Changelog]] to see information about the latest version and the changes from previous versions.

Use the following maven dependency:
```xml
<dependency>
  <groupId>io.swagger</groupId>
  <artifactId>swagger-jaxrs</artifactId>
  <version>1.5.0</version>
</dependency>
```

<hr><hr>

## <a name="hooking-up-swagger-core-in-your-application"></a>2. Hooking up Swagger-Core in your Application

RestEasy offers several mechanisms to register resources and providers. Use *one* of the following to hook up swagger-core, based on the method you already use:

- [Manual registration using configuration switches](#manual-registration-using-configuration-switches)
- [Automatic scanning and registration](#automatic-scanning-and-registration)
- [Using the Application class](#using-a-custom-application-subclass)
- [Using Spring](#using-spring)

You can use the options mentioned using any form of available web.xml RestEasy initialization (servlets, filter or listener).

Regardless of the configuration method, the Swagger specification will be available at `/swagger.json` under the designated context root. If, for example, you mapped RestEasy to serve requests under `/myapi/*`, then Swagger will be available under `/myapi/swagger.json`.

<hr>

### Manual registration using configuration switches

Using RestEasy's `<context-param>` switches, you can set your applications resources manually. The following `<context-param>` need to be part of your *web.xml*:

```xml
<context-param>
    <param-name>resteasy.providers</param-name>
    <param-value>
      io.swagger.jaxrs.listing.ApiListingResource,
      io.swagger.jaxrs.listing.SwaggerSerializers,
      {your.application.providers}
    </param-value>
  </context-param>
```


Note: `{your.application.providers}` is your application's list of comma separated providers acting as resources.

**You can now proceed to the next section - [Configure and Initialize Swagger](#configure-and-initialize-swagger).**

<hr>

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

**You can now proceed to the next section - [Configure and Initialize Swagger](#configure-and-initialize-swagger).**

<hr>

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

        resources.add(io.swagger.jaxrs.listing.ApiListingResource.class);
        resources.add(io.swagger.jaxrs.listing.SwaggerSerializers.class);

        return resources;
    }
}
```

The commented part is where you'd add your own application's resources and providers.

**You can now proceed to the next section - [Configure and Initialize Swagger](#configure-and-initialize-swagger).**

<hr>

### Using Spring

The following instruction are based on using RESTEasy's own listener for Spring integration (the `org.jboss.resteasy.plugins.spring.SpringContextLoaderListener` listener). 

Swagger Core's classes are framework-agnostic and as such do contain Spring annotations and will not be picked by Spring's scanning feature.

As such, you need to add Swagger-Core's relevant classes manually to your application context configuration file:

```xml
    <bean id="apiListingResourceJSON" class="io.swagger.jaxrs.listing.ApiListingResource"/>
    <bean id="apiDeclarationProvider" class="io.swagger.jaxrs.listing.SwaggerSerializers"/>
```

When using Spring, you must add a [BeanConfig bean](#using-springs-bean-declaration) to initialize Swagger.

**You can now proceed to the next section - [Configure and Initialize Swagger](#configure-and-initialize-swagger).**

<hr><hr>

## <a name="configure-and-initialize-swagger"></a>3. Configure and Initialize Swagger

There are two main ways to configure and initialize the Swagger definition within your application. The first, within the web.xml, offers a simplified method of configuration. If you're looking for more control, then the BeanConfig path is the one you should choose.

- [Using Swagger's Servlet in the web.xml](#using-swaggers-servlet-in-the-webxml)
- [Using Swagger's BeanConfig](#using-swaggers-beanconfig)
    - [Using a Servlet](#using-a-servlet)
    - [Using the Application class](#using-the-application-class)
    - [Using Spring's Bean Declaration](#using-springs-bean-declaration)

<hr>

### Using Swagger's Servlet in the web.xml

Swagger offers a simple Servlet that can be used to initialize it.

Add the following snippet to your web.xml:
```xml
    <servlet>
        <servlet-name>Jersey2Config</servlet-name>
        <servlet-class>io.swagger.jaxrs.config.DefaultJaxrsConfig</servlet-class>
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
- [Using Spring's Bean Declaration](#using-springs-bean-declaration)

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

<hr>

#### Using Spring's Bean Declaration

When using Spring, you must include a BeanConfig bean declaration in your application context configuration file. When using Spring, Swagger-Core cannot pick up the resources automatically, so must set the value of the `resourcePackage` property to which packages need to be scanned by Swagger. You must also set the `scan` property to be `true` for the scanning to take place.

A sample declaration would be:
```xml
    <bean id="beanConfig" class="io.swagger.jaxrs.config.BeanConfig">
        <property name="title" value="Swagger Sample App"/>
        <property name="version" value="1.0.0" />
        <property name="schemes" value="http" />
        <property name="host" value="localhost:8002" />
        <property name="basePath" value="/api"/>
        <property name="resourcePackage" value="io.swagger.resources"/>
        <property name="scan" value="true"/>
    </bean>
```

You can use any of the properties available with to the [BeanConfig](#using-swaggers-beanconfig) to customize your Swagger configuration.

**You are done with this guide! You should now be able to access the Swagger definition at `/swagger.json` and `/swagger.yaml` at the context root of your application.**