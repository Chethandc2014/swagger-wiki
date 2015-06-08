This page contains the required information to add Swagger to your Mule application.

You can find additional information at our main [[set up page|Swagger-Core-JAX-RS-Project-Setup-1.5.X]].

**NOTE: swagger-core 1.5.X produces [Swagger 2.0](https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md) definition files. For more information, check out the [swagger-spec repository](https://github.com/swagger-api/swagger-spec).**

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

You can check out the [sample application](https://github.com/swagger-api/swagger-samples/tree/master/java/java-mule).

<hr><hr>

## <a name="adding-the-dependencies-to-your-application"></a>1. Adding the dependencies to your application

Check the [[change log|Changelog]] to see information about the latest version and the changes from previous versions.

Use the following maven dependency:
```xml
<dependency>
  <groupId>io.swagger</groupId>
  <artifactId>swagger-mule</artifactId>
  <version>1.5.0</version>
</dependency>
```

<hr><hr>

## <a name="hooking-up-swagger-core-in-your-application"></a>2. Hooking up Swagger-Core in your Application

The next step is to set up your mule configuration (`config.resources`).

In your mule integration xml, add the following snippet:

```xml
    <spring:beans>
        <spring:bean id="apiListingJSON"
            class="io.swagger.mule.ApiListingJSON" />
    </spring:beans>
```

Under the flow definition where you define your Jersey resources, add the bean you defined above as an additional component:

```xml
    <jersey:resources doc:name="REST">
        <component>
            <spring-object bean="sampleResourceBean" />
        </component>
        <component>
            <spring-object bean="apiListingJSON" />
        </component>
    </jersey:resources>
```

The above snippet assumes you have an additional REST resource you declared as `sampleResourceBean`.

<hr><hr>

## <a name="configure-and-initialize-swagger"></a>3. Configure and Initialize Swagger

The final step is to use swagger-core's `BeanConfig` to initialize Swagger.

In your mule configuration file, add the BeanConfig bean. You can add it alongside with the previous ApiListingJSON declaration as such:
```xml
    <spring:beans>
        <spring:bean id="apiListingJSON"
            class="io.swagger.mule.ApiListingJSON" />
        <spring:bean id="swaggerConfig"
            class="io.swagger.jaxrs.config.BeanConfig">
            <spring:property name="resourcePackage" value="com.app.sample" />
            <spring:property name="version" value="1.0.0" />
            <spring:property name="host" value="localhost:7001" />
            <spring:property name="basePath" value="/" />
            <spring:property name="scan" value="true" />
        </spring:bean>
    </spring:beans>
```

The following properties are available for your BeanConfig:

Property | Purpose
--- | --- 
title | Sets the title of the application.
description | Sets the description of the application.
termsOfServiceUrl | Sets the URL of the application's Terms of Service.
contact | Sets the contact information for the application.
license | Sets the license of the application.
licenseUrl | Sets the licesne url of the application.
version | Sets the version of the API.
schemes | Sets the schemes for the API URLs (http, https).
host | Sets the host (including a port) for the API URLs. Does not include the schemes nor context root.
basePath | Sets the context root for the API calls.
filterClass | Sets a security filter for Swagger's documentation.
resourcePackage | Sets which package(s) Swagger should scan to pick up resources. If there's more than one package, it can be a list of comma-separated packages.
scan | When set to `true`, Swagger will build the documentation.
prettyPrint | Sets whether the `swagger.json` will be pretty printed.

In order for the Swagger to operate properly, you *must* set the base path of the application.

In order for Swagger to actually produce the documentation, you *must* set `"scan"` to be `"true"`.

**You are done with this guide! You should now be able to access the Swagger definition at `/swagger.json` and `/swagger.yaml` at the context root of your application.**