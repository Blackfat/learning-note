### Spring AOP支持的AspectJ切入点指示符

```AspectJExpressionPointcut```

 within：用于匹配指定类型内的方法执行；

```java
#feign.Client类及其子类中的方法
within(feign.Client+)
```

 this：用于匹配当前AOP代理对象类型的执行方法；注意是AOP代理对象的类型匹配，这样就可能包括引入接口也类型匹配；

```
#代理对象为AccountService类型的中的方法
this(com.xyz.service.AccountService)
```

target：用于匹配当前目标对象类型的执行方法；注意是目标对象的类型匹配，这样就不包括引入接口也类型匹配；

```
#目标对象为AccountService类型的中的方法
target(com.xyz.service.AccountService)
```

 args：用于匹配当前执行的方法传入的参数为指定类型的执行方法；

 ```
#匹配只有一个参数，且类型为com.ms.aop.args.demo1.UserModel
args(com.ms.aop.args.demo1.UserModel)
 ```

 @within：用于匹配所以持有指定注解类型内的方法；

```
#service注解类中的方法，不能是接口，必须是实现类
@within(com.alibaba.dubbo.config.annotation.Service)
```

@target：用于匹配当前目标对象类型的执行方法，其中目标对象持有指定的注解；

```
@target(com.ms.aop.jtarget.Annotation1)
```

 @args：用于匹配当前执行的方法传入的参数持有指定注解的执行；

```
@annotation(com.ms.aop.jannotation.demo2.Annotation1)
```

@annotation：用于匹配当前执行方法持有指定注解的方法；

```
#方法注解中含有token的注解
@annotation(com.xxxx.token)
```

#### **this 和 target 的不同点**

this作用于代理对象，target作用于目标对象

#### **@target 和 @within 的不同点**

@target关注的是被调用的对象，@within关注的是调用的方法所在的类