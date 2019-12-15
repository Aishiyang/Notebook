# Spring Source Analysis



## SpringMVC

在web.xml中我们会这样配置springmvc.当有url请求过来，可以看到他会去调用DispatcherServlet类

```Java
<servlet>
	<servlet-name>appServlet</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:application.xml</param-value>
	</init-param>
	<load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>appServlet</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

DispatcherServlet实现了FrameworkServlet类，FrameworkServlet类继承HttpServletBean类实现ApplicationContextAware接口，而只要实现ApplicationContextAware接口，在spring中只要实现了ApplicationContextAware接口，就会自动调用方法setContextInitializers(),在FrameworkServlet类中实现了ApplicationContextAware接口，所以会自动调用FrameworkServlet类中的setContextInitializers()方法

```Java
package org.springframework.web.servlet;

@SuppressWarnings("serial")
public class DispatcherServlet extends FrameworkServlet {
    
}
```

```Java
package org.springframework.web.servlet;

@SuppressWarnings("serial")
public abstract class FrameworkServlet extends HttpServletBean implements ApplicationContextAware {
    
    ...
        
    public void setContextInitializers(@Nullable ApplicationContextInitializer<?>... initializers) {
		if (initializers != null) {
			for (ApplicationContextInitializer<?> initializer : initializers) {		this.contextInitializers.add((ApplicationContextInitializer<ConfigurableApplicationContext>) initializer);
			}
		}
	}    
        
    ...
    
}
```