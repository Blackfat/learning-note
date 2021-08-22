# SpringBoot中Bean加载过程

本文基于Springboot2.3.1.RELEASE版本，且主要说明springboot应用启动过程中的bean加载过程，对于通过自定义扩展方式加载bean的过程不会涉及，对其他无关的逻辑也不会过多涉及。

#### 注册Source

##### 构建Source

首先我们看一下一个简单的Springboot应用主程序入口，示例代码如下：

```java
@SpringBootApplication
public class SourceDemoApplication {
	public static void main(String[] args) {
		SpringApplication.run(SourceDemoApplication.class, args);
	}
}
```

大多数时候我们的代码都是这样活着类似这样，这段代码也是springboot程序的启动入口，所以bean的加载过程也要从这里开始。首先我们看下这个SpringApplication#run(Class<?>, String...)方法做了些什么。

```java
public static ConfigurableApplicationContext run(Class<?> primarySource, String... args) {
  return run(new Class<?>[] { primarySource }, args);
}
public static ConfigurableApplicationContext run(Class<?>[] primarySources, String[] args) {
  return new SpringApplication(primarySources).run(args);
}
```

这里看起来很简单，调用SpringApplication构造函数然后在调用#run(String...)方法，在这个构造函数中传入了我们的启动类ourceDemoApplication.class，仅需跟踪发现构造了一个变量名为primarySources的LinkedHashSet，并将我们的这个class放入其中。

##### 构建ApplicationContext

接着我们看#run(String...)方法，这里是springboot启动的主要逻辑，我们只看bean启动相关的代码，其他的代码不会过多涉及。Bean的加载和容器息息相关，所以我们直接从bean容器ApplicaitonContext的创建开始，估计大家对这里的代码都已经很熟悉了，这里我们需要说一下因为我们并没有制定上下文的类型，所以这里通过构造函数创建的是一个默认的AnnotationConfigApplicationContext。

```java
protected ConfigurableApplicationContext createApplicationContext() {
  Class<?> contextClass = this.applicationContextClass;
  if (contextClass == null) {
    try {
      switch (this.webApplicationType) {
        case SERVLET:
          contextClass = Class.forName(DEFAULT_SERVLET_WEB_CONTEXT_CLASS);
          break;
        case REACTIVE:
          contextClass = Class.forName(DEFAULT_REACTIVE_WEB_CONTEXT_CLASS);
          break;
        default:
          contextClass = Class.forName(DEFAULT_CONTEXT_CLASS);
      }
    }
    catch (ClassNotFoundException ex) {
      throw new IllegalStateException(
        "Unable create a default ApplicationContext, please specify an ApplicationContextClass", ex);
    }
  }
  return (ConfigurableApplicationContext) BeanUtils.instantiateClass(contextClass);
}
```

我们也看下AnnotationConfigApplicationContext的默认构造函数，这里构建了两个东西AnnotatedBeanDefinitionReader和ClassPathBeanDefinitionScanner，这两个东西会在后面扫描包路径和读取注解元数据的时候用到。

```java
public AnnotationConfigApplicationContext() {
  this.reader = new AnnotatedBeanDefinitionReader(this);
  this.scanner = new ClassPathBeanDefinitionScanner(this);
}
```

AnnotatedBeanDefinitionReader在构造函数中向BeanDefinitionRegistry注册了一些processor，这些processor包括对Jsr250注解、jpa注解、Autowired注解等的处理，其中一个比较重要的是**ConfigurationClassPostProcessor**，这个类我们会在后面的讲解中重点说明，这里只是简单提一下。

