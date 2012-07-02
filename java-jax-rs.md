Java + JAX-RS
==========

Swagger supports the Jersey framework for integration with JAX-RS.

#### Integration Steps

First, include the swagger artifacts in your project.  If using maven, add to your `pom.xml`:

```xml
  <dependencies>
    <dependency>
      <groupId>com.wordnik</groupId>
      <artifactId>swagger-jaxrs_2.9.1</artifactId>
      <version>1.0.1</version>
      <scope>compile</scope>
    </dependency>
  </dependencies>
```

And with ivy, add to your `ivy.xml`:

```xml
  <dependency org="com.wordnik" name="swagger-jaxrs_2.9.1" rev="1.0.1"/>
  <dependency org="com.wordnik" name="swagger-core_2.9.1" rev="1.0.1"/>
```

Next, you need to tell jersey how to find the [Resource Listing](Resource-Listing) class--this is done in one of
two ways:

One: If you use your `web.xml` to declare the packages to scan on startup of jersey, you will have something like this:

```xml
  <servlet>
    <servlet-name>jersey</servlet-name>
    <servlet-class>com.sun.jersey.spi.container.servlet.ServletContainer
    </servlet-class>
    <init-param>
      <param-name>com.sun.jersey.config.property.packages</param-name>
      <param-value>com.your.project;com.wordnik.swagger.jaxrs</param-value>
    </init-param>
  ...
  </servlet>
```

Note the addition to `com.wordnik.swagger.jaxrs` in the `param-value`.  Why?  Because that tells jersey to scan &
register the [resource listing](/wordnik/swagger-core/blob/master/modules/swagger-jaxrs/src/main/scala/com/wordnik/swagger/jaxrs/JavaApiListing.scala), which is in that package in the swagger-jaxrs module.

Two: If you use a Jersey Application to configure your project, you'll likely have something like this in your `web.xml`:

```xml
  <init-param>
    <param-name>javax.ws.rs.Application</param-name>
    <param-value>com.your.project.RestApplication</param-value>
  </init-param>
```

and this class:
```java
package com.your.project;

import com.sun.jersey.api.core.PackagesResourceConfig;

public class RESTApplication extends PackagesResourceConfig {
	public RESTApplication() {
		super("com.your.project.resources");
	}
}
```
Note!  There is no `com.sun.jersey.config.property.packages` configuration.  Don't add one!  If this is your setup, you have
to extend the `JavaApiListing` class in the same package as above:

```java
package com.your.project;

import com.wordnik.swagger.annotations.*;
import com.wordnik.swagger.jaxrs.JavaApiListing;

import javax.ws.rs.*;

@Path("/resources.json")
@Api("/resources")
@Produces({"application/json"})
public class ApiListingResource extends JavaApiListing {}
```

Now you have one last step before adding APIs-configuring the `basePath` for your application.  Recall that the
Resource Listing contains references to other APIs in your app--they are based on the `basePath` in your app
(note! We also snuck in an `api.version` param, which is purely informational to your consumers).

In your web.xml:

```xml
  <init-param>
    <param-name>swagger.api.basepath</param-name>
    <param-value>http://localhost:8080</param-value>
  </init-param>
  <init-param>
    <param-name>api.version</param-name>
    <param-value>1.0</param-value>
  </init-param>
```

Note! This should match the `servlet-mapping` path to your jersey servlet:

```xml
  <servlet-mapping>
    <servlet-name>jersey</servlet-name>
    <url-pattern>/*</url-pattern>
  </servlet-mapping>
```

if you, for instance, had your REST api on /api, you would have `http://localhost:8080/api` in the `swagger.api.basepath` and
`/api/*` in your `url-pattern` config.

You should be able to add APIs now.  This is done by annotating your APIs like this:

