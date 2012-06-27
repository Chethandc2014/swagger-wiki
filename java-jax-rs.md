Java + JAX-RS
==========

Swagger supports the Jersey framework for integration with JAX-RS.

#### Integration Steps

First, include the swagger artifacts in your project.  If using maven, add to your `pom.xml`:

````
  <dependencies>
    <dependency>
      <groupId>com.wordnik</groupId>
      <artifactId>swagger-jaxrs_2.9.1</artifactId>
      <version>1.0.1</version>
      <scope>compile</scope>
    </dependency>
    <dependency>
      <groupId>com.wordnik</groupId>
      <artifactId>swagger-core_2.9.1</artifactId>
      <version>1.0.1</version>
      <scope>compile</scope>
    </dependency>
  </dependencies>
````

And with ivy, add to your `ivy.xml`:

````
  <dependency org="com.wordnik" name="swagger-jaxrs_2.9.1" rev="1.0.1"/>
  <dependency org="com.wordnik" name="swagger-core_2.9.1" rev="1.0.1"/>
````

Next, you need to tell jersey how to find the [Resource Listing](Resource-Listing) class--this is done in one of
two ways:

One: If you use your `web.xml` to declare the packages to scan on startup of jersey, you will have something like this:

````
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
````

Note the addition to `com.wordnik.swagger.jaxrs` in the `param-value`.  Why?  Because that tells jersey to scan &
register the [resource listing](/wordnik/swagger-core/blob/master/modules/swagger-jaxrs/src/main/scala/com/wordnik/swagger/jaxrs/JavaApiListing.scala), which is in that package in the swagger-jaxrs module.

Two: If you use a Jersey Application to configure your project, you'll likely have something like this in your `web.xml`:

````
  <init-param>
    <param-name>javax.ws.rs.Application</param-name>
    <param-value>com.your.project.RestApplication</param-value>
  </init-param>
````

and this class:
````
package com.your.project;

import com.sun.jersey.api.core.PackagesResourceConfig;

public class RESTApplication extends PackagesResourceConfig {
	public RESTApplication() {
		super("com.your.project.resources");
	}
}
````
Note!  There is no `com.sun.jersey.config.property.packages` configuration.  Don't add one!  If this is your setup, you have
to extend the `JavaApiListing` class in the same package as above:

````
package com.your.project;

import com.wordnik.swagger.annotations.*;
import com.wordnik.swagger.jaxrs.JavaApiListing;

import javax.ws.rs.*;

@Path("/resources.json")
@Api("/resources")
@Produces({"application/json"})
public class ApiListingResource extends JavaApiListing {}
````

Now you have one last step before adding APIs-configuring the `basePath` for your application.  Recall that the
Resource Listing contains references to other APIs in your app--they are based on the `basePath` in your app
(note! We also snuck in an `api.version` param, which is purely informational to your consumers).

In your web.xml:

````
  <init-param>
    <param-name>swagger.api.basepath</param-name>
    <param-value>http://localhost:8080</param-value>
  </init-param>
  <init-param>
    <param-name>api.version</param-name>
    <param-value>1.0</param-value>
  </init-param>
````

Note! This should match the `servlet-mapping` path to your jersey servlet:

````
  <servlet-mapping>
    <servlet-name>jersey</servlet-name>
    <url-pattern>/*</url-pattern>
  </servlet-mapping>
````

if you, for instance, had your REST api on /api, you would have `http://localhost:8080/api` in the `swagger.api.basepath` and
`/api/*` in your `url-pattern` config.

You should be able to add APIs now.  This is done by annotating your APIs like this:

````
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
    /* your resource logic */
  }
```` 

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

````
http://localhost:8080/resources.json
````

This should tell Swagger to scan all the APIs available and produce a listing of them:

````
{
  apiVersion: "1.0",
  swaggerVersion: "1.0",
  basePath: "http://localhost:8080",
  apis: 
  [
    {
      path: "/pet.{format}",
      description: "Operations about pets"
    }
  ]
}
````

Following the link in the resource listing, you can now read the pet resource:

````
http://localhost:8080/pet.json
````

This should produce something like this:

````
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
````

Note!  You can see from above that this example assumes Swagger has the GET method on the root API!  That means,
you are not using `http://localhost:8080/pet.json` for any other operations.  This may not be the case, in which
you need to take an alternative listing scheme.  See [java-alt-resource-listing](/wordnik/swagger-core/tree/master/samples/java-alt-resource-listing) for
an example.


#### Other configurations

<li> You might not like the .{format} in the resource listing or in the api itself.  Don't fret!  You can configure this, see
[scala-jaxrs-no-format](/wordnik/swagger-core/tree/master/samples/scala-jaxrs-no-format) for example.</li>

#### Troubleshooting

<li> Note that jersey 1.10 or greater requires you to add some additional artifacts than what are currently in the sample:

````
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
````
