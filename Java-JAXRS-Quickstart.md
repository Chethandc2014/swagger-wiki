==========

Java integration has many flavors depending on the java framework you're using.

### Java + Jersey + JAX-RS Quickstart

Swagger is supported in Jersey 1.x.  This is probably the most popular Java integration--the swagger code is written in `scala` but it is fully compatible with Java.  There is a Swagger-Jersey-JAXRS module which can be added to your project with this dependency:

```xml
<dependency>
  <groupId>com.wordnik</groupId>
  <artifactId>swagger-jersey-jaxrs_2.9.1</artifactId>
  <version>1.3.0-RC2</version>
</dependency>
```

This adds a jersey-dependent swagger module for JAX-RS, along with all the required dependencies.

#### Specify the swagger package

First, we'll tell jersey how to find the swagger resource listing--this is done by configuring the package that jersey scans on startup.

Your web.xml should contain something like the following, when using Jersey as either a servlet:

```xml
<servlet>
  <servlet-name>jersey</servlet-name>
  <servlet-class>com.sun.jersey.spi.container.servlet.ServletContainer</servlet-class>
  <init-param>
    <param-name>com.sun.jersey.config.property.packages</param-name>
    <param-value>{your-packages};com.wordnik.swagger.jaxrs.listing</param-value>
  </init-param>
  ...
</servlet>
```

Of course, replace {your-packages} with the packages to your actual resources.  Now swagger will be loaded by jersey and can respond to requests for the Resource Listing and Api Declaration.

#### Specify a Swagger Configuration Class

The default configuration class is `DefaultJaxrsConfig`, which reads configuration values from your `web.xml`:

```xml
<servlet>
  <servlet-name>DefaultJaxrsConfig</servlet-name>
  <servlet-class>com.wordnik.swagger.jaxrs.config.DefaultJaxrsConfig</servlet-class>
  <init-param>
    <param-name>api.version</param-name>
    <param-value>1.0.0</param-value>
  </init-param>
  <init-param>
    <param-name>swagger.api.basepath</param-name>
    <param-value>http://localhost:8002/api</param-value>
  </init-param>
  <load-on-startup>2</load-on-startup>
</servlet>
```

The `swagger.api.basepath` should be configured to the _external, public address_ of your API.

#### Annotate your resources

Swagger only scans resources which have an `@Api` annotation on them.  Each `@Api` resource will appear as an Api Declaration in swagger.  The annotation is done as follows:

```java
import com.wordnik.swagger.annotations.*;

@Path("/pet")
@Api(value = "/pet", description = "Operations about pets")
@Produces({"application/json", "application/xml"})
public class PetResource {
  ...
```

Swagger will list your api under `/pet` as well with the description specified, and also tells swagger that the the API produces `application/json` and `application/xml` content types.

#### Annotate your methods

Annotating your methods and parameters gives extra info to your API consumers:

```java
@GET
@Path("/{petId}")
@ApiOperation(value = "Find pet by ID", notes = "More notes about this method", response = Pet.class)
@ApiResponses(value = {
  @ApiResponse(code = 400, message = "Invalid ID supplied"),
  @ApiResponse(code = 404, message = "Pet not found") 
})
public Response getPetById(
    @ApiParam(value = "ID of pet to fetch", required = true) @PathParam("petId") String petId)
    throws WebApplicationException {
```

This tells us that this REST operation has the short description `Find pet by ID` and has a longer notes section.  The response from this operation is a `Pet` object.  It has two `@ApiResponses` for non-success modes.  Finally, the only input required for the operation is a `petId` of type `String`, which is required.

#### Annotate your models

While not required, you can annotate your models to provide more descriptions or override certain values

```java
@ApiModel(value = "A pet is a person's best friend")
@XmlRootElement(name = "Pet")
public class Pet {
  @XmlElement(name = "status")
  @ApiModelProperty(value = "Order Status", required=true, allowableValues = "placed,approved,delivered")
  public void setStatus(String status) {
    this.status = status;
  }
  public String getStatus() {
    return status;
  }
```

This gives the `Pet` model a description, describes `status` and tells us that it is `required` and should be either `placed`, `approved` or `delivered`.


#### Wrap up

This is a very short getting started guide for Java + JAXRS.  There are many options and configurations to swagger which can be explored in the more detailed guides.