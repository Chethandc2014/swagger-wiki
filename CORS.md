==========

### A note about CORS

CORS is a technique to prevent websites from doing bad things with your personal data.  Most browsers + javascript toolkits not only support CORS but enforce it, which has implications for your API server which supports Swagger.

You can read about CORS here:

[http://www.w3.org/TR/cors/](http://www.w3.org/TR/cors/)

Here's a brief on what you need to do on your server to support Swagger.

_You can stop reading if you host swagger-ui on the same domain as your API!  You do not need CORS support for Swagger in this situation!_

-----

Your resources need to respond to the HTTP `options` request for the following:

* Your Swagger Resource Listing (i.e. http://petstore.swagger.wordnik.com/api/api-docs)

* Your Swagger Api Declarations (i.e. http://petstore.swagger.wordnik.com/api/api-docs/pet)

* Your actual APIs, if you want to use the _try it now_ feature

-----

#### How do you know if you support CORS?

You can verify CORS support with one of two techniques.

1) Curl your API and inspect the headers.  For instance:

```bash
$ curl -I "http://petstore.swagger.wordnik.com/api/api-docs"
HTTP/1.1 200 OK
Date: Thu, 12 Sep 2013 17:05:44 GMT
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, DELETE, PUT, PATCH, OPTIONS
Access-Control-Allow-Headers: Content-Type, api_key, Authorization
Content-Type: application/json
Content-Length: 0
```

This tells us that the petstore resource listing supports OPTIONS, and the following headers:  `Content-Type`, `api_key`, `Authorization`

2)  Try swagger-ui from your file system and look at the debug console.  If CORS is not enabled, you'll see something like this:

```
XMLHttpRequest cannot load http://sad.server.com/v2/api-docs. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'null' is therefore not allowed access. 
```

Swagger-UI cannot easily show this error state.


#### How do I enable CORS support?

There are many ways to support CORS which depend completely on how you have integrated Swagger.  Here are some examples:

* Using java/scala + servlets, you can add filter such as
[com.wordnik.swagger.sample.util.ApiOriginFilter](https://github.com/wordnik/swagger-core/blob/master/samples/java-jaxrs/src/main/java/com/wordnik/swagger/sample/util/ApiOriginFilter.java)

This requires the filter being initialized in the `WEB-INF/web.xml`.  Please see the sample [web.xml](https://github.com/wordnik/swagger-core/blob/master/samples/java-jaxrs/src/main/webapp/WEB-INF/web.xml) for this. Specifically, you need the bits that define the servlet filter using `ApiOriginFilter` and the `<filter-mapping>` for that filter.

* Using node.js + [swagger-node-express](https://github.com/wordnik/swagger-node-express):

https://github.com/wordnik/swagger-node-express/blob/master/Common/node/swagger.js#L70

You simply inject this in your express handler before the response is written

* nginx with static files

You can follow a strategy like such:

https://gist.github.com/michiel/1064640

For supporting CORS via nginx

#### What about header params?

Swagger lets you easily send headers as parameters to requests.  The name of these headers MUST be supported in your CORS configuration as well.  From our example above:

```
Access-Control-Allow-Headers: Content-Type, api_key, Authorization
```

Only headers with these names will be allowed to be sent by Swagger-UI