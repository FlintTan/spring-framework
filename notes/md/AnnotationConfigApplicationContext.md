# AnnotationConfigApplicationContext

## this();

### super();

- new DefaultListableBeanFactory(); ——通过父类GenericApplicationContext构造方法

### 初始化reader

- new 一个@Condition 注解的解析器
- 注册注解的配置处理器

	- 将内部各种组件、解析器的BeanDefinition 注册到容器中

	  internalConfigurationAnnotationProcessor
	  internalAutowiredAnnotationProcessor
	  ...

### 初始化scanner

- 根据useDefaultFilters这个字段，决定是否使用默认的过滤器

	- true的时候，扫描@Component注解以及JSR250/330规范下的注解

- 设置环境
- 设置资源加载器

## registry(componentClass);

### 注册自己传入的配置类

- 包装成BeanDefinitionHolder，注册到registry中

## refresh();

### 1.prepareRefresh();前期准备工作

- closed = false; （不是已关闭状态） active = true; （处于活跃状态）
- initPropertySources(); ——初始化属性值
- getEnvironment().validateRequiredProperties(); ——验证环境所需的必要参数
- earlyApplicationListeners、earlyApplicationEvents ——new LinkedHashSet<>(); 

### 2.obtainFreshBeanFactory();获取新鲜容器

- GenericApplicationContext#refreshBeanFactory();设置序列化ID

### 3.prepareBeanFactory(beanFactory);

### 4.postProcessBeanFactory(beanFactory);

### 5.invokeBeanFactoryPostProcessors(beanFactory);调用Bean工厂后置处理器

这个方法主要作用就是调用 BeanFactoryPostProcessor (BFPP)中的 postProcessBeanFactory 方法。其中，BeanDefinitionRegistryPostProcessor (BDRPP)是 BeanFactoryPostProcessor 的一个子类，对 BeanFactoryPostProcessor 做了一个扩展，加了一个 postProcessBeanDefinitionRegistry 方法，可在 postProcessBeanFactory 方法前执行。

postProcessBeanDefinitionRegistry：作用：可修改上下文中内部Bean的定义注册表，添加更多的bean定义，可额外添加一些组件。执行时机：（beanFactory标准初始化之后）bean定义将要被加载，还未加载的时候，先于 postProcessBeanFactory 执行。
postProcessBeanFactory：作用：可添加/覆盖属性，甚至可以初始化Bean。执行时机：（beanFactory标准初始化之后）所有的Bean定义信息已经加载到容器中，但是Bean实例还没有被创建。

- 1.先调用 beanFactoryPostProcessors 中 和  beanFactory 中获取到的 BDRPP 的 postProcessBeanDefinitionRegistry 方法；然后调用这些 BDRPP 对应的 postProcessBeanFactory 方法；最后调用 beanFactoryPostProcessors 中 普通BFPP 的 postProcessBeanFactory 方法

	- 1.区分 BeanFactoryPostProcessor 与 BeanDefinitionRegistryPostProcessor ，并放入两个List中

		- 如果是后置处理器实现了 BeanDefinitionRegistryPostProcessor ，则去处理配置定义信息

			- todo

	- 2.先sort排序一下，然后调用实现了 PriorityOrdered 优先顺序接口的后置处理器 postProcessBeanDefinitionRegistry
	- 3.先sort排序一下，然后调用实现了 Ordered 有序接口的后置处理器 postProcessBeanDefinitionRegistry
	- 4.调用无序的后置处理器 postProcessBeanDefinitionRegistry
	- 5.到此，所有的bean定义注册后置处理器方法都执行完了，处理所有bean工厂的后置处理器方法 postProcessBeanFactory

		- 1.beanFactoryPostProcessors 与 beanFactory中获取的 BDRPP 中的 postProcessBeanFactory
		- 2.beanFactoryPostProcessors 中的 普通BFPP 的 beanFactoryPostProcessors

- 2.调用从 beanFactory 中获取到的普通BFPP 后置处理器的 postProcessBeanFactory 方法

	- 与上面类似，先区分类型（优先顺序->有序->无序），然后排序，调用 postProcessBeanFactory

- 3.清除合并的bean定义高速缓存，删除尚未被认为适合完全元数据高速缓存的bean条目，此时将保留已经创建的bean的元数据

### 6.registerBeanPostProcessors(beanFactory);

### 7.initMessageSource();

### 8.initApplicationEventMulticaster();

### 9.onRefresh();

### 10.registerListeners();

### 11.finishBeanFactoryInitialization(beanFactory);

### 12.finishRefresh();

### 13.resetCommonCaches();

*XMind: ZEN - Trial Version*