```java
public AnnotatedBeanDefinitionReader(BeanDefinitionRegistry registry, Environment environment) {
  Assert.notNull(registry, "BeanDefinitionRegistry must not be null");
  Assert.notNull(environment, "Environment must not be null");
  this.registry = registry;
  this.conditionEvaluator = new ConditionEvaluator(registry, environment, null);
  AnnotationConfigUtils.registerAnnotationConfigProcessors(this.registry);
}

public static Set<BeanDefinitionHolder> registerAnnotationConfigProcessors(
			BeanDefinitionRegistry registry, @Nullable Object source) {

  DefaultListableBeanFactory beanFactory = unwrapDefaultListableBeanFactory(registry);
  if (beanFactory != null) {
    if (!(beanFactory.getDependencyComparator() instanceof AnnotationAwareOrderComparator)) {
      beanFactory.setDependencyComparator(AnnotationAwareOrderComparator.INSTANCE);
    }
    if (!(beanFactory.getAutowireCandidateResolver() instanceof ContextAnnotationAutowireCandidateResolver)) {
      beanFactory.setAutowireCandidateResolver(new ContextAnnotationAutowireCandidateResolver());
    }
  }

  Set<BeanDefinitionHolder> beanDefs = new LinkedHashSet<>(8);

  if (!registry.containsBeanDefinition(CONFIGURATION_ANNOTATION_PROCESSOR_BEAN_NAME)) {
    RootBeanDefinition def = new RootBeanDefinition(ConfigurationClassPostProcessor.class);
    def.setSource(source);
    beanDefs.add(registerPostProcessor(registry, def, CONFIGURATION_ANNOTATION_PROCESSOR_BEAN_NAME));
  }

  if (!registry.containsBeanDefinition(AUTOWIRED_ANNOTATION_PROCESSOR_BEAN_NAME)) {
    RootBeanDefinition def = new RootBeanDefinition(AutowiredAnnotationBeanPostProcessor.class);
    def.setSource(source);
    beanDefs.add(registerPostProcessor(registry, def, AUTOWIRED_ANNOTATION_PROCESSOR_BEAN_NAME));
  }

  if (jsr250Present && !registry.containsBeanDefinition(COMMON_ANNOTATION_PROCESSOR_BEAN_NAME)) {
    RootBeanDefinition def = new RootBeanDefinition(CommonAnnotationBeanPostProcessor.class);
    def.setSource(source);
    beanDefs.add(registerPostProcessor(registry, def, COMMON_ANNOTATION_PROCESSOR_BEAN_NAME));
  }

  if (jpaPresent && !registry.containsBeanDefinition(PERSISTENCE_ANNOTATION_PROCESSOR_BEAN_NAME)) {
    RootBeanDefinition def = new RootBeanDefinition();
    try {
      def.setBeanClass(ClassUtils.forName(PERSISTENCE_ANNOTATION_PROCESSOR_CLASS_NAME,
                                          AnnotationConfigUtils.class.getClassLoader()));
    } catch (ClassNotFoundException ex) {
      throw new IllegalStateException(
        "Cannot load optional framework class: " + PERSISTENCE_ANNOTATION_PROCESSOR_CLASS_NAME, ex);
    }
    def.setSource(source);
    beanDefs.add(registerPostProcessor(registry, def, PERSISTENCE_ANNOTATION_PROCESSOR_BEAN_NAME));
  }

  if (!registry.containsBeanDefinition(EVENT_LISTENER_PROCESSOR_BEAN_NAME)) {
    RootBeanDefinition def = new RootBeanDefinition(EventListenerMethodProcessor.class);
    def.setSource(source);
    beanDefs.add(registerPostProcessor(registry, def, EVENT_LISTENER_PROCESSOR_BEAN_NAME));
  }

  if (!registry.containsBeanDefinition(EVENT_LISTENER_FACTORY_BEAN_NAME)) {
    RootBeanDefinition def = new RootBeanDefinition(DefaultEventListenerFactory.class);
    def.setSource(source);
    beanDefs.add(registerPostProcessor(registry, def, EVENT_LISTENER_FACTORY_BEAN_NAME));
  }

  return beanDefs;
}
```

##### 注册Source

我们回到#run(String...)方法继续看，在创建了应用上下文后进入SpringApplication.prepareContext(ConfigurableApplicationContext, ConfigurableEnvironment, SpringApplicationRunListeners, ApplicationArguments, Banner)逻辑。

