==========

Swagger is supported in Jersey 1.x.  This is probably the most popular Java integration--the swagger code is written in `scala` but it is fully compatible with Java.  There is a Swagger-Jersey-JAXRS module which can be added to your project with this dependency:

```xml
<dependency>
  <groupId>com.wordnik</groupId>
  <artifactId>swagger-jaxrs_2.9.1</artifactId>
  <version>1.3.0-RC3</version>
</dependency>
```

This adds a swagger module for JAX-RS, along with all the required dependencies.

#### Specify the swagger package

First, we'll tell jersey how to find the swagger resource listing--this is done by configuring the `applicationContext.xml` file which configures the server.  While you _can_ use a web.xml with CXF but in this example, we'll use the applicationContext instead

```xml
<!-- Swagger API listing resource -->
<bean id="swaggerResourceJSON" class="com.wordnik.swagger.jaxrs.listing.ApiListingResourceJSON" />

<!-- Swagger writers -->
<bean id="resourceWriter" class="com.wordnik.swagger.jaxrs.listing.ResourceListingProvider" />
<bean id="apiWriter" class="com.wordnik.swagger.jaxrs.listing.ApiDeclarationProvider" />

<bean class="org.apache.cxf.jaxrs.JAXRSServerFactoryBean" init-method="create">
  <property name="address" value="/" />
  <property name="serviceBeans">
    <list>
      <!-- your resources go here -->
      <!-- ... -->
      <!-- ... -->
      <!-- Swagger API Listing resource -->
      <ref bean="swaggerResourceJSON" />
    </list>
  </property>
  <property name="providers">
    <list>
      <!-- any other providers you need go here -->
      <!-- ... -->
      <!-- ... -->
      <!-- required for writing swagger classes -->
      <ref bean="resourceWriter" />
      <ref bean="apiWriter" />
    </list>
  </property>
</bean>
```

In the above, we've created the `swaggerResourceJSON` bean as the actual resource that writes the swagger JSON.  We also created two JAXRS `@Provider` resources called `resourceWriter` and `apiWriter` which do the actual marshalling of swagger objects into JSON.  Finally, we've added all these into the `create` method for the server to make them all live.

#### Specify a Swagger Configuration Class

To configure swagger, there's one more bean to add to the `applicationContext`:

```xml
<!-- this scans the classes for resources -->
<bean id="swaggerConfig" class="com.wordnik.swagger.jaxrs.config.BeanConfig">
  <property name="resourcePackage" value="com.wordnik.swagger.sample.resource"/>
  <property name="version" value="1.0.0"/>
  <property name="basePath" value="http://localhost:8002/api"/>
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

Assuming you're running on the default port of `8080` and CXF is mounted on `/*`, you should be able to view your resource listing here:

[http://localhost:8080/api-docs](http://localhost:8080/api-docs)

and any subsequent api declarations as follows:

[http://localhost:8080/api-docs/{path}](http://localhost:8080/api-docs/{path})

#### Example app

You can see a working sample here, which uses Java + CXF with Tomcat:

[scala-servlet](https://github.com/wordnik/swagger-core/tree/1.3-RC3/samples/java-jaxrs-cxf)

#### Wrap up

This is a very short getting started guide for Java + CXF + JAXRS.  There are many options and configurations to swagger which can be explored in the more detailed guides.