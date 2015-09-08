# gradle-spring-4-mvc-boilerplate
As the package specifies, DelegatingFilterProxy is conceived to be used with and only with Spring Web MVC (aka controllers, annotated or not). It can't be used with plain servlet-jsps, as it seems you are trying to do. So to make it work you need:

- 1 application context loaded through listener pretty much like you've done(if you call the file applicationContext.xml and put it in /WEB-INF, you don't even need to specify its location with a context param):
Code:
<listener>
<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
- 1 Spring web mvc's DispatcherServlet defined in web xml, loaded at startup, and mapped to take care of all urls for your web application. This servlet will define its own servlet context (so you will need another xml file):
Code:
  <servlet>
	<servlet-name>spring</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	<init-param>
	  <param-name>contextConfigLocation</param-name>
	  <param-value>WEB-INF//yourapp-servlet.xml</param-value>
	</init-param>
	<load-on-startup>1</load-on-startup>
  </servlet>
- The dispatcher-servlet context will be child of the application context and thus it will be able to access all its beans. In the dispatcher-servlet context you will define everything related to Spring web MVC (<mvc:annotation-driven/>, interceptors, view resolvers, exception resolvers, context:component-scan for controllers package, aop that you want applied to controllers, etc.);

- In the main application context you will define all that's non-mvc related (for example your persistence configuration, scanning for services and dao packages etc.).

Once you've properly configured Spring and Spring web MVC following my instructions, you must learn to use Spring web MVC properly (annotations, form tags, url mappings etc.) and THEN you can use DelegatingFilterProxy properly...
