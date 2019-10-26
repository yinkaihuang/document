# spring中getBean源码分析

> BeanFactory是什么
>
> FactoryBean又是什么
>
> BeanPostProcessor何时被调用
>
> 循环依赖是如何解决的



## BeanFactory是什么

**BeanFactory**是ioc容器用户缓存实例化的对象，实现了对象的单列。其实现类为`DefaultListableBeanFactory`。该类不但实现了`BeanFactory`接口同时也实现了`BeanDefinitionRegistry`接口。`BeanDefinitionRegistry`用于收集`BeanDefinition`，为对象实例化提供必要的数据结构。



## FactoryBean是什么

**FactoryBean** 其接口上有两个重要的方法getObjectType和getObject。很多框架就是利用那个上面的方法来实现动态向spring的容器中注入对象，例如JPA等等。



## BeanPostProcessor何时被调用

**BeanPostProcessor**的调用时机是在`InitializingBean`的afterPropertiesSet的前后调用。很多框架也是利用这个时机来动态修改spring容器中的bean实例化的数据。



## getBean主要流程分析



```
1.调用transformedBeanName(name)用来讲传入的名称获取到beanName也就是spring容器中的唯一标识名称
这里转变主要是 别名解析 前缀&去掉

2.getSingleton(beanName)从缓存中获取数据，这里缓存中获取的数据可能是提早暴露的也就是属性未完全初始化

3.如果上一步获取到数据为空调用getObjectForBeanInstance(sharedInstance, name, beanName, null)，这里是将上一步获取到的数据进行转变。例如上一步获取到的数据为FactoryBean实现对象而name不是以&头则调用该对象上面getObject方法返回实例给用户。否则返回原始对象。

4.如果在第2步时从缓存中获取的数据为空时，调用RootBeanDefinition mbd = getMergedLocalBeanDefinition(beanName)，根据对象名称获取其在spring容器中的数据结构

5.String[] dependsOn = mbd.getDependsOn();接着获取该数据结构所有在这之间初始化的对象（也就是@DependOn 修饰的改对象）

6.getBean(dep);依次调用上面的getBean方法

7.createBean(beanName, mbd, args);调用正式创建对象方法

8.Object bean = resolveBeforeInstantiation(beanName, mbdToUse);主要是调用InstantiationAwareBeanPostProcessor上面的postProcessBeforeInstantiation方法

9.Object beanInstance = doCreateBean(beanName, mbdToUse, args);

10.instanceWrapper = createBeanInstance(beanName, mbd, args);

11.applyMergedBeanDefinitionPostProcessors(mbd, beanType, beanName);

12.populateBean(beanName, mbd, instanceWrapper);

13.exposedObject = initializeBean(beanName, exposedObject, mbd);
```

