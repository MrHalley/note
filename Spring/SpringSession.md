# SpringSession



## 功能

用于解决Session分布式下共享的问题

1.同一服务不同实例间session不同步

2.不同服务间session不共享



## 源码分析

> Maven依赖

```
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.6.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.session</groupId>
    <artifactId>spring-session-data-redis</artifactId>
</dependency>
```

1、首先定位到`SpringHttpSessionConfiguration`中springSessionRepositoryFilter中可以看见创建了一个Filter并注册为Bean实例。以下1.xx是Filter的相关何时配置怎么配置的相关源码解析。可以直接跳过，查看SpringSession核心原理方法

> SpringHttpSessionConfiguration

```java
@Bean
public <S extends Session> SessionRepositoryFilter<? extends Session> springSessionRepositoryFilter(
  SessionRepository<S> sessionRepository) {
  SessionRepositoryFilter<S> sessionRepositoryFilter = new SessionRepositoryFilter<>(sessionRepository);
  sessionRepositoryFilter.setHttpSessionIdResolver(this.httpSessionIdResolver);
  return sessionRepositoryFilter;
}
```



1.1、定位这个Filter可以找到，在`SessionRepositoryFilterConfiguration`中添加`FilterRegistrationBean`的Bean来配置springSessionRepositoryFilter过滤器，并且没有调用registration.addUrlPatterns（配置过滤器Filter的拦截路径），可以推使用的是默认拦截路径："/*"

> SessionRepositoryFilterConfiguration

```java
@Bean
FilterRegistrationBean<SessionRepositoryFilter<?>> sessionRepositoryFilterRegistration(
  SessionProperties sessionProperties, SessionRepositoryFilter<?> filter) {
  FilterRegistrationBean<SessionRepositoryFilter<?>> registration = new FilterRegistrationBean<>(filter);
  registration.setDispatcherTypes(getDispatcherTypes(sessionProperties));
  registration.setOrder(sessionProperties.getServlet().getFilterOrder());
  return registration;
}
```

1.2、定位到`FilterRegistrationBean`的父类`AbstractFilterRegistrationBean`，可以看到默认采用/*拦截路径

> AbstractFilterRegistrationBean

```java
public abstract class AbstractFilterRegistrationBean<T extends Filter> extends DynamicRegistrationBean<Dynamic> {
  private static final String[] DEFAULT_URL_MAPPINGS = { "/*" };

  private Set<String> urlPatterns = new LinkedHashSet<>();
  @Override
  protected void configure(FilterRegistration.Dynamic registration) {
    ....
    //urlPatterns为空，使用默认的DEFAULT_URL_MAPPINGS  "/*"
    if (servletNames.isEmpty() && this.urlPatterns.isEmpty()) {
      registration.addMappingForUrlPatterns(dispatcherTypes, this.matchAfter, DEFAULT_URL_MAPPINGS);
    }
    else {
      if (!servletNames.isEmpty()) {
        registration.addMappingForServletNames(dispatcherTypes, this.matchAfter,
                                               StringUtils.toStringArray(servletNames));
      }
      if (!this.urlPatterns.isEmpty()) {
        registration.addMappingForUrlPatterns(dispatcherTypes, this.matchAfter,
                                              StringUtils.toStringArray(this.urlPatterns));
      }
    }
  }
}
```

1.3、configure定位到`DynamicRegistrationBean` register(xx)中执行，再定位到找到`RegistrationBean` ( 继承自 `ServletContextInitializer` :  Spring Boot 中的一个接口，用于在应用程序启动时初始化 Servlet 容器的配置和组件) onStartup(xx)中执行。也就是说程序启动初始化Servlet时就配置好了Filter的默认拦截路径

> DynamicRegistrationBean

```java
public abstract class DynamicRegistrationBean<D extends Registration.Dynamic> extends RegistrationBean {
  @Override
  protected final void register(String description, ServletContext servletContext) {
    D registration = addRegistration(description, servletContext);
    if (registration == null) {
      logger.info(StringUtils.capitalize(description) + " was not registered (possibly already registered?)");
      return;
    }
    configure(registration);
  }

  protected abstract D addRegistration(String description, ServletContext servletContext);

  protected void configure(D registration) {
    registration.setAsyncSupported(this.asyncSupported);
    if (!this.initParameters.isEmpty()) {
      registration.setInitParameters(this.initParameters);
    }
  }
}
```

> RegistrationBean 

```java
public abstract class RegistrationBean implements ServletContextInitializer, Ordered {
  @Override
  public final void onStartup(ServletContext servletContext) throws ServletException {
    String description = getDescription();
    if (!isEnabled()) {
      logger.info(StringUtils.capitalize(description) + " was not registered (disabled)");
      return;
    }
    register(description, servletContext);
  }
}
```
> ServletContextInitializer  

```java
@FunctionalInterface
public interface ServletContextInitializer {
	void onStartup(ServletContext servletContext) throws ServletException;
}
```



2、**注：核心方法**： `SessionRepositoryFilter`中的doFilterInternal(xx)，可以看到使用了包装者模式，对`HttpServletRequest`进行了包装。

```java
@Order(SessionRepositoryFilter.DEFAULT_ORDER)
public class SessionRepositoryFilter<S extends Session> extends OncePerRequestFilter {
  @Override
  protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
    throws ServletException, IOException {
    request.setAttribute(SESSION_REPOSITORY_ATTR, this.sessionRepository);
	//包装者模式
    SessionRepositoryRequestWrapper wrappedRequest = new SessionRepositoryRequestWrapper(request, response);
    SessionRepositoryResponseWrapper wrappedResponse = new SessionRepositoryResponseWrapper(wrappedRequest,
                                                                                            response);

    try {
      filterChain.doFilter(wrappedRequest, wrappedResponse);
    }
    finally {
      wrappedRequest.commitSession();
    }
  }
}
```

3、//TODO解析如何包装的。