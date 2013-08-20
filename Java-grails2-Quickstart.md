==========

Swagger is supported in Grails + Jersey 1.x.

```xml
<dependency>
  <groupId>com.wordnik</groupId>
  <artifactId>swagger-jaxrs_2.9.1</artifactId>
  <version>1.3.0</version>
</dependency>
```

This adds a swagger module for JAX-RS, along with all the required dependencies.

#### Specify the swagger package

You need to tell grails about the new swagger packages to scan.  This is typically done in the `config.groovy` file:

```groovy
// set the url mappings.  These paths are passed to JAXRS, and need to include the swagger resource listing and the apis themselves.

org.grails.jaxrs.url.mappings = ['/api','/api-docs']

// give the scanning package for the swagger resource listings, along with your apis
org.grails.jaxrs.provider.init.parameters = [
  'com.sun.jersey.config.property.packages': 
    'com.wordnik.swagger.sample.resource;com.wordnik.swagger.jaxrs.listing']
```

This tells grails to map requests to `/api` and `/api-docs` to JAXRS.  In addition the scanning packages will include YOUR apis, in package `com.wordnik.swagger.sample.resource` (of course, replace with your implementation) and the swagger listings under `com.wordnik.swagger.jaxrs.listing`.  In addition, it will find the JSON writers for the Resource Listing and Api Declaration, which will properly marshal the swagger objects into JSON without monkeying with your default JSON processors.

Now when grails starts up, it'll find the JAXRS packages.

Next, we need to configure grails with a swagger configurator.  This can be done in one of three ways:

1) In your BootStrap.groovy.  This is how you'd do so programmatically

2) In a `web.xml`.  If you use the web.xml plugin, you can configure a servlet to start-up and configure swagger.

3) In an `applicationContext.xml`.  If you use spring, which you probably do if you're using grails, you can configure swagger in a bean.  Let's follow that technique:

```xml
<!-- this scans the classes for resources -->
<bean id="swaggerConfig" class="com.wordnik.swagger.jaxrs.config.BeanConfig">
  <property name="resourcePackage" value="com.wordnik.swagger.sample.resource"/>
  <property name="version" value="1.0.0"/>
  <property name="basePath" value="http://localhost:8080/java-grails2/api"/>
  <property name="title" value="Petstore sample app"/>
  <property name="description" value="This is a app."/>
  <property name="contact" value="apiteam@wordnik.com"/>
  <property name="license" value="Apache 2.0 License"/>
  <property name="licenseUrl" value="http://www.apache.org/licenses/LICENSE-2.0.html"/>
  <property name="scan" value="true"/>
</bean>
```

This tells Swagger what package to scan for `@Api` annotations, sets the version of the app and provides additional metadata about the API.  The final line, `scan=true` tells the bean to do the actual work (this should always be the last property in the bean).

Recall, the `swagger.api.basepath` should be configured to the _external, public address_ of your API.

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

Assuming you're running on the default port of `8080` and grails is mounted on `/java-grails2`, you should be able to view your resource listing here:

[http://localhost:8080/java-grails2/api-docs](http://localhost:8080/java-grails2/api-docs)

and any subsequent api declarations as follows:

[http://localhost:8080/java-grails2/api-docs/{path}](http://localhost:8080/java-grails2/api-docs/{path})

Finally, view the UI here:

[http://localhost:8080/java-grails2](http://localhost:8080/java-grails2)

You can see a working sample here, which uses Java + grails with Tomcat:

[scala-servlet](https://github.com/wordnik/swagger-core/blob/master/samples/java-grails2)

#### Wrap up

This is a very short getting started guide for Java + grails + JAXRS.  There are many options and configurations to swagger which can be explored in the more detailed guides.