# Mybatis

## Mybatis拦截器

> 以下两篇文章的mybatis拦截器的使用

[在springboot中给mybatis加拦截器](https://blog.csdn.net/qq_43985303/article/details/130378043)

[mybatis拦截器统一处理createBy、createTime、updateBy等字段_mybatis统一处理_写完bug就找女朋友的博客-CSDN博客](https://blog.csdn.net/qq_37844454/article/details/130513925)

> 以下我自己实现的拦截器

```java
package com.ldw.bid.interceptor;
import com.ldw.cloud.common.util.data.StringUtils;
import com.ldw.cloud.common.util.secure.UserInfoUtils;
import org.apache.ibatis.binding.MapperMethod;
import org.apache.ibatis.executor.Executor;
import org.apache.ibatis.mapping.MappedStatement;
import org.apache.ibatis.reflection.DefaultReflectorFactory;
import org.apache.ibatis.reflection.MetaObject;
import org.apache.ibatis.reflection.factory.DefaultObjectFactory;
import org.apache.ibatis.reflection.wrapper.DefaultObjectWrapperFactory;
import org.apache.ibatis.session.ResultHandler;
import org.apache.ibatis.session.RowBounds;
import org.apache.ibatis.plugin.*;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;
import java.util.Properties;

//@Component
@Intercepts({
        @Signature(type = Executor.class, method = "update", args = {MappedStatement.class, Object.class}),
})
public class AutoFillInterceptor implements Interceptor {

    @Override
    public Object intercept(Invocation invocation) throws Throwable {
        if (invocation.getTarget() instanceof Executor) {
            MappedStatement mappedStatement = (MappedStatement) invocation.getArgs()[0];
            String sqlCommandType = mappedStatement.getSqlCommandType().name();

            // 在插入操作时填充createBy和createTime字段
            Object parameter = invocation.getArgs()[1];
            if (parameter != null) {
                MetaObject metaObject = MetaObject.forObject(parameter, new DefaultObjectFactory(), new DefaultObjectWrapperFactory(), new DefaultReflectorFactory());
                if ("INSERT".equals(sqlCommandType)) {
                    String createBy = (String) metaObject.getValue("createBy");
                    if(StringUtils.isBlank(createBy)){
                        createBy = UserInfoUtils.getUserId();
                        metaObject.setValue("createBy", createBy);
                        metaObject.setValue("createTime", LocalDateTime.now());
                    }
                } else if ("UPDATE".equals(sqlCommandType)) {
                    if (parameter instanceof MapperMethod.ParamMap) {
                        MapperMethod.ParamMap<?> p = (MapperMethod.ParamMap<?>) parameter;
                        if (p.containsKey("et")) {
                            parameter = p.get("et");
                        } else {
                            parameter = p.get("param1");
                        }
                        if (parameter == null) {
                            return invocation.proceed();
                        }
                    }
                    // 在更新操作时填充其他字段或采取其他操作
                    String updateBy = (String) metaObject.getValue("updateBy");
                    if(StringUtils.isBlank(updateBy)){
                        updateBy = UserInfoUtils.getUserId();
                        metaObject.setValue("updateBy", updateBy);
                        metaObject.setValue("updateTime", LocalDateTime.now());
                    }
                }
            }
        }

        return invocation.proceed();
    }

    @Override
    public Object plugin(Object target) {
        return Plugin.wrap(target, this);
    }

    @Override
    public void setProperties(Properties properties) {
        System.out.println("properties = " + properties);
        // 可以在这里配置一些属性
    }
}
```

> 下面将对拦截器进行部分源码分析，标粗为具体分析操作步骤

1.拦截器无外乎就是在执行某方法之前加入我们自己的逻辑，那么就涉及到什么？  没错--><u>**代理**</u>。也就是说具体能拦截哪些类我们就可以通过源码去翻它代理了哪些类就行了。下面我们来看看源码。

首先我们就以我写的拦截器示例为例，我们怎么下手找到它的代理逻辑以及代理的类呢？既然我们要在执行原方法之前加入我们自己的逻辑处理，那么好了我们自己的逻辑代码必定会在原方法之前执行。而我们自己的逻辑在哪里？答：`Interceptor` 类的public Object intercept(Invocation invocation) throws Throwable 方法里。也就是说interceptor在invoke之前执行，那么我们可以**直接定位interceptor方法(拦截的逻辑)在哪执行的**

```java
public class Plugin implements InvocationHandler
  //...
  @Override
  public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    try {
      Set<Method> methods = signatureMap.get(method.getDeclaringClass());
      if (methods != null && methods.contains(method)) {
        return interceptor.intercept(new Invocation(target, method, args));
      }
      return method.invoke(target, args);
    } catch (Exception e) {
      throw ExceptionUtil.unwrapThrowable(e);
    }
  }
  //...
}
```

我们可以看到Plugin是个`InvocationHandler`很显然这是个jdk基于接口的动态代理，那么我们下面就有下一个思路了，就是干嘛？找这个**Plugin在哪里用到**的，即代理类是在哪创建的，同时我们知道代理是要has-a一个被代理对象的，那我们就可以通过创建代理类的地方找到它（Plugin）代理了什么对象？

不过在此之前我们可以看到执行完interceptor.intercept()就返回了，也就是说并不像我们之前说的一样interceptor在invoke之前执行，我们看到代理的参数被封装进了Invocation中我可以猜到，invoke在interceptor里面执行的，通过我的示例中也可以看到有invocation.proceed()方法，而它的源码就是执行 method.invoke(target, args)

并且从这段代码我们可以看出它的**<u>核心逻辑</u>**就是：

​	根据你拦截器中定义的@Signature找到你要代理的类和方法，如果当前被代理的类没有此方法就正常执行原有方法，如果当前被代理的类有此方法则执行拦截逻辑，并将被代理类的实例、方法以及参数封装到Invocation类中以便你可以在拦截逻辑中控制原有方法的执行时机（填充数据在invoke之前，记录日志则有可能在invoke之前或之后[例如记录sql的注释start--end]）。也就是说它会生成所有将要被代理类的代理对象，而是否执行代理逻辑就看你的拦截器里面@Signature指定的是哪个方法。

那么我们接下来看Plugin在哪里用到？

```java
public class Plugin implements InvocationHandler{
  //...
  public static Object wrap(Object target, Interceptor interceptor) {
    Map<Class<?>, Set<Method>> signatureMap = getSignatureMap(interceptor);
    Class<?> type = target.getClass();
    Class<?>[] interfaces = getAllInterfaces(type, signatureMap);
    if (interfaces.length > 0) {
      return Proxy.newProxyInstance(
        type.getClassLoader(),
        interfaces,
        new Plugin(target, interceptor, signatureMap));
    }
    return target;
  }
  //...
}
```

我们可以通过Proxy.newProxyInstance看到这是jdk代理实例的创建，在定位wrap是在哪里用的往上查找就可以慢慢找到具体代理的类。

```java
//...
public class AutoFillInterceptor implements Interceptor {
  //...
  @Override
  public Object plugin(Object target) {
    //1
    return Plugin.wrap(target, this);
  }
  //...
}

/**
 * @author Clinton Begin
 */
public class InterceptorChain {
  //...
  public Object pluginAll(Object target) {
    for (Interceptor interceptor : interceptors) {
      //2
      target = interceptor.plugin(target);
    }
    return target;
  }
  //...
}


/**
 * @author Clinton Begin
 */
public class Configuration {
  //...
  public ParameterHandler newParameterHandler(MappedStatement mappedStatement, Object parameterObject, BoundSql boundSql) {
    ParameterHandler parameterHandler = mappedStatement.getLang().createParameterHandler(mappedStatement, parameterObject, boundSql);
    parameterHandler = (ParameterHandler) interceptorChain.pluginAll(parameterHandler);
    return parameterHandler;
  }

  public ResultSetHandler newResultSetHandler(Executor executor, MappedStatement mappedStatement, RowBounds rowBounds, ParameterHandler parameterHandler,
      ResultHandler resultHandler, BoundSql boundSql) {
    ResultSetHandler resultSetHandler = new DefaultResultSetHandler(executor, mappedStatement, parameterHandler, resultHandler, boundSql, rowBounds);
    resultSetHandler = (ResultSetHandler) interceptorChain.pluginAll(resultSetHandler);
    return resultSetHandler;
  }

  public StatementHandler newStatementHandler(Executor executor, MappedStatement mappedStatement, Object parameterObject, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) {
    StatementHandler statementHandler = new RoutingStatementHandler(executor, mappedStatement, parameterObject, rowBounds, resultHandler, boundSql);
    statementHandler = (StatementHandler) interceptorChain.pluginAll(statementHandler);
    return statementHandler;
  }
  
  //...
  public Executor newExecutor(Transaction transaction, ExecutorType executorType) {
    executorType = executorType == null ? defaultExecutorType : executorType;
    executorType = executorType == null ? ExecutorType.SIMPLE : executorType;
    Executor executor;
    if (ExecutorType.BATCH == executorType) {
      executor = new BatchExecutor(this, transaction);
    } else if (ExecutorType.REUSE == executorType) {
      executor = new ReuseExecutor(this, transaction);
    } else {
      executor = new SimpleExecutor(this, transaction);
    }
    if (cacheEnabled) {
      executor = new CachingExecutor(executor);
    }
    executor = (Executor) interceptorChain.pluginAll(executor);
    return executor;
  }
  //...
}
```

通过上面代码我们可以看到**<u>Mybatis拦截器实际拦截了四种类型</u>**：ParameterHandler、ResultSetHandler、StatementHandler、Executor。

这与前面示例文章中提到的四种：ParameterHandler、ResultHandler、StatementHandler、Executor 唯一不同的就是ResultHandler和ResultSetHandler，这可能是版本原因，我就没有再细致追究了。

