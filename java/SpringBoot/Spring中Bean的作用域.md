# Spring中Bean的作用域

[Spring官方文档](<https://docs.spring.io/spring/docs/5.2.7.RELEASE/spring-framework-reference/core.html#beans-factory-scopes>)中称Bean有6种原生作用域分别是：singleton，prototype，request，session，application，websocket。此外用户也可以对作用域进行扩展，称为自定义作用域。request，session，application主要用在mvc中。

以下是Spring的作用域体系：

- Scope(接口)
  - AbstractRequestAttributesScope(抽象类)
    - RequestScope
    - SessionScope
  - ServletContextScope
  - SimpleThreadScope
  - SimpleTransactionScope
  - 自定义scope或者其他spring体系对scope的扩展（如**GenericScope**此类及其子类RefreshScope和ThreadScope并非出自SpringFramework，而是来自SpringCloud对Scope的扩展)

#### prototype

- 对prototype作用域的Bean，spring并不能完整管理其生命周期，也没有办法记录实例的存在，比如Bean的销毁方法不会被回调。
- 如果外层的bean是singleton，依赖注入的bean是prototype，那么每次请求过来这个注入的bean并不会变化，这是因为外层singleton的bean生成后不会改变，所以属性也不会变化，除非手动重新注入。如果外层的bean例如controller也是prototype或者request，那么每次请求过来的Controller都是新的对象，其内部注入的prototype的bean也是新的对象。

#### 自定义Scope

- 实现`org.springframework.beans.factory.config.Scope`

- 注册，两种方式：

  - org.springframework.beans.factory.config.ConfigurableBeanFactory#registerScope(String, Scope)

  - xml配置

    ```xml
    <bean class="org.springframework.beans.factory.config.CustomScopeConfigurer">
    	<property name="scopes">
        	<map>
    			<entry key="thread">
    				<bean class="org.springframework.context.support.SimpleThreadScope"/>
    			</entry>
    		</map>
    	</property>
    </bean>
    <bean id="thing2" class="x.y.Thing2" scope="thread">
        <property name="name" value="Rick"/>
        <aop:scoped-proxy/>
    </bean>
    ```

#### 源码分析

在AbstractBeanFactory#doGetBean(String, Class<T>, Object[], boolean)方法中，通过BeanDefinition判断bean的作用域，对于singleton和prototype的会通过硬编码实现，对其他Scope的bean，会通过具体的Scope实现类来获取。