```java
@Path("/pet.json")
@Api(value = "/pet", description = "Operations about pets")
@Produces({"application/json"})
public class PetResource extends JavaHelp {
  @GET
  @Path("/{petId}")
  @ApiOperation(value = "Find pet by ID", notes = "Add extra notes here", responseClass = "com.wordnik.swagger.sample.model.Pet")
    @ApiErrors(value = { @ApiError(code = 400, reason = "Invalid ID supplied"),
    @ApiError(code = 404, reason = "Pet not found") })
  public Response getPetById (
    @ApiParam(value = "ID of pet that needs to be fetched", allowableValues = "range[1,5]", required = true) @PathParam("petId") String petId)
  throws NotFoundException {
    // your resource logic 
  }
```

What is this?  First, the `@Path` annotation tells jersey what path to listen to.  You probably already have this.  Next,
the @Api annotation has a mapping to `/pet`--this is the path without the `.json` suffix.  The Swagger framework will add
that automatically--it's left off in case you have a `.xml` and `.json` format support in your api.

The `@Path` is also required for jersey.  The @ApiOperation annotation allows you to specify the human-readable operation
explanation, as well as longer notes and a `responseClass`.  Note that the `responseClass` is fully qualified to the package
of the return model--this lets Swagger automatically scan the model and turn it into a json-schema.

The `@ApiError` annotations let you tell the user what the API might return for errors.  Finally the `@ApiParam` declares
all the input values to the operation.

#### Testing

You should be able to start up your server now.  First thing to verify is that you can hit the Resource Declaration:

```
http://localhost:8080/resources.json
```

This should tell Swagger to scan all the APIs available and produce a listing of them:

```js
{
  apiVersion: "1.0",
  swaggerVersion: "1.0",
  basePath: "http://localhost:8080",
  apis: [
    {
      path: "/pet.{format}",
      description: "Operations about pets"
    }
  ]
}
```

Following the link in the resource listing, you can now read the pet resource:

```
http://localhost:8080/pet.json
```

This should produce something like this:

```js
{
  apiVersion: "1.0",
  swaggerVersion: "1.0",
  basePath: "http://localhost:8080",
  resourcePath: "/pet",
  apis: [
    {
      path: "/pet.{format}/{petId}",
      description: "Operations about pets",
      operations: [
        {
        parameters: [
          {
            name: "petId",
            description: "ID of pet that needs to be fetched",
            dataType: "string",
            allowableValues: {
              valueType: "RANGE",
              max: 10,
              min: 0
            },
            required: true,
            allowMultiple: false,
            paramType: "path"
          }
        ]
        ...
```

Note!  You can see from above that this example assumes Swagger has the GET method on the root API!  That means,
you are not using `http://localhost:8080/pet.json` for any other operations.  This may not be the case, in which
you need to take an alternative listing scheme.  See [java-alt-resource-listing](/wordnik/swagger-core/tree/master/samples/java-alt-resource-listing) for
an example.


## Other configurations

#### Removing the .{format} suffix

You might want to get rid of the `.{format}` in the resource listing and `.json` in your api suffix.  This is done as follows:

Create a bootstrap servlet which will fire on server startup.  The purpose of this servlet is to set the Swagger suffix
to an empty string before Swagger warms up:

```java
package com.wordnik.swagger.sample;

import com.wordnik.swagger.jaxrs.JaxrsApiReader;
import javax.servlet.http.HttpServlet;

public class Bootstrap extends HttpServlet {
  static {
	  JaxrsApiReader.setFormatString("");
  }
}
```

Next, you'll need to override the default `JavaApiListing` class.  You may have already done this if using a
Jersey Application, but for completeness, subclass the listing class and set the resource listing to NOT have the `.json` suffix:

```java
package com.your.project;

import com.wordnik.swagger.annotations.*;
import com.wordnik.swagger.jaxrs.JavaApiListing;

import javax.ws.rs.*;

@Path("/resources")
@Api("/resources")
@Produces({"application/json"})
public class ApiListingResource extends JavaApiListing {}
```
Finally, in all your APIs, your `@Path` annotations will appropriately not have the `.json` suffix:

