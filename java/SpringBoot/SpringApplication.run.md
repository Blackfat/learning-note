```java
public static ConfigurableApplicationContext run(Object[] sources, String[] args) {
		return new SpringApplication(sources).run(args);
}

public SpringApplication(Object... sources) {
		initialize(sources);
}

private void initialize(Object[] sources) {
		if (sources != null && sources.length > 0) {
			this.sources.addAll(Arrays.asList(sources));
		}
         // 判断是否是web程序
		this.webEnvironment = deduceWebEnvironment();
         // 从类路径下找到META-INF/spring.factories下配置的所有的ApplicationContextInitializer
		setInitializers((Collection) getSpringFactoriesInstances(
				ApplicationContextInitializer.class));
         // 从类路径下找到META-INF/spring.factories下配置的所有的ApplicationListener
		setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
         // 找到当前main方法的类
		this.mainApplicationClass = deduceMainApplicationClass();
}

public ConfigurableApplicationContext run(String... args) {
		StopWatch stopWatch = new StopWatch();
		stopWatch.start();
		ConfigurableApplicationContext context = null;
		FailureAnalyzers analyzers = null;
		configureHeadlessProperty();
        // 从类路径下找到META-INF/spring.factories下配置的所有的SpringApplicationRunListener，并将
        // 保存的ApplicationListener注入到SpringApplicationRunListener中，为事件发布做准备
		SpringApplicationRunListeners listeners = getRunListeners(args);
        // 调用listeners的starting
		listeners.starting();
		try {
             // 封装传入的参数
			ApplicationArguments applicationArguments = new DefaultApplicationArguments(
					args);
             // 准备环境，完成后回调SpringApplicationRunListener的environmentPrepared方法
			ConfigurableEnvironment environment = prepareEnvironment(listeners,
					applicationArguments);
             // 打印banner
			Banner printedBanner = printBanner(environment);
             // 判断是否是web程序创建web容器还是普通的容器
			context = createApplicationContext();
             // 从类路径下找到META-INF/spring.factories下配置的所有的FailureAnalyzers并初始化
			analyzers = new FailureAnalyzers(context);
             // 将environment保存至环境中
             // 遍历之前保存的ApplicationContextInitializer的Initialize()方法
             // 回调SpringApplicationRunListener的contextPrepared方法
             // 从源加载Bean的定义信息，并封装成BeanDefinition对象，并注册到ApplicationContext
             // 回调SpringApplicationRunListener的contextLoaded方法
			prepareContext(context, environment, listeners, applicationArguments,
					printedBanner);
             // 刷新容器，进行组件的扫描，创建和加载
			refreshContext(context);
             // 从容器中获取所有的ApplicationRunner和CommandLineRunner，先执行ApplicationRunner，再               执行CommandLineRunner
			afterRefresh(context, applicationArguments);
             // 回调SpringApplicationRunListener的finished方法
			listeners.finished(context, null);
			stopWatch.stop();
			if (this.logStartupInfo) {
				new StartupInfoLogger(this.mainApplicationClass)
						.logStarted(getApplicationLog(), stopWatch);
			}
			return context;
		}
		catch (Throwable ex) {
			handleRunFailure(context, listeners, analyzers, ex);
			throw new IllegalStateException(ex);
		}
	}
```

