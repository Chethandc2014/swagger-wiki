==========

Dropwizard uses Jersey + JAXRS, both which are supported with Swagger.  The swagger dependencies are written in `scala` but it is fully compatible with Java.  There is a Swagger-JAXRS module which can be added to your project with this dependency:

```xml
<dependency>
  <groupId>com.wordnik</groupId>
  <artifactId>swagger-jaxrs_2.10</artifactId>
  <version>1.3.10</version>
</dependency>
```

#### Add Swagger to your Service

Adding Swagger to your Service is done by adding three components--the two `@Provider` classes and the `ApiListingResource`.  The Scanner is then configured

```java
public class SwaggerSampleService extends Service<SwaggerSampleConfiguration> {
  public static void main(String[] args) throws Exception {
    new SwaggerSampleService().run(args);
  }

  @Override
  public void initialize(Bootstrap<SwaggerSampleConfiguration> bootstrap) {
    bootstrap.setName("swagger-sample");
  }

  @Override
  public void run(SwaggerSampleConfiguration configuration, Environment environment) {
    // Swagger Resource
    environment.addResource(new ApiListingResourceJSON());

    // Swagger providers
    environment.addResource(new ApiDeclarationProvider());
    environment.addResource(new ResourceListingProvider());

    // Swagger Scanner, which finds all the resources for @Api Annotations
    ScannerFactory.setScanner(new DefaultJaxrsScanner());

    // Add the reader, which scans the resources and extracts the resource information
    ClassReaders.setReader(new DefaultJaxrsApiReader());

    // Set the swagger config options
    SwaggerConfig config = ConfigFactory.config();
    config.setApiVersion("1.0.1");
    config.setBasePath("http://localhost:8000");
  }
}
```

If you are running swagger-ui on a different host than the actual API, you'll probably want to add CORS support, via jetty servlet, in your pom.xml:

```xml
<dependency>
  <groupId>org.eclipse.jetty</groupId>
  <artifactId>jetty-servlets</artifactId>
  <version>8.1.10.v20130312</version>
</dependency>

```
... and in your run routine:

```java
// optional CORS support
environment.addFilter(CrossOriginFilter.class, "/*");
```

In the above, we did everything we need to configure Dropwizard to use Swagger.  Now all that's left is to annotate our resources.

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

#### Test your configuration

Assuming you're running on the default port of `8080` and your servlet mapping is as shown in the above web.xml snippet, you should be able to view your resource listing here:

[http://localhost:8080/api-docs](http://localhost:8080/api-docs)

and any subsequent api declarations as follows:

[http://localhost:8080/api-docs/{path}](http://localhost:8080/api-docs/{path})

#### Example app

You can see a working sample here, which uses Java + Dropwizard:

[java-dropwizard](https://github.com/swagger-api/swagger-core/blob/master/samples/java-dropwizard)

#### Wrap up

This is a very short getting started guide for Java + dropwizard.  There are many options and configurations to swagger which can be explored in the more detailed guides.