```java
private void prepareContext(ConfigurableApplicationContext context, 
                            ConfigurableEnvironment environment,
														SpringApplicationRunListeners listeners, ApplicationArguments 
                            applicationArguments, Banner printedBanner) {
  context.setEnvironment(environment);
  postProcessApplicationContext(context);
  applyInitializers(context);
  listeners.contextPrepared(context);
  if (this.logStartupInfo) {
    logStartupInfo(context.getParent() == null);
    logStartupProfileInfo(context);
  }
  // Add boot specific singleton beans
  ConfigurableListableBeanFactory beanFactory = context.getBeanFactory();
  beanFactory.registerSingleton("springApplicationArguments", applicationArguments);
  if (printedBanner != null) {
    beanFactory.registerSingleton("springBootBanner", printedBanner);
  }
  if (beanFactory instanceof DefaultListableBeanFactory) {
    ((DefaultListableBeanFactory) beanFactory)
    .setAllowBeanDefinitionOverriding(this.allowBeanDefinitionOverriding);
  }
  if (this.lazyInitialization) {
    context.addBeanFactoryPostProcessor(new LazyInitializationBeanFactoryPostProcessor());
  }
  // 将我们传入的Class封装成BeanDefinitionLoader并调用#load()方法
  Set<Object> sources = getAllSources();
  Assert.notEmpty(sources, "Sources must not be empty");
  load(context, sources.toArray(new Object[0]));
  listeners.contextLoaded(context);
}
```

这里我们忽略其他步骤看方法后面的逻辑中一段调用SpringApplication.load(ApplicationContext, Object[])，这个地方很重要。上文提到我们传入的类会放在一个名称为primarySources的set中，这里首先取出这个set中所有的primarySources和所有其他方式传递进来的Sources，然后将这些Sources封装到一个新创建的BeanDefinitionLoader中，在调用BeanDefinitionLoader#load()方法对这些sources一个一个进行处理(BeanDefinitionLoader是springboot中加入的类,SpringFramework中没有)

```java
protected void load(ApplicationContext context, Object[] sources) {
  if (logger.isDebugEnabled()) {
    logger.debug("Loading source " + StringUtils.arrayToCommaDelimitedString(sources));
  }
  BeanDefinitionLoader loader = createBeanDefinitionLoader(getBeanDefinitionRegistry(context), sources);
  if (this.beanNameGenerator != null) {
    loader.setBeanNameGenerator(this.beanNameGenerator);
  }
  if (this.resourceLoader != null) {
    loader.setResourceLoader(this.resourceLoader);
  }
  if (this.environment != null) {
    loader.setEnvironment(this.environment);
  }
  loader.load();
}
protected BeanDefinitionLoader createBeanDefinitionLoader(BeanDefinitionRegistry registry, Object[] sources) {
  return new BeanDefinitionLoader(registry, sources);
}
```

接下来我们看看SpringBoot中对这些Sources如何进行load处理。

```java
org.springframework.boot.BeanDefinitionLoader
//入口是一个默认的包内访问的方法
int load() {
  int count = 0;
  for (Object source : this.sources) {
    count += load(source);
  }
  return count;
}
private int load(Object source) {
  Assert.notNull(source, "Source must not be null");
  if (source instanceof Class<?>) {
    return load((Class<?>) source);
  }
  if (source instanceof Resource) {
    return load((Resource) source);
  }
  if (source instanceof Package) {
    return load((Package) source);
  }
  if (source instanceof CharSequence) {
    return load((CharSequence) source);
  }
  throw new IllegalArgumentException("Invalid source type " + source.getClass());
}
```

从上面的BeanDefinitionLoader类的源码中可以看到，这些sources可以有四种类型，分别是Class、Resource、Package、CharSequence，而我们之前在代码中的#run(Class<?>, String...)方法里传入的source是Class类型(目前springboot中对外只提供两种方式source的注册，一种是Class一种是String)。

Class类型Source的处理比较简单(忽略groovy)，就是直接将这个Class注册成为BeanDefinition。

```java
org.springframework.boot.BeanDefinitionLoader
private int load(Class<?> source) {
  if (isGroovyPresent() && GroovyBeanDefinitionSource.class.isAssignableFrom(source)) {
    GroovyBeanDefinitionSource loader = BeanUtils.instantiateClass(source, GroovyBeanDefinitionSource.class);
    load(loader);
  }
  if (isEligible(source)) {
    this.annotatedReader.register(source);
    return 1;
  }
  return 0;
}
```

