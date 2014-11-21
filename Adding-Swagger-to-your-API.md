==========

Before starting to integrate with Swagger there are some decisions to make.  There are many different ways to integrate, so let's look through some options.

### Question: Is Swagger integrated with the server runtime?

This is probably the most important question to answer first.  You can serve swagger JSON which is hand crafted, and not even hosted on your server.  The downside is that, without a proper server integration, the swagger JSON can grow stale and not keep up with the server integration.  In addition, performing runtime filtering based on permissions is pretty difficult without a server integration.

#### Answer: No!  You don't want any server integration

You can create Swagger JSON and serve them up on any server.  The JSON needs to conform to the [Swagger Spec](https://github.com/swagger-api/swagger-spec) with the Api Declaration having the `basePath` pointing directly to the target server.

[See here](No-server-Integrations) for your options.

#### Answer: Yes! You want server integration

Server integration means dynamically providing JSON at runtime.  Within a server integration with Swagger, there are still various levels, depending on the language, framework, and implementation.

Check out the [Java integrations] (Java-Integrations). 