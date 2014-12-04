# Swagger-Core Annotations

In order to generate the Swagger documentation, swagger-core offers a set of annotations to declare and manipulate the output. The swagger-core output is compliant with [Swagger Specification](https://github.com/swagger-api/swagger-spec/blob/master/versions/1.2.md). A user is not required to be familiar with the full aspects of the Swagger Specification in order to use it, but as a reference it may answer a few questions regarding the generated output.  

This page introduces the annotations provided by swagger-core. They are grouped into three - the annotation to declare the resource, the set of annotations to declare an operation, and the set of annotations that declare API models. 

The documentation for each annotation is meant as an overview of its usage. Each annotation also has links to its javadocs (both on the header and at the end of the overview). The javadocs provide you with additional information about each annotation, especially dealing with some edge cases.

At the very least, `@Api` is required to declare an API resource and `@ApiOperation` is required to declare an API operation. Without having those two combined, no output will be generated. Servlets require `@ApiImplicitParam` to define the method parameters whereas JAX-RS based application can utilize the basic `@XxxxParam` annotations (`@QueryParam`, `@PathParam`...).

New in **1.3.9**: Annotations are now `@Inherited`. This means that defining them on interfaces or classes will affect the classes that implement/extend them.  

Table of contents:
* [Quick Annotation Overview](#quick-annotation-overview)
* [Resource API Declaration](#resource-api-declaration)
* [Operation Declaration](#operation-declaration)
* [Model Declaration](#model-declaration)

**For your convenience, the [javadocs](http://docs.swagger.io/swagger-core/apidocs) are available as well.**


## Quick Annotation Overview

Name | Description 
--- | --- 
[@Api](#api) | Marks a class as a Swagger resource.
[@ApiImplicitParam](#apiimplicitparam-apiimplicitparams) | Represents a single parameter in an API Operation.
[@ApiImplicitParams](#apiimplicitparam-apiimplicitparams) | A wrapper to allow a list of multiple ApiImplicitParam objects.
[@ApiModel](#apimodel) | Provides additional information about Swagger models.
[@ApiModelProperty](#apimodelproperty) | Adds and manipulates data of a model property.
[@ApiOperation](#apioperation) | Describes an operation or typically a HTTP method against a specific path.
[@ApiParam](#apiparam) | Adds additional meta-data for operation parameters. 
[@ApiResponse](#apiresponses-apiresponse) | Describes a possible response of an operation. 
[@ApiResponses](#apiresponses-apiresponse) | A wrapper to allow a list of multiple ApiResponse objects.
[@Authorization](#authorization-authorizationscope) | Declares an authorization scheme to be used on a resource or an operation.
[@AuthorizationScope](#authorization-authorizationscope) | Describes an OAuth2 authorization scope.

## Resource API Declaration

### [@Api](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/Api.html)

The `@Api` is used to declare a Swagger resource API. It serves a double purpose - it affects the Resource Listing *and* the API Declaration. Only classes that are annotated with `@Api` will be scanned by Swagger.

In the Resource Listing, the annotation will translate to the [Resource Object](https://github.com/swagger-api/swagger-spec/blob/master/versions/1.2.md#512-resource-object).

In the API Declaration, it will basically serve as the basis for the [API Declaration](https://github.com/swagger-api/swagger-spec/blob/master/versions/1.2.md#52-api-declaration) itself.

A JAX-RS usage would be:
```java
@Path("/pet")
@Api(value = "/pet", description = "Operations about pets")
@Produces({"application/json", "application/xml"})
public class PetResource {
 ...
}
```

Here we have a Pet resource that is exposed on `/pet`. The `@Api` here states that the *documentation* of this resource will be hosted under `/pet` as well (keep in mind it can be any URL) and there’s a description given to this resource. Swagger will pick up on the `@Produces` annotation but you can override this value if you wish. 

The output of the Resource Listing would be (as a value in the `apis` array):
```js
    {
      "path": "/pet",
      "description": "Operations about pets"
    }
```
A Servlet sample would be:
```java
@Api(value = "/sample/users", description = "gets some data from a servlet", consumes="application/json, application/xml")
public class SampleServlet extends HttpServlet {
```

In this case too, the *documentation* will be hosted at `/sample/users`. Notice that unlike with JAX-RS, this *has* to be the URL mapping of the Servlet. We also added a `consumes` property to the `@Api` to declare which content types are accepted by the exposed API.

The output of the Resource Listing would be (as a value in the `apis` array):
```js
    {
      "path": "/sample",
      "description": "Operations about pets"
    }
```
Note how the path is `"/sample"` and not `"/sample/users"`. The "/users" part will be used as the operation path.

`@Api` can also be used to declare authorization at the resource-level. These definitions apply to all operations under this resource, but can be overridden at the operation level if needed. This applies to both JAX-RS and Servlets. As a simplified example:
```java
  @Api(value = "/sample", 
  	authorizations = {
		  @Authorization(value="sampleoauth", scopes = {})
  	}
  )
```

In this example we're adding a previously-declared OAuth2 authorization scheme without any scopes. For further details, check the [@Authorization](#authorization-authorizationscope) annotation.

New in **1.3.7**: You can now define a specific `basePath` for a given API.

New in **1.3.8**: The boolean `hidden` property was added to the annotation. This can be used to entirely hide an @Api even if it declared. This is especially useful when using sub-resources to remove unwanted artifacts.

**For further details about this annotation, usage and edge cases, check out the [javadocs](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/Api.html).**

## Operation Declaration

### [@ApiOperation](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/ApiOperation.html)

The `@ApiOperation` is used to declare a single operation within an API resource. An operation is considered a unique combination of a path and a HTTP method. Only methods that are annotated with `@ApiOperation` will be scanned and added the API Declaration.

The annotation will affect two parts of the Swagger output, the [API Object](https://github.com/swagger-api/swagger-spec/blob/master/versions/1.2.md#522-api-object), which would be created one per path, and the [Operation Object](https://github.com/swagger-api/swagger-spec/blob/master/versions/1.2.md#523-operation-object), which would be created one per @ApiOperation.
Remember that when using Servlets, the `@Api` would affect the API Object instead as it sets the path.

A JAX-RS usage would be:
```java
 @GET
 @Path("/findByStatus")
 @ApiOperation(value = "Finds Pets by status",
    notes = "Multiple status values can be provided with comma seperated strings",
    response = Pet.class,
    responseContainer = "List")
 public Response findPetsByStatus(...) { ... }
```

The `value` of the annotation is a short description on the API. Since this is displayed in the list of operations in Swagger-UI and the location is limited in size, this should be kept short (preferably shorter than 120 characters). The `notes` allows you to give significantly more details about the operations (e.g. you can include request samples and responses here). `response` is the return type of the method. Notice that the actual method declaration returns a `Response` but that is a general-purpose JAX-RS class and not the actual response sent to the user. If the returned object is the actual result, it can be used directly instead of declaring it in the annotation. Since we want to return a list of pets, we declare that using the `responseContainer`. Keep in mind that Java has type erasure, so using generics in the return type may not be parsed properly, and the `response` should be used directly. The `@GET` JAX-RS annotation will be used as the (HTTP) `method` field of the operation, and the `@Path` would tell us the path of the operation (operations are grouped under the same path, one for each HTTP method used).

The output would be:
```js
    {
      "path": "/pet/findByStatus",
      "operations": [
        {
          "method": "GET",
          "summary": "Finds Pets by status",
          "notes": "Multiple status values can be provided with comma seperated strings",
          "type": "array",
          "items": {
            "$ref": "Pet"
          },
          "nickname": "findPetsByStatus",
          .
          .
          .
```

A Servlet sample would be:
```java
@ApiOperation(httpMethod = "GET", 
   value = "Resource to get a user", 
   response = SampleData.class, 
   nickname="getUser")
public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {...}
```
 
We already know what `value` and `response` here mean, based on the JAX-RS sample. Here, we have two additional properties. The `httpMethod` is used to explicitly declare which HTTP method is used in this operation, since we don't have that information like in JAX-RS. The `nickname` is also new and serves as unique name for the operation. In JAX-RS this would default to the method name and with Servlets it must be declared by the user for proper functionality. The `path` of the operation would be derived from the `@Api` annotation on the Servlet itself.

The output would be:
```js
         {
          "method": "GET",
          "summary": "Resource to get a user",
          "type": "SampleData",
          .
          .
          .
```

**For further details about this annotation, usage and edge cases, check out the [javadocs](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/ApiOperation.html).**

### [@ApiResponses](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/ApiResponses.html), [@ApiResponse](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/ApiResponse.html)

It's a common practice to return errors (or other success messages) using HTTP status codes. While the general return type of an operation is defined in the [@ApiOperation](#apioperation), the rest of the return codes should be described using these annotations.

The `@ApiResponse` describes a concrete possible response. It cannot be used directly on the method and needs to be included in the array value of `@ApiResponses` (whether there's one response or more).

If the response is accompanied with a body, the body model can be described as well (once model per response).

There's no difference in usage between usages (JAX-RS, Servlets or otherwise):
```java
  @ApiResponses(value = { @ApiResponse(code = 400, message = "Invalid ID supplied"),
      @ApiResponse(code = 404, message = "Pet not found") })
  public Response getPetById(...) {...}
```

**For further details about this annotation, usage and edge cases, check out the javadocs ([@ApiResponses](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/ApiResponses.html), [@ApiResponse](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/ApiResponse.html))**.

### [@Authorization](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/Authorization.html), [@AuthorizationScope](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/AuthorizationScope.html)

These annotations are used as input to [@Api](#api) and [@ApiOperation](#apioperation) only, and not directly on the resources and operations. Once you've declared and configured which authorization schemes you support in your API, you can use these annotation to note which authorization scheme is required on a resource or a specific operation. The `@AuthorizationScope` is specific to the case of an OAuth2 authorization scheme where you may want to specify specific supported scopes.

The @Authorization and @AuthorizationScope translate to the [Authorization Object](https://github.com/swagger-api/swagger-spec/blob/master/versions/1.2.md#5210-authorizations-object) and the [Scope Object](https://github.com/swagger-api/swagger-spec/blob/master/versions/1.2.md#5211-scope-object) respectively.

The behavior between the implementations (JAX-RS, Servlets or otherwise) is the same:
```java
  @ApiOperation(value = "Add a new pet to the store", 
  	authorizations = {
		  @Authorization(
				  value="petoauth", 
				  scopes = {
						  @AuthorizationScope(
								  scope = "add:pet", 
								  description = "allows adding of pets")
						  }
				  )
  	}
  )
  public Response addPet(...) {...}
```

In this case we declare that the `addPet` operation uses the `petoauth` authorization scheme (we'll assume it is an OAuth2 authorization scheme). Then using the `@AuthorizationScope` we fine-tune the definition by saying it requires the `add:pet` scope. As mentioned above, you can see that `@AuthorizationScope` is used as an input to `@Authorization`, and that in turn is used as input to `@ApiOperation`. Remember, these annotations can only be used as input to `@Api` and `@ApiOperation`. Using any of them directly on a class or a method will be ignored.

The output would be:
```js
  "authorizations": {
    "petoauth": [
      {
        "scope": "add:pet",
        "description": "allows adding of pets"
      }
    ]
  }
```

**For further details about this annotation, usage and edge cases, check out the javadocs ([@Authorization](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/Authorization.html), [@AuthorizationScope](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/AuthorizationScope.html))**.

### [@ApiParam](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/ApiParam.html)

The `@ApiParam` is used solely with the JAX-RS parameter annotations (`@PathParam`, `@QueryParam`, `@HeaderParam`, `@FormParam` and in JAX-RS 2, `@BeanParam`). While swagger-core scans these annotations by default, the `@ApiParam` can be used to add more details on the parameters or change the values as they are read from the code.

In the Swagger Specification, this translates to the [Parameter Object](https://github.com/swagger-api/swagger-spec/blob/master/versions/1.2.md#524-parameter-object).

Swagger will pick up the `value()` of these annotations and use them as the parameter name, and based on the the annotation it will also set the parameter type. For the body parameter (the single input parameter of a JAX-RS method), the name will automatically be set as `body` (as required by the Swagger Specification).

Swagger will also use the value of `@DefaultValue` as the default value property if one exists.

```java
 @Path("/{username}")
 @ApiOperation(value = "Updated user",
    notes = "This can only be done by the logged in user.")
public Response updateUser(
      @ApiParam(value = "name that need to be updated", required = true) @PathParam("username") String username,
      @ApiParam(value = "Updated user object", required = true) User user) {...}
```

Here we have two parameters. The first, `username` which is a part of the path. The second is the body, in this case a User object. Note that both parameters have the `required` property set to `true`. For the @PathParam, this is redundant as it is mandatory by default and cannot be overridden.

The output would be:
```js
 "parameters": [
            {
              "name": "username",
              "description": "name that need to be updated",
              "required": true,
              "type": "string",
              "paramType": "path",
              "allowMultiple": false
            },
            {
              "name": "body",
              "description": "Updated user object",
              "required": true,
              "type": "User",
              "paramType": "body",
              "allowMultiple": false
            }
          ]
```

**For further details about this annotation, usage and edge cases, check out the [javadocs](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/ApiParam.html).**

### [@ApiImplicitParam](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/ApiImplicitParam.html), [@ApiImplicitParams](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/ApiImplicitParams.html)

You may wish you describe operation parameters manually. This can be for various reasons, for example:
* Using Servlets which don't use JAX-RS annotations.
* Wanting to hide a parameter as it is defined and override it with a completely different definition.
* Describe a parameter that is used by a filter or another resource prior to reaching the JAX-RS implementation.

Since there can be several parameters to be included, the `@ApiImplicitParams` allows for multiple `@ApiImplicitParam` definitions.

In the Swagger Specification, these translate to the [Parameter Object](https://github.com/swagger-api/swagger-spec/blob/master/versions/1.2.md#524-parameter-object).

When defining parameters implicitly, it's important to set `name`, `dataType` and `paramType` for Swagger's definitions to be proper. 

```java
 @ApiImplicitParams({
    new ApiImplicitParam(name = "name", value = "User's name", required = true, dataType = "string", paramType = "query"),
    new ApiImplicitParam(name = "email", value = "User's email", required = false, dataType = "string", paramType = "query"),
    new ApiImplicitParam(name = "id", value = "User ID", required = true, dataType = "long", paramType = "query")
  })
 public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {...}
```

In the above sample we can see a Servlet definition with several parameters. The `dataType` can be either a primitive or a class name. The `paramType` can be any of the parameter types that are supported by Swagger (refer to the javadocs or the spec for further details).

```js
 "parameters": [
            {
              "name": "name",
              "description": "User's name",
              "required": true,
              "type": "string",
              "paramType": "query",
              "allowMultiple": false
            },
            {
              "name": "email",
              "description": "User's email",
              "required": false,
              "type": "string",
              "paramType": "query",
              "allowMultiple": false
            },
            {
              "name": "id",
              "description": "User ID",
              "required": true,
              "type": "integer",
              "format": "int64",
              "paramType": "query",
              "allowMultiple": false
            }
          ]
```

**For further details about this annotation, usage and edge cases, check out the javadocs ([@ApiImplicitParam](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/ApiImplicitParam.html), [@ApiImplicitParams](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/ApiImplicitParams.html))**.

## Model Declaration

### [@ApiModel](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/ApiModel.html)

Swagger-core builds the model definitions based on the references to them throughout the API introspection. The `@ApiModel` allows you to manipulate the meta data of a model from a simple description or name change to a definition of polymorphism.

This translates to the [Model Object](https://github.com/swagger-api/swagger-spec/blob/master/versions/1.2.md#527-model-object) in the Swagger Specification.

At its basic functionality, you an use `@ApiModel` to change the name of the model and add a description to it:
```java
@ApiModel(value="DifferentModel", description="Sample model for the documentation")
class OriginalModel {...}
```
Here we change the name of the model from OriginalModel to DifferentModel.

The output would be:
```js
 "DifferentModel": {
      "id": "DifferentModel",
      "description": "Sample model for the documentation",
      .
      .
  }
```
    

To support polymorphism and inheritance, we use the `discriminator` and the `subTypes` fields. Both must be used for the Swagger output to be valid. 

The `discriminator` field must be a field at the top model which will be used to determine which sub model is being used. For example, if you have an `Animal` class with `Cat`, `Dog` and `Chicken` as sub classes, the `animalType` field could be used as the discriminator to determine which animal is actually being used. 

The `subTypes` must list the classes of the inheriting models. The classes themselves don't have to inherit from the super type. In fact, Swagger will not automatically read the extending classes and you have to manually describe these classes in the `subTypes` in order for them to be parsed.

```java
@ApiModel(value="SuperModel", discriminator = "foo", subTypes = {SubModel.class})
public class SuperModel {...}

@ApiModel(value="SubModel")
public class SubModel {...}
```

The above snippet is a simple sample of how inheritance can be described. Notice SubModel does not extend SuperModel. In the same way, you can add multiple inheriting classes. There can be any number of inheritance levels.

The output for this would be:
```js
"SuperModel": {
  "id": "SuperModel",
  "required": [
    "foo"
  ],
  "properties": {
    "foo": {
      "type": "string"
    }
  },
  "subTypes": ["SubModel"],
  "discriminator": "foo"
},
"SubModel": {
  "id": "SubModel",
  "properties": {
     ...
  },
}
```

**For further details about this annotation, usage and edge cases, check out the [javadocs](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/ApiModel.html).**

### [@ApiModelProperty](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/ApiModelProperty.html)

While swagger-core will introspect fields and setters/getters, it will also read and process JAXB annotations. The `@ApiModelProperty` allows controlling Swagger-specific definitions such as allowed values, and additional notes. It also offers additional filtering properties in case you want to hide the property in certain scenarios.

For information about this in the Swagger Spec, check out the [Property Object](https://github.com/swagger-api/swagger-spec/blob/master/versions/1.2.md#529-property-object).

```java
  @ApiModelProperty(value = "pet status in the store", allowableValues = "available,pending,sold")
  public String getStatus() {
    return status;
  }
```

This is a simple example of adding a short description to the model property. It can also be observed that while `status` is a String, we document it as having only three possible values.

The output of it would be:
```js
  "properties": {
        ...,
        "status": {
          "type": "string",
          "description": "pet status in the store",
          "enum": [
            "available",
            "pending",
            "sold"
          ]
        }
      }
```

**For further details about this annotation, usage and edge cases, check out the [javadocs](http://docs.swagger.io/swagger-core/apidocs/index.html?com/wordnik/swagger/annotations/ApiModelProperty.html).**