Resource类型处理可以看下Environment的加载过程。

CharSequence类型的处理主要是处理通配符，然后再依次尝试用其他三种方式加载资源，如果其中一种加载到资源就立马返回。

到此我们已经明白，我们在#run(Class<?>, String...)方法中传入的Class被注册成了一个BeanDefinition，习惯上我们大家都是把@SpringBootApplication、@ComponentScan等注解放在这个类上，后续逻辑会继续解析这个BeanDefinition从而完成这些注解的处理，对这个BeanDefinition上注解的处理也是SpringBoot中真正的类加载过程。

#### 处理Source类

上一部分注册了一个类型为Class的Source的BeanDefinition，这一部分就是对这个BeanDefinition的解析。

##### 解析`@Configuration`注解

回到SpringApplication#run(String...)方法中，在`prepareContext(context, environment, listeners, applicationArguments, printedBanner)`完成Source的注册后调用#refreshContext(context)方法刷新ApplicationContext，这个刷新的主要逻辑就是调用ApplicationContext#refresh()方法，相信大家对#refresh()方法已经非常熟悉，这里不再展示。我们看#refresh()其中的一个步骤`invokeBeanFactoryPostProcessors(beanFactory)`，这里是对BeanFactoryPostProcessor的回掉处理，在上文中我们提到在通过构造函数构造AnnotatedBeanDefinitionReader的过程中会注册一个**ConfigurationClassPostProcessor**，这个BeanFactoryPostProcessor也会在这里被调用，这个Processor主要处理`@Configuration`注解和元标注了`@Configuration`注解的注解，而`@SpringBootApplication`注解间接元标注了`@Configuration`，所以到此大家应该明白我们在上一部分提到的注册到Spring中的Source类在这里会被处理。下面我们看下ConfigurationClassPostProcessor里的逻辑，代码如下：

```java
public void processConfigBeanDefinitions(BeanDefinitionRegistry registry) {
  List<BeanDefinitionHolder> configCandidates = new ArrayList<>();
  String[] candidateNames = registry.getBeanDefinitionNames();

  for (String beanName : candidateNames) {
    BeanDefinition beanDef = registry.getBeanDefinition(beanName);
    if (beanDef.getAttribute(ConfigurationClassUtils.CONFIGURATION_CLASS_ATTRIBUTE) != null) {
      if (logger.isDebugEnabled()) {
        logger.debug("Bean definition has already been processed as a configuration class: " + beanDef);
      }
    } else if (ConfigurationClassUtils.checkConfigurationClassCandidate(beanDef, this.metadataReaderFactory)) {
      configCandidates.add(new BeanDefinitionHolder(beanDef, beanName));
    }
  }
  // 如果没有@Configuration注解的类则直接返回
  if (configCandidates.isEmpty()) {
    return;
  }
  // 对@Configuration注解的类进行排序基于@Order注解的顺序
  configCandidates.sort((bd1, bd2) -> {
    int i1 = ConfigurationClassUtils.getOrder(bd1.getBeanDefinition());
    int i2 = ConfigurationClassUtils.getOrder(bd2.getBeanDefinition());
    return Integer.compare(i1, i2);
  });

  ......省略代码

  //重点-> Parse each @Configuration class
  ConfigurationClassParser parser = new ConfigurationClassParser(
    this.metadataReaderFactory, this.problemReporter, this.environment,
    this.resourceLoader, this.componentScanBeanNameGenerator, registry);

  Set<BeanDefinitionHolder> candidates = new LinkedHashSet<>(configCandidates);
  Set<ConfigurationClass> alreadyParsed = new HashSet<>(configCandidates.size());
  do {
    parser.parse(candidates);
    parser.validate();

    //对parse出来的类中标有@Configuration注解的类继续循环进行处理
    Set<ConfigurationClass> configClasses = new LinkedHashSet<>(parser.getConfigurationClasses());
    configClasses.removeAll(alreadyParsed);
    ......省略代码
  } while (!candidates.isEmpty());

  if (sbr != null && !sbr.containsSingleton(IMPORT_REGISTRY_BEAN_NAME)) {
    sbr.registerSingleton(IMPORT_REGISTRY_BEAN_NAME, parser.getImportRegistry());
  }

  if (this.metadataReaderFactory instanceof CachingMetadataReaderFactory) {
    ((CachingMetadataReaderFactory) this.metadataReaderFactory).clearCache();
  }
}
```

