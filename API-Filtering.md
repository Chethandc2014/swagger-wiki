API Filtering allows Swagger to maintain multiple API versions. The root getResorces method has a new parameter called filter. This could be any string, but is typically a version ID.

The filter is used to match Controllers that are annotated with a filter parameter in their @Api annotation.

In this case, we also declared a "default" version. Typically this would point to the earliest supported version of your API. However, this causes a difficulty with the path. Paths must be unique, so "/v1/users" is a good path. But, this will cause all Swagger paths to become something like /api-docs/v1/users, when we just want /api-docs/users/. So, a pathAlias parameter was also introduced to the @Api annotation to solve this problem.

## Example (Scala Play2)

### conf/routes
```
GET     /api-docs                  controllers.ApiHelpController.getResources(filter = "vDefault")
GET     /v1/api-docs               controllers.ApiHelpController.getResources(filter = "v1")
GET     /v2/api-docs               controllers.ApiHelpController.getResources(filter = "v2")

GET     /api-docs/users            controllers.ApiHelpController.getResource(path = "/vDefault/users")
GET     /v1/api-docs/users         controllers.ApiHelpController.getResource(path = "/v1/users")
GET     /v2/api-docs/users         controllers.ApiHelpController.getResource(path = "/v2/users")
```

### app/controllers/v1/UserController.scala
```
package controllers.v1

@Api(value = "/v1/users", filter = "v1", pathAlias = "/users", description = "Operations on Users")
class UserController extends Controller {
...
}
```

### app/controllers/v2/UserController.scala
```
package controllers.v2

@Api(value = "/v2/users", filter = "v2", pathAlias = "/users", description = "Operations on Users")
class UserController extends Controller {
...
}
```

### app/controllers/UserController.scala
```
package controllers

@Api(value = "/vDefault/users", filter = "vDefault", pathAlias = "/users", description = "Operations on Users")
object UserController extends v1.UserController
```