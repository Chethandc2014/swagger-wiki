In some cases, you may need to add CORS support to enable web-based tool to function properly. This is true for [swagger-ui](https://github.com/swagger-api/swagger-ui/blob/master/README.md#cors-support), for example.

*Remember that in some cases, your organization already provides a proxy that enables CORS support which requires no further action from you as a developer.*

Using Java/Scala + servlets, you can add filter such as
[io.swagger.sample.util.ApiOriginFilter](https://github.com/swagger-api/swagger-samples/blob/master/java/java-jaxrs/src/main/java/io/swagger/sample/util/ApiOriginFilter.java).

This requires the filter being initialized in the `web.xml`. Please see the sample [web.xml](https://github.com/swagger-api/swagger-samples/blob/master/java/java-jaxrs/src/main/webapp/WEB-INF/web.xml#L55) for this. Specifically, you need the bits that define the servlet filter using `ApiOriginFilter` and the `<filter-mapping>` for that filter.