```java
protected <T> T doGetBean(final String name, 
                          @Nullable final Class<T> requiredType,
                          @Nullable final Object[] args, 
                          boolean typeCheckOnly) throws BeansException {

    final String beanName = transformedBeanName(name);
    Object bean;

    /**
     * 检查单例bean是否已经存在
     * 对于通过SingletonBeanRegistry#registerSingleton(String, Object)手动注入的bean
     * 会存放在变量名为singletonObjects的ConcurrentHashMap中，此处就是处理这种单例对象
     **/ 
    Object sharedInstance = getSingleton(beanName);
    if (sharedInstance != null && args == null) {
        if (logger.isTraceEnabled()) {
            if (isSingletonCurrentlyInCreation(beanName)) {
                logger.trace("Returning eagerly cached instance of singleton bean '" 
                             + beanName +
                             "' that is not fully initialized yet - a consequence" 
                             + " of a circular reference");
            } else {
                logger.trace("Returning cached instance of singleton bean '" 
                             + beanName + "'");
            }
        }
        bean = getObjectForBeanInstance(sharedInstance, name, beanName, null);
    } else {
        // prototype 循环依赖
        if (isPrototypeCurrentlyInCreation(beanName)) {
            throw new BeanCurrentlyInCreationException(beanName);
        }

        // 如果本地不存在这个BeanDefinition就从parent中查找.
        BeanFactory parentBeanFactory = getParentBeanFactory();
        if (parentBeanFactory != null && !containsBeanDefinition(beanName)) {
            // Not found -> check parent.
            String nameToLookup = originalBeanName(name);
            if (parentBeanFactory instanceof AbstractBeanFactory) {
                return ((AbstractBeanFactory) parentBeanFactory).doGetBean(
                    nameToLookup, requiredType, args, typeCheckOnly);
            } else if (args != null) {
                return (T) parentBeanFactory.getBean(nameToLookup, args);
            } else if (requiredType != null) {
                return parentBeanFactory.getBean(nameToLookup, requiredType);
            } else {
                return (T) parentBeanFactory.getBean(nameToLookup);
            }
        }

        if (!typeCheckOnly) {
            markBeanAsCreated(beanName);
        }

        try {
            final RootBeanDefinition mbd = getMergedLocalBeanDefinition(beanName);
            checkMergedBeanDefinition(mbd, beanName, args);

            // Guarantee initialization of beans that the current bean depends on.
            String[] dependsOn = mbd.getDependsOn();
            if (dependsOn != null) {
                for (String dep : dependsOn) {
                    if (isDependent(beanName, dep)) {
                        throw new BeanCreationException(mbd.getResourceDescription(),
                                   beanName,
                                   "Circular depends-on relationship between '" 
                                   + beanName + "' and '" + dep + "'");
                    }
                    registerDependentBean(dep, beanName);
                    try {
                        getBean(dep);
                    } catch (NoSuchBeanDefinitionException ex) {
                        throw new BeanCreationException(mbd.getResourceDescription(), 
                                   beanName,
                                   "'" + beanName + "' depends on missing bean '" 
                                   + dep + "'", ex);
                    }
                }
            }

            // Singleton scope
            if (mbd.isSingleton()) {
                sharedInstance = getSingleton(beanName, () -> {
                    try {
                        return createBean(beanName, mbd, args);
                    } catch (BeansException ex) {
                        destroySingleton(beanName);
                        throw ex;
                    }
                });
                bean = getObjectForBeanInstance(sharedInstance, name, beanName, mbd);
            } 
            // Prototype scope
            else if (mbd.isPrototype()) {
                Object prototypeInstance = null;
                try {
                    beforePrototypeCreation(beanName);
                    prototypeInstance = createBean(beanName, mbd, args);
                }
                finally {
                    afterPrototypeCreation(beanName);
                }
                bean = getObjectForBeanInstance(prototypeInstance, name, 
                                                beanName, mbd);
            }
			// 其他scope通过Scope接口的具体实现类获取bean
            else {
                String scopeName = mbd.getScope();
                final Scope scope = this.scopes.get(scopeName);
                if (scope == null) {
                    throw new IllegalStateException("No Scope registered for scope"
                                                    + " name '" + scopeName + "'");
                }
                try {
                    // Scope的具体实现类获取bean对象
                    Object scopedInstance = scope.get(beanName, () -> {
                        beforePrototypeCreation(beanName);
                        try {
                            return createBean(beanName, mbd, args);
                        } finally {
                            afterPrototypeCreation(beanName);
                        }
                    });
                    bean = getObjectForBeanInstance(scopedInstance, name, 
                                                    beanName, mbd);
                } catch (IllegalStateException ex) {
                    throw new BeanCreationException(beanName,
                              "Scope '" + scopeName + 
                              "' is not active for the current thread; consider " 
                              + "defining a scoped proxy for this bean if you " +
                              "intend to refer to it from a singleton", ex);
                }
            }
        } catch (BeansException ex) {
            cleanupAfterBeanCreationFailure(beanName);
            throw ex;
        }
    }

    // Check if required type matches the type of the actual bean instance.
    if (requiredType != null && !requiredType.isInstance(bean)) {
        try {
            T convertedBean = getTypeConverter().convertIfNecessary(bean, 
                                                                    requiredType);
            if (convertedBean == null) {
                throw new BeanNotOfRequiredTypeException(name, requiredType, 
                                                         bean.getClass());
            }
            return convertedBean;
        } catch (TypeMismatchException ex) {
            if (logger.isTraceEnabled()) {
                logger.trace("Failed to convert bean '" + name + "' to required type '" 
                             + ClassUtils.getQualifiedName(requiredType) + "'", ex);
            }
            throw new BeanNotOfRequiredTypeException(name, requiredType, 
                                                     bean.getClass());
        }
    }
    return (T) bean;
}
```

从上面的类结构图中我们看到request和session作用域的实现类分别是RequestScope和SessionScope，他们的抽象父类`AbstractRequestAttributesScope`中实现了**#get(String, ObjectFactory<?>)**方法，SessionScope重写了这个方法，先去获取锁(因为可以开多个页面操作)，然后再调用父类的这个方法。

```java
public Object get(String name, ObjectFactory<?> objectFactory) {
    // 从ThreadLocal中获取RequestAttributes
    RequestAttributes attributes = RequestContextHolder.currentRequestAttributes();
    // 从RequestAttributes中获取bean实例
    Object scopedObject = attributes.getAttribute(name, getScope());
    if (scopedObject == null) {
        // 通过objectFactory获取实例，再放入RequestAttributes
        scopedObject = objectFactory.getObject();
        attributes.setAttribute(name, scopedObject, getScope());
        // spring允许对放入RequestAttributes的对象再次进行装饰，所以这里要再次获取一下
        Object retrievedObject = attributes.getAttribute(name, getScope());
        if (retrievedObject != null) {
            scopedObject = retrievedObject;
        }
    }
    return scopedObject;
}
```

AbstractRequestAttributesScope先从ThreadLocal中获取RequestAttributes，再从RequestAttributes获取bean实例，如果实例不存在就通过ObjectFactory获取然后放到RequestAttributes中。注意这里spring允许对放入RequestAttributes的对象再次进行装饰，就是说`#getAttribute(name, getScope())`这个方法可以进行扩展，所以需要调用这个方法再次获取一下再返回。

RequestAttributes是Spring对request的抽象，我们进入ServletRequestAttributes可以看到，最终是通过HttpServletRequest和HttpSession的的setAttribute(name, value)实现bean实例的存放。

application作用域和request类似，只是放在了ServletContext中。

