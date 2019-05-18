---
title: Spring与SpringMVC父子容器
date: 2019-01-22 17:43:14
category: Spring
tag: Spring
---

## 1. 父子容器概念

**Servlet WebApplicationContext:** 主要针对Web层，如控制器(Controller)、视图解析器(View Resolvers)等相关bean。通过SpringMVC提供的DispatcherServlet加载配置，通常配置文件名为spring-servlet.xml。
**Root WebApplicationContext:** 主要针对Service层、Dao层，如业务bean、数据源等。在web应用中，一般通过ContextLoaderListener加载配置，通常配置文件名为applicationContext.xml。

附上web.xml配置案例

``` bash
<?xml version="1.0" encoding="UTF-8"?>  
  
<web-app version="3.0" xmlns="http://java.sun.com/xml/ns/javaee"  
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaeehttp://java.sun.com/xml/ns/javaee/web-app_3_0.xsd">  
    
    <!— 创建Root WebApplicationContext -->
    <context-param>  
        <param-name>contextConfigLocation</param-name>  
        <param-value>/WEB-INF/spring/applicationContext.xml</param-value>  
    </context-param>  
  
    <listener>  
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>  
    </listener>  
    
    <!— 创建Servlet WebApplicationContext -->
    <servlet>  
        <servlet-name>dispatcher</servlet-name>  
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>  
        <init-param>  
            <param-name>contextConfigLocation</param-name>  
            <param-value>/WEB-INF/spring/spring-servlet.xml</param-value>  
        </init-param>  
        <load-on-startup>1</load-on-startup>  
    </servlet>  
    <servlet-mapping>  
        <servlet-name>dispatcher</servlet-name>  
        <url-pattern>/*</url-pattern>  
    </servlet-mapping>  
  
</web-app>
```

1、 contextLoaderListener首先被初始化，根据<context-param>元素中contextConfigLocation参数指定的配置文件路径，来创建 Root WebApplicationContext实例,并调用ServletContext中的setAttribute方法，设置key为‘org.springframework.web.context.WebApplicationContext.ROOT’， value为该WebApplicationContext实例。
2、DispatherServlet在初始化时，会根据<init-param>元素中contextConfigLocation参数指定的配置文件路径，创建Servlet WebApplicationContext,同时调用ServletContext的getAttribute方法来判断RootWebApplicationContext是否存在，如果存在，则设置为自己的parent。这就是父子容器的概念

## 2. 父子容器的作用

1）查找bean时，先从子容器找，如果子容器中找不到且父容器非空，则从父容器中找到bean，类似于JVM中类加载的机制——双亲委派模型
2）SRP职责分离，解耦。Servlet WebApplicationContext主要针对Web层，而web层有多种选择如Spring MVC, Struts，这样即使web层框架改变并不会影响到Root WebApplicationContext中使用的配置文件，同理，dao层改变，也互不影响

## 3. Root WebApplicationConetxt源码主方法

``` bash
public class ContextLoaderListener extends ContextLoader implements ServletContextListener {
   
    @Override
    public void contextInitialized(ServletContextEvent event) {
        initWebApplicationContext(event.getServletContext());
    }
}
```

``` bash
public class ContextLoader {

    public WebApplicationContext initWebApplicationContext(ServletContext servletContext) {
        ...
        if (logger.isInfoEnabled()) {
            logger.info("Root WebApplicationContext: initialization started");
        }
        if (this.context == null) {
            this.context = createWebApplicationContext(servletContext);
        }
        servletContext.setAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE, this.context);
        ...
    }
}
```

## 4. Servlet WebApplicationContext源码主方法

``` bash
public abstract class HttpServletBean extends HttpServlet
        implements EnvironmentCapable, EnvironmentAware {

    @Override
    public final void init() throws ServletException {
        // Let subclasses do whatever initialization they like.
        initServletBean();
    }

    protected void initServletBean() throws ServletException {
    }
}
```

``` bash
public abstract class FrameworkServlet extends HttpServletBean implements ApplicationContextAware {

    @Override
    protected final void initServletBean() throws ServletException {
        if (this.logger.isInfoEnabled()) {
            this.logger.info("FrameworkServlet '" + getServletName() + "': initialization started");
        }
        this.webApplicationContext = initWebApplicationContext();
    }

    protected WebApplicationContext initWebApplicationContext() {
        // 获取Root WebApplicationContext
        WebApplicationContext rootContext = WebApplicationContextUtils.getWebApplicationContext(getServletContext());
        WebApplicationContext wac = null;
        if (wac == null) {
            // No context instance is defined for this servlet -> create a local one
            wac = createWebApplicationContext(rootContext);
        }
    }
}
```

``` bash
public class DispatcherServlet extends FrameworkServlet {

}
```

## 5. JAVA方式配置

``` bash
public interface WebApplicationInitializer {

    void onStartup(ServletContext servletContext) throws ServletException;
}
```

``` bash
public abstract class AbstractContextLoaderInitializer implements WebApplicationInitializer {

    /**
     * 向ServletContext中注册ContextLoaderListener,从而创建Root WebApplicationContext
     */
    protected void registerContextLoaderListener(ServletContext servletContext) {
        WebApplicationContext rootAppContext = createRootApplicationContext();
        if (rootAppContext != null) {
            ContextLoaderListener listener = new ContextLoaderListener(rootAppContext);
            listener.setContextInitializers(getRootApplicationContextInitializers());
            servletContext.addListener(listener);
        }
        else {
            logger.debug("No ContextLoaderListener registered, as " +
                    "createRootApplicationContext() did not return an application context");
        }
    }
}
```

``` bash
public abstract class AbstractDispatcherServletInitializer extends AbstractContextLoaderInitializer {

    /**
    * 向ServletContext中注册DispatcherServlet，从而创建Servlet WebApplicationContext
    */
    protected void registerDispatcherServlet(ServletContext servletContext) {
        String servletName = getServletName();
        WebApplicationContext servletAppContext = createServletApplicationContext();
        DispatcherServlet dispatcherServlet = createDispatcherServlet(servletAppContext);
        ServletRegistration.Dynamic registration = servletContext.addServlet(servletName, dispatcherServlet);
    }

 
    protected abstract String[] getServletMappings();
}
```

``` bash
public abstract class AbstractAnnotationConfigDispatcherServletInitializer
        extends AbstractDispatcherServletInitializer {

  
    protected abstract Class<?>[] getRootConfigClasses();

    protected abstract Class<?>[] getServletConfigClasses();
}
```

``` bash
// 根据Spring以上抽象类的逻辑，实现以下代码
public class MyWebConfigInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    //获得创建Root WebApplicationContext所需的配置类
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class<?[] { RootConfig.class };
    }
    //获得创建Servlet WebApplicationContext所需的配置类
    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class<?[] { ServletConfig.class };
    }
    //获得DispatchServlet拦截的url
    @Override
    protected String[] getServletMappings() {
        return new String[] { "/app/*" };
    }
}
```