上面的代码中首先从BeanDefinitionRegistry中获取所有标注了@Configuration注解的类，如果没有则直接返回，有则构建一个ConfigurationClassParser进行解析，然后对解析出来的类中有标注了@Configuration注解的类循环进行解析处理，直到所有的直接或间接标注@Configuration的类都被处理。接下来我们看下解析过程具体做了哪些工作。

```java
ConfigurationClassParser类源码

protected final SourceClass doProcessConfigurationClass(
			ConfigurationClass configClass, SourceClass sourceClass, Predicate<String> filter)
			throws IOException {
  if (configClass.getMetadata().isAnnotated(Component.class.getName())) {
    processMemberClasses(configClass, sourceClass, filter);
  }
  // Process any @PropertySource annotations
  for (AnnotationAttributes propertySource : AnnotationConfigUtils.attributesForRepeatable(
    sourceClass.getMetadata(), PropertySources.class,
    org.springframework.context.annotation.PropertySource.class)) {
    if (this.environment instanceof ConfigurableEnvironment) {
      processPropertySource(propertySource);
    } else {
      logger.info("Ignoring @PropertySource annotation on [" + sourceClass.getMetadata().getClassName() +
                  "]. Reason: Environment must implement ConfigurableEnvironment");
    }
  }

  // Process any @ComponentScan annotations
  Set<AnnotationAttributes> componentScans = AnnotationConfigUtils.attributesForRepeatable(
    sourceClass.getMetadata(), ComponentScans.class, ComponentScan.class);
  if (!componentScans.isEmpty() &&
      !this.conditionEvaluator.shouldSkip(sourceClass.getMetadata(), ConfigurationPhase.REGISTER_BEAN)) {
    for (AnnotationAttributes componentScan : componentScans) {
      // The config class is annotated with @ComponentScan -> perform the scan immediately
      Set<BeanDefinitionHolder> scannedBeanDefinitions =
        this.componentScanParser.parse(componentScan, sourceClass.getMetadata().getClassName());
      // Check the set of scanned definitions for any further config classes and parse recursively if needed
      for (BeanDefinitionHolder holder : scannedBeanDefinitions) {
        BeanDefinition bdCand = holder.getBeanDefinition().getOriginatingBeanDefinition();
        if (bdCand == null) {
          bdCand = holder.getBeanDefinition();
        }
        if (ConfigurationClassUtils.checkConfigurationClassCandidate(bdCand, this.metadataReaderFactory)) {
          parse(bdCand.getBeanClassName(), holder.getBeanName());
        }
      }
    }
  }

  // Process any @Import annotations
  processImports(configClass, sourceClass, getImports(sourceClass), filter, true);

  // Process any @ImportResource annotations
  AnnotationAttributes importResource =
    AnnotationConfigUtils.attributesFor(sourceClass.getMetadata(), ImportResource.class);
  if (importResource != null) {
    String[] resources = importResource.getStringArray("locations");
    Class<? extends BeanDefinitionReader> readerClass = importResource.getClass("reader");
    for (String resource : resources) {
      String resolvedResource = this.environment.resolveRequiredPlaceholders(resource);
      configClass.addImportedResource(resolvedResource, readerClass);
    }
  }

  // Process individual @Bean methods
  Set<MethodMetadata> beanMethods = retrieveBeanMethodMetadata(sourceClass);
  for (MethodMetadata methodMetadata : beanMethods) {
    configClass.addBeanMethod(new BeanMethod(methodMetadata, configClass));
  }

  // Process default methods on interfaces
  processInterfaces(configClass, sourceClass);

  // Process superclass, if any
  if (sourceClass.getMetadata().hasSuperClass()) {
    String superclass = sourceClass.getMetadata().getSuperClassName();
    if (superclass != null && !superclass.startsWith("java") &&
        !this.knownSuperclasses.containsKey(superclass)) {
      this.knownSuperclasses.put(superclass, configClass);
      // Superclass found, return its annotation metadata and recurse
      return sourceClass.getSuperClass();
    }
  }
  // No superclass -> processing is complete
  return null;
}
```