```java
@Path("/pet")
@Api(value = "/pet", description = "Operations about pets")
@Produces({"application/json"})
public class PetResource extends JavaHelp {
```

That's it--note that taking the format suffix away will likely cause other issues for your clients, especially
if you support `xml` as well.

#### Changing the listing path

If your API needs to support `GET` on the root resource, you can re-map the Swagger API listing files wherever 
you like.  There are a couple steps to do this.

First, you need to specify what the listing path should be in your APIs:

```java
package com.wordnik.swagger.sample.resource;

@Path("/resources/pet")
@Api(value = "/pet", 
	description = "Operations about pets",
	listingPath="/resources.json/pet")
@Produces({"application/json"})
public class PetResource {
  /* do something here */
}
```

Note in this scenario, you are putting the API listing under this location:  `http://localhost:8080/resources.json/pet`

Next, you'll need to create a resource to respond to the listing path requests:

```java
@Path("/resources.json/pet")
@Api(value = "/pet",
  description = "Operations about pets",
  listingPath = "/resources.json/pet",
  listingClass = "com.wordnik.swagger.sample.resource.PetResource")
@Produces({"application/json"})
public class PetResourceListing extends JavaHelp {}
```

In the above code block, you're specifying that the /pet API will be described under `http://localhost:8080/resources.json/pet`.
You are also telling it *which* class has the annotations for the description `com.wordnik.swagger.sample.resource.PetResource`.  Finally,
the noop code is OK--there's nothing to do in this method.

## Troubleshooting

If you are accessing a swagger-powered API from browser (via javascript), it is likely that you need to set a cross-origin filter in your service.  Specifically the UI, if not deployed on the same host as the API will receive an error when attempting to make AJAX calls to the server.

You can test your cross-origin server settings by the following:

```
# example: curl -i http://api.wordnik.com/v4/resources.json

curl -i http://YOUR_SERVER/PATH_TO_RESOURCE_LISTING

HTTP/1.1 200 OK
Access-Control-Allow-Headers: Origin, X-Atmosphere-tracking-id, X-Atmosphere-Framework, X-Cache-Date, Content-Type, X-Atmosphere-Transport, *
Access-Control-Allow-Methods: POST, GET, OPTIONS , PUT
**Access-Control-Allow-Origin: ***
Access-Control-Request-Headers: Origin, X-Atmosphere-tracking-id, X-Atmosphere-Framework, X-Cache-Date, Content-Type, X-Atmosphere-Transport,  *
Content-Type: application/json
Date: Mon, 02 Jul 2012 19:23:14 GMT
Wordnik-API-Version: 4.11.7
Content-Length: 319
Connection: keep-alive
```

Note the `Access-Control-Allow-Origin: *` line from the server.  You can add a filter to return this header as follows:

Creating a filter:

https://github.com/wordnik/swagger-core/blob/master/samples/java-jaxrs/src/main/java/com/wordnik/swagger/sample/util/ApiOriginFilter.java

Adding the filter to the web service:

```xml
  <filter>
    <filter-name>ApiOriginFilter</filter-name>
    <filter-class>com.wordnik.swagger.sample.util.ApiOriginFilter</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>ApiOriginFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
```

<li> Note that jersey 1.10 or greater requires you to add some additional artifacts which are currently not in the samples:

```xml
      <dependency>
        <groupId>com.sun.jersey</groupId>
        <artifactId>jersey-core</artifactId>
        <version>${jersey-version}</version>
        <scope>compile</scope>
      </dependency>
      <dependency>
        <groupId>com.sun.jersey</groupId>
        <artifactId>jersey-server</artifactId>
        <version>${jersey-version}</version>
        <scope>compile</scope>
      </dependency>
      <dependency>
        <groupId>com.sun.jersey</groupId>
        <artifactId>jersey-servlet</artifactId>
        <version>${jersey-version}</version>
        <scope>compile</scope>
      </dependency>
```