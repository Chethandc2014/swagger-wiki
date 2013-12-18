==========

The purpose of info section is define metadata about the API. Metadata is presented directly in the [swagger-ui](https://github.com/wordnik/swagger-ui) console for easy access. Here is example:

```json
  "info": {
    "title": "Swagger Sample App",
    "description": "This is a sample server Petstore server.",
    "termsOfServiceUrl": "http://helloreverb.com/terms/",
    "contact": "apiteam@wordnik.com",
    "license": "Apache 2.0",
    "licenseUrl": "http://www.apache.org/licenses/LICENSE-2.0.html"
  }
```

The `info` itself is optional and located under [Resource Listing](Resource-Listing).

The `title` and `description` are required and other are optional.