##### 扫描并注册Bean

对`@Configuration`注解的处理，最终会通过递归调用#doProcessConfigurationClass(ConfigurationClass, SourceClass, Predicate<String>)方法实现。这个方法里我们看到了对@PropertySource、@Bean、@Import、@ImportResource、@ComponentScan注解的处理。`@SpringBootApplication`注解元标注了`@ComponentScan`注解，Spring利用ComponentScanAnnotationParser类对这个注解进行处理，我们从这里继续追踪源码发现比较熟悉的Bean解析逻辑，通过ClassPathBeanDefinitionScanner进行包路径扫描，然后把扫描到的bean封装成BeanDefinition进行注册。

```java
public Set<BeanDefinitionHolder> parse(AnnotationAttributes componentScan, final String declaringClass) {
	ClassPathBeanDefinitionScanner scanner = new ClassPathBeanDefinitionScanner(this.registry,
				componentScan.getBoolean("useDefaultFilters"), this.environment, this.resourceLoader);

  Class<? extends BeanNameGenerator> generatorClass = componentScan.getClass("nameGenerator");
  boolean useInheritedGenerator = (BeanNameGenerator.class == generatorClass);
  scanner.setBeanNameGenerator(useInheritedGenerator ? this.beanNameGenerator :
                               BeanUtils.instantiateClass(generatorClass));

  ScopedProxyMode scopedProxyMode = componentScan.getEnum("scopedProxy");
  if (scopedProxyMode != ScopedProxyMode.DEFAULT) {
    scanner.setScopedProxyMode(scopedProxyMode);
  } else {
    Class<? extends ScopeMetadataResolver> resolverClass = componentScan.getClass("scopeResolver");
    scanner.setScopeMetadataResolver(BeanUtils.instantiateClass(resolverClass));
  }

  scanner.setResourcePattern(componentScan.getString("resourcePattern"));

  for (AnnotationAttributes filter : componentScan.getAnnotationArray("includeFilters")) {
    for (TypeFilter typeFilter : typeFiltersFor(filter)) {
      scanner.addIncludeFilter(typeFilter);
    }
  }
  for (AnnotationAttributes filter : componentScan.getAnnotationArray("excludeFilters")) {
    for (TypeFilter typeFilter : typeFiltersFor(filter)) {
      scanner.addExcludeFilter(typeFilter);
    }
  }

  boolean lazyInit = componentScan.getBoolean("lazyInit");
  if (lazyInit) {
    scanner.getBeanDefinitionDefaults().setLazyInit(true);
  }

  Set<String> basePackages = new LinkedHashSet<>();
  String[] basePackagesArray = componentScan.getStringArray("basePackages");
  for (String pkg : basePackagesArray) {
    String[] tokenized = StringUtils.tokenizeToStringArray(
      this.environment.resolvePlaceholders(pkg),
      ConfigurableApplicationContext.CONFIG_LOCATION_DELIMITERS);
    Collections.addAll(basePackages, tokenized);
  }
  for (Class<?> clazz : componentScan.getClassArray("basePackageClasses")) {
    basePackages.add(ClassUtils.getPackageName(clazz));
  }
  if (basePackages.isEmpty()) {
    basePackages.add(ClassUtils.getPackageName(declaringClass));
  }

  scanner.addExcludeFilter(new AbstractTypeHierarchyTraversingFilter(false, false) {
    @Override
    protected boolean matchClassName(String className) {
      return declaringClass.equals(className);
    }
  });
  return scanner.doScan(StringUtils.toStringArray(basePackages));
}
```

最后再次回到ApplicationContext#refres()方法中，在#finishBeanFactoryInitialization(beanFactory)这一步完成Bean(非延迟初始化Bean)的实例化操作。

