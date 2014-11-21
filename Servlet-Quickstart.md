==========

Swagger is supported with pure servlets, requiring no integration with a REST framework.  This requires, of course, that the developer handle all the mapping of requests into the raw servlet, and that's no small feat.  However, there are many times that servlets alone are the fastest solution, so to that point, swagger can be integrated.

To support servlets, add this dependency:

```xml
<dependency>
  <groupId>com.wordnik</groupId>
  <artifactId>swagger-servlet_2.10</artifactId>
  <version>1.3.10</version>
</dependency>
```

This adds a swagger module for servlets, along with all the required dependencies.

#### Add the Swagger-servlet reader

First, we'll configure the swagger servlet reader which loads on startup:

```xml
<!-- swagger servlet reader -->
<servlet>
  <servlet-name>DefaultServletReaderConfig</servlet-name>
  <servlet-class>com.wordnik.swagger.servlet.config.DefaultServletReaderConfig</servlet-class>
  <load-on-startup>2</load-on-startup>
  <init-param>
    <param-name>swagger.resource.package</param-name>
    <param-value>com.wordnik.swagger.sample.servlet</param-value>
  </init-param>
  <init-param>
    <param-name>swagger.api.basepath</param-name>
    <param-value>http://localhost:8002</param-value>
  </init-param>
  <init-param>
    <param-name>api.version</param-name>
    <param-value>1.0.0</param-value>
  </init-param>
</servlet>
```

In the above, we're telling the `DefaultServletReaderConfig` to run when the container starts.  It will look for classes with `@Api` annotations in the package defined by the param `swagger.resource.package`.  It sets the `basePath` to `http://localhost:8002` and the `api.version` to `1.0.0`.

#### Add the Swagger servlet

Now we'll add the swagger servlet, which responds to the requests for the resource listing and Api Declaration to the `web.xml`:

```xml
<!-- swagger api declaration -->
<servlet>
  <servlet-name>ApiDeclarationServlet</servlet-name>
  <servlet-class>com.wordnik.swagger.servlet.listing.ApiDeclarationServlet</servlet-class>
</servlet>
<servlet-mapping>
  <servlet-name>ApiDeclarationServlet</servlet-name>
  <url-pattern>/api-docs/*</url-pattern>
</servlet-mapping>
```

In the above, we are adding the `ApiDeclarationServlet` to listen to `/api-docs/*`.

#### Annotate your resources

Swagger scans all classes with an `@Api` annotation on them.  Each `@Api` resource will appear as an Api Declaration in swagger.  The annotation is done as follows:

```java
import com.wordnik.swagger.annotations.*;

@Api(value = "/sample/users", description = "gets some data from a servlet")
public class SampleServlet extends HttpServlet {
  ...
```

Swagger will list your api under `/sample/users` as well with the description specified.  Note, it is _your_ duty to make sure this servlet is actually mounted under that path in your `web.xml`!

#### Annotate your methods

We need to first tell Swagger about the operation itself, so we add an `@ApiOperation` annotation to our method.  Here we specify the `httpMethod`, the `response` datatype and the `nickname`.  Also, since servlets respond to `doPost` and `doGet` only, you have to implicitly declare what the inputs are to the requests.  This is done by attaching the `@ApiImplicitParams` to the get or post methods:

```java
@ApiOperation(httpMethod = "GET", value = "Resource to get a user", 
  response = SampleData.class, nickname="getUser")
@ApiResponses(Array(new ApiResponse(code = 400, message = "Invalid input", response = ApiResponse.class)))
@ApiImplicitParams({
  @ApiImplicitParam(name = "name", value = "User's name", required = true, dataType = "string", paramType = "query"),
  @ApiImplicitParam(name = "email", value = "User's email", required = true, dataType = "string", paramType = "query"),
})
@Override
public void doGet(HttpServletRequest request, HttpServletResponse response) {

```

Now this operation returns an object of type `SampleData` for the `GET` http method.  It may return a `400` response if the input is invalid, and it requires both the `name` and `email` query parameters as inputs.  Again, extracting these values from the request is up to the developer.

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

Assuming you're running on the default port of `8080` and your Swagger `ApiDeclarationServlet` as shown above, you should be able to view your resource listing here:

[http://localhost:8080/api-docs](http://localhost:8080/api-docs)

and any subsequent api declarations as follows:

[http://localhost:8080/api-docs/{path}](http://localhost:8080/api-docs/{path})

#### Example app

You can see a working sample here, which uses scala + servlets:

[scala-servlet](https://github.com/swagger-api/swagger-core/blob/master/samples/scala-servlet)

#### Wrap up

This is a very short getting started guide for Java + Servlets.  There are many options and configurations to swagger which can be explored in the more detailed guides.
