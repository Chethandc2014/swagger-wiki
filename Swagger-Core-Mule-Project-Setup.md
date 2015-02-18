This page contains the required information to add Swagger to your Mule application.

You can find additional information at our main [[set up page|Swagger-Core-JAX-RS-Project-Setup]].

1. [Adding the dependencies to your application](#adding-the-dependencies-to-your-application)
1. [Hooking up Swagger-Core in your Application](#hooking-up-swagger-core-in-your-application)
1. [Configure and Initialize Swagger](#configure-and-initialize-swagger)

You can check out the [sample application](https://github.com/swagger-api/swagger-core/tree/v1.3.12/samples/java-mule).

## Adding the dependencies to your application

Check the [[change log|Changelog]] to see information about the latest version and the changes from previous versions.

Use the following maven dependency:
```xml
<dependency>
  <groupId>com.wordnik</groupId>
  <artifactId>swagger-mule_2.10</artifactId>
  <version>1.3.12</version>
</dependency>
```

**Note:** The minimum version for Mule support is 1.3.10.

## Hooking up Swagger-Core in your Application

The next step is to set up your mule configuration (`config.resources`).

In your mule integration xml, add the following snippet:

```xml
	<spring:beans>
		<spring:bean id="apiListingJSON"
			class="com.wordnik.swagger.mule.ApiListingJSON" />
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

The final step is to register swagger-core's providers. For this, you need to create a directory called `services` under your `META-INF` directory.

Under the `services` directory, create a text file with the name `javax.ws.rs.ext.MessageBodyWriter`. In it, include the following text (one line per entry):
```
com.wordnik.swagger.jaxrs.listing.ResourceListingProvider
com.wordnik.swagger.jaxrs.listing.ApiDeclarationProvider
```
This is based on Java's [SPI](http://docs.oracle.com/javase/tutorial/sound/SPI-intro.html) mechanism.

## Configure and Initialize Swagger

The final step is to use swagger-core's `BeanConfig` to initialize Swagger.

In your mule configuration file, add the BeanConfig bean. You can add it alongside with the previous ApiListingJSON declaration as such:
```xml
	<spring:beans>
		<spring:bean id="apiListingJSON"
			class="com.wordnik.swagger.mule.ApiListingJSON" />
		<spring:bean id="swaggerConfig"
			class="com.wordnik.swagger.jaxrs.config.BeanConfig">
			<spring:property name="resourcePackage" value="com.app.sample" />
			<spring:property name="version" value="1.0.0" />
			<spring:property name="basePath" value="http://localhost:7001" />
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
basePath | Sets the `basePath` for the API calls.
apiReader | Sets an API Reader class for Swagger.
filterClass | Sets a security filter for Swagger's documentation.
resourcePackage | Sets which package(s) Swagger should scan to pick up resources. If there's more than one package, it can be a list of comma-separated packages.
scan | When set to `true`, Swagger will build the documentation.

In order for the Swagger to operate properly, you *must* set the base path of the application.

In order for Swagger to actually produce the documentation, you *must* set `"scan"` to be `"true"`.