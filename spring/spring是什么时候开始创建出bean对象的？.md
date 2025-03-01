# 单例模式
## 单例情况创建 bean 对象的流程
首先我们需要知道一个类叫ClassPathXmlApplicationContext，我们去跟下源码。
在 Spring 框架中，`ClassPathXmlApplicationContext` 主要负责加载 XML 配置文件并创建和管理 bean。底层还是调用 getBean 来创建或获取单例对象。

### 主要的方法和类

1. **`ClassPathXmlApplicationContext`**：
   - `ClassPathXmlApplicationContext` 继承自 `AbstractApplicationContext`，其主要职责是读取 XML 配置文件并刷新其上下文。

2. **`refresh` 方法**：
   - `refresh()` 方法是整个上下文刷新和 bean 创建的核心。调用这个方法时，Spring 会读取配置、实例化 bean，并构建容器。

3. **`getBean` 方法**（**核心**）：
   - 最终 bean 的创建是通过 `getBean()` 方法反向调用的，具体实现依赖于 `DefaultListableBeanFactory` 的 `createBean` 方法。
### 跟踪源码

让我们逐步跟踪一下关键的源码部分：

4. **入口**：创建 `ClassPathXmlApplicationContext` 和调用 `refresh` 方法：

    ```java
    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
    ```

   在构造函数中，`refresh()` 方法会被调用。

   ```java
   public ClassPathXmlApplicationContext(String configLocation, boolean refresh, boolean parent) {
       super(parent);
       // Other initializations...
       refresh(); // 调用
   }
   ```

5. **`refresh` 方法**：

   ```java
   @Override
   public void refresh() throws BeansException, IllegalStateException {
       // 一些准备工作
       // ...
       	// 开始初始化bean对象
       this.finishBeanFactoryInitialization(beanFactory);
   }
   ```

6. **`finishBeanFactoryInitialization`** 方法：
```java
protected void finishBeanFactoryInitialization(ConfigurableListableBeanFactory beanFactory) {

    // 省略..
    beanFactory.preInstantiateSingletons();
}
```
7. **`preInstantiateSingletons` 方法**：

   该方法在 `DefaultListableBeanFactory` 中定义并负责实例化单例 bean：

   ```java
   public void preInstantiateSingletons() throws BeansException {
       for (String beanName : this.beanDefinitionNames) {
           if (isSingleton(beanName)) {
               getBean(beanName); // 调用 getBean 方法
           }
       }
   }
   ```

8. **`getBean` 方法**：

   当您调用 `getBean` 获取 bean 时，会进一步调用 `doGetBean`，最终到达 `createBean` 方法：

   ```java
   public T getBean(String name, Class<T> requiredType) throws BeansException {
       return (T) getBean(name); // 关键
   }

   protected <T> T doGetBean(final String beanName, final boolean allowEagerInit) {
	// 从缓存中获取bean
	  Object sharedInstance = this.getSingleton(beanName);
	// 最终返回的bean对象
	  Object beanInstance;
       // 如果缓存中存在bean，则直接return
    if (sharedInstance != null && args == null) {  
		beanInstance = this.getObjectForBeanInstance(sharedInstance, name, beanName, (RootBeanDefinition)null);
    }else{
		// 如果缓存中不存在bean则调用createBean方法创建bean并放进缓存中
		// 如果是单例则调用getSingleton方法创建单例对象
	    if (mbd.isSingleton()) {  
	    sharedInstance = this.getSingleton(beanName, () -> {  
	            return this.createBean(beanName, mbd, args);  
	    });  
	    beanInstance = this.getObjectForBeanInstance(sharedInstance, name, beanName, mbd);  
	    }  
	 return  beanInstance
}
   ```

### 总结

- `ClassPathXmlApplicationContext` 的创建Bean对象底层是通过getBean方法来创建的。
- `getBean()` 最为核心，内部逻辑如下：
	1. 从缓存中获取bean。
	2. 如果bean存在则直接返回。
	3. bean不存在则创建bean。
		1. 创建bean的时候判断是单例还是多例。
		2. 如果是单例则创建在返回bean对象。

## 效果
```java
@Test
public void testScope(){
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-scope.xml");

    SpringBean sb1 = applicationContext.getBean("sb", SpringBean.class);
    System.out.println(sb1);

    SpringBean sb2 = applicationContext.getBean("sb", SpringBean.class);
    System.out.println(sb2);
}
```

执行结果：  
![](https://cdn.nlark.com/yuque/0/2022/png/21376908/1665221074412-9b48c6e3-44f0-492c-a712-37b4baa24341.png#averageHue=%23937e66&clientId=ufc7e21e2-2cbb-4&from=paste&height=173&id=u956152e2&originHeight=173&originWidth=573&originalType=binary&ratio=1&rotation=0&showTitle=false&size=26951&status=done&style=shadow&taskId=uad922a99-49bc-4c58-a11e-9d2b89f6856&title=&width=573)  
# 多例模式
## 多例模式创建 bean 对象的流程
## 效果

编写测试程序：

```java
@Test
public void testCustomScope(){
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-scope.xml");
    SpringBean sb1 = applicationContext.getBean("sb", SpringBean.class);
    SpringBean sb2 = applicationContext.getBean("sb", SpringBean.class);
    System.out.println(sb1);
    System.out.println(sb2);
    // 启动线程
    new Thread(new Runnable() {
        @Override
        public void run() {
            SpringBean a = applicationContext.getBean("sb", SpringBean.class);
            SpringBean b = applicationContext.getBean("sb", SpringBean.class);
            System.out.println(a);
            System.out.println(b);
        }
    }).start();
}
```

执行结果：  
![](https://cdn.nlark.com/yuque/0/2022/png/21376908/1665297614749-ae92b97d-85fd-4792-af87-e72d35784187.png#averageHue=%23343331&clientId=u3fe1442a-4567-4&from=paste&height=260&id=u600551ec&originHeight=260&originWidth=587&originalType=binary&ratio=1&rotation=0&showTitle=false&size=43796&status=done&style=shadow&taskId=u14c4b472-78e4-43c6-b593-93b39a42d3f&title=&width=587)


# 总结
Spring 创建对象分俩种情况：
- 第一种单例模式：在单例模式下 bean 的创建是通过new classPathApplicationContext 的时候创建的对象（底层也是调用 getBean 创建对象），后续的所有 getBean 方法都会服用第一次的对象。
- 第二种多例模式：每调用一次 getBean 都会创建一个 Bean 对象