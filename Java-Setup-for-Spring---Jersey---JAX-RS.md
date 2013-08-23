If you are using Spring+Jersey, you might have configuration like this:

    <bean id="restController" class="org.springframework.web.servlet.mvc.ServletWrappingController">
        <property name="servletClass" value="com.sun.jersey.spi.spring.container.servlet.SpringServlet"/>
        <property name="initParameters">
            <props>
                <prop key="com.sun.jersey.api.json.POJOMappingFeature">true</prop>
                <prop key="contextConfigLocation">classpath*:com/business/beans.xml</prop>
                <prop key="com.sun.jersey.spi.container.ContainerRequestFilters">...</prop>
                <prop key="com.sun.jersey.spi.container.ContainerResponseFilters">...</prop>
            </props>
        </property>
    </bean>

This might also be specified in `web.xml`.

In this environment, you can't use the Default JAX-RS Swagger configuration, mainly because the `@Context Application` doesn't contain the correct `getSingletons()` and `getClasses()` that Swagger uses to generate the API listings.  This is because Spring must manage these beans in order for Jersey to use them, so you end up needing to declare each of the Swagger resources in your Spring XML file.

In the above code block, we are loading a `beans.xml` Spring file.  Add these beans to this file:

    <bean class="com.wordnik.swagger.jaxrs.listing.ApiListingResourceJSON"/>
    <bean class="com.wordnik.swagger.jaxrs.listing.ApiDeclarationProvider" scope="singleton"/>
    <bean class="com.wordnik.swagger.jaxrs.listing.ResourceListingProvider" scope="singleton"/>
    <bean class="com.business.rest.SwaggerConfigurer" init-method="init"/>

Create the `SwaggerConfigurer` class like so:

    public class JamaSwaggerConfigurer {
        public void init() {
            ReflectiveJaxrsScanner scanner = new ReflectiveJaxrsScanner();
            scanner.setResourcePackage("com.business.rest.resources"); //your "resources" package
            ScannerFactory.setScanner(scanner);
            ClassReaders.setReader(new DefaultJaxrsApiReader());
            
            // Set the swagger config options
            SwaggerConfig config = ConfigFactory.config();
            config.setApiVersion("1.0.0");
            config.setBasePath("http://localhost:8080/contour/rest"); //your "base" url
        }
    }


### Notes on Swagger-UI and Cross-Domain Requests

If Swagger UI is not installed alongside your web application, it will typically be on a different domain.  In order for the Swagger UI code to use AJAX and make requests to your application, you need to configure it to allow for cross-domain requests.

Add an `ApiOriginFilter` to your Jersey application's `ContainerResponseFilters`.  In the above Spring XML file, this might look like:

    <prop key="com.sun.jersey.spi.container.ContainerResponseFilters">com.business.ApiOriginFilter</prop>

The `ApiOriginFilter` should look something like this:

    public class ApiOriginFilter implements ContainerResponseFilter {
        @Override
        public ContainerResponse filter(ContainerRequest creq, ContainerResponse cresp) {
            cresp.getHttpHeaders().putSingle("Access-Control-Allow-Origin", "*");
            cresp.getHttpHeaders().putSingle("Access-Control-Allow-Methods", "GET, POST, DELETE, PUT, OPTIONS");
            cresp.getHttpHeaders().putSingle("Access-Control-Allow-Headers", "Content-Type, Accept, Authorization, api_key");
            return cresp;
        }
    }

In this example, all requests would be going through a Spring DispatcherServlet, defined in your web.xml.  Since CORS requests require an OPTIONS call to be answered correctly, you need to tell this dispatcher to allow OPTIONS requests.  You do so by providing the `dispatchOptionsRequest` option, like this:

    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>dispatchOptionsRequest</param-name>
            <param-value>true</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

See <https://developer.mozilla.org/en-US/docs/HTTP/Access_control_CORS> for more information on cross-domain access control.