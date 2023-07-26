#### LITE模式和FULL模式

LITE模式

1. 类上标注有`@Component`注解
2. 类上标注有`@ComponentScan`注解
3. 类上标注有`@Import`注解
4. 类上标注有`@ImportResource`注解
5. 若类上没有任何注解，但类内存在@Bean方法
6. 标注有`@Configuration(proxyBeanMethods = false)`

优缺点：

- 该模式下，配置类本身不会被CGLIB增强，放进IoC容器内的就是本身
- 该模式下，对于内部类是没有限制的：可以是Full模式或者Lite模式
- 该模式下，配置类内部不能通过方法调用来处理依赖，否则每次生成的都是一个新实例而并非IoC容器内的单例
- 该模式下，配置类就是一普通类嘛，所以@Bean方法可以使用`private/final`等进行修饰

FULL模式

标注有`@Configuration`或者`@Configuration(proxyBeanMethods = true)`的类被称为Full模式的配置类

优缺点：

- 该模式下，配置类会被CGLIB增强(生成代理对象)，放进IoC容器内的是代理
- 该模式下，对于内部类是没有限制的：可以是Full模式或者Lite模式
- 该模式下，配置类内部可以通过方法调用来处理依赖，并且能够保证是同一个实例，都指向IoC内的那个单例
- 该模式下，@Bean方法不能被`private/final`等进行修饰（因为方法需要被复写，所以不能私有和final。defualt/protected/public都可以）



#### BeanDefinition

- `AnnotatedGenericBeanDefinition`：在开始传入的配置类，以及通过@Import注解引入的Bean
- `ScannedGenericBeanDefinition`：通过@Component扫描包引入的Bean
- `ConfigurationClassBeanDefinition`：通过@Bean注解引入的Bean
- `RootBeanDefinition`：Spring内部使用，如生产Bean时将其他`BeanDefinition`转成`RootBeanDefinition`



#### BeanFactory

DefaultListableBeanFactory继承的接口：

- `SingletonBeanRegistry`: 定义了对单例缓存池相关的操作，如将bean注册到单例缓存池中
- `ConfigurableBeanFactory`: 可配置的`BeanFactory`，定义了各种各样的配置能力，如bean的作用域，bean的classLoader,添加bean的后置处理器，设置bean的创建状态，销毁bean等等
- `AutowireCapableBeanFactory`: 能进行自动装配的`BeanFactory`,定义了自动装配的类型(byName/byType)，`createBean`, `autowireBean`, 自动装配属性, populateBean, initializeBean, 对于与bean生命周期相关的方法都将在这里体现
- `ListableBeanFactory`: 对`BeanFactory`的增强，定义了一系列根据beanType获取bean或者beanName的方法
- `ConfigurableListableBeanFactory`: 对`ConfigurableBeanFactory`的增强，定义了忽略bean的类型、缓存bean定义、预实例化单例bean等方法
- `BeanDefinitionRegistry`: bean定义注册器，定义了与bean定义相关的方法

DefaultListableBeanFactory接口的实现：

- `DefaultSingletonBeanRegistry`: 单例bean注册器，定义了三级缓存，其实就是三个Map属性
- `FactoryBeanRegistrySupport`: 提供对`FactoryBean`的支持
- `AbstractBeanFactory`: 实现了一系列操作IOC容器的功能，但最终的createBean依旧交由子类`AbstractAutowireCapableBeanFactory`完成
- `AbstractAutowireCapableBeanFactory`: 实现了创建bean的功能，所有与创建bean的相关的功能都在这里
- `DefaultListableBeanFactory`: 在以上父类的功能基础上实现了`ConfigurableBeanFactory`和`BeanDefinitionRegistry`接口，定义了一些存放Bean定义相关信息的Map



#### BeanPostProcessor

ApplicationContextAwareProcessor

作用：

完成**Aware的Bean的注入

注入时机：

initializeBean方法

AutowiredAnnotationBeanPostProcessor

作用：

完成@Autowired、@Value、@Inject、@Resource注解的注入，其中**@Resource** 的默认实现是 **autowired by field name**，当其失败时才会退化为 **autowired by type**，而 @Autowired 和 @Inject 的默认实现都是 **autowired by type** 。

注入时机：

- 实例化 Bean 的时候在 createBeanInstance 方法中会调用 AutowiredAnnotationBeanPostProcessor 中的方法来获取需要自动注入的构造方法
- 在调用所有 MergedBeanDefinitionPostProcessor 时会调用到 AutowiredAnnotationBeanPostProcessor 的方法来完成对所有需要自动注入的属性和方法的解析和缓存
- 在 populatedBean 方法中调用到 AutowiredAnnotationBeanPostProcessor 中的方法来完成需要自动注入属性的注入工作

 使用：

自定义注解，完成Bean的注入，实现InstantiationAwareBeanPostProcessorAdapter，重写`postProcessPropertyValues`方法,自定义属性注入的方式。

AbstractAutoProxyCreator(AnnotationAwareAspectJAutoProxyCreator)

作用：完成Spring AOP的功能,Spring AOP 是基于**代理**实现的，因此 this 即为 AOP 代理对象，target 即为被代理的原始对象

注入时机：

initializeBean方法，进行代理之前原始 Bean 实例其实已经被创建出来了，只不过在对实例进行初始化的过程中又将其替换为了代理对象



