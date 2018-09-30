---
typora-copy-images-to: ..\images
typora-root-url: ..
---

# 4 IoC容器

## 4.1 IoC概述

### 4.1.1 什么是IoC

IoC（Inversion of Control）是一种用来解决组件之间依赖关系、配置及生命周期的设计模式，其中对组件依赖关系的处理是IoC的精华部分。IoC的实际意义就是把组件之间的依赖关系提取（反转）出来，由容器来具体配置。这样，各个组件之间就不存在hard-code的关联，任何组件都可以最大程度的得到重用。运用了IoC模式后我们不再需要自己管理组件之间的依赖关系，只需要声明由容器去实现这种依赖关系。就好像把对组件之间依赖关系的控制进行了倒置，不再由组件自己来建立这种依赖关系而交给容器去管理。

**IoC就是创建对象的控制权进行转移，以前创建对象的主动权和创建时机是由自己把控的，而现在这种权力转移到第三方。对于spring框架来说，就是由spring来负责控制对象的生命周期和对象间的关系。所以对象与对象之间是 松散耦合，这样也方便测试，利于功能复用，更重要的是使得程序的整个体系结构变得非常灵活**。通过依赖注入机制，我们只需要通过简单的配置，而无需任何代码就可指定目标需要的资源，完成自身的业务逻辑，而不需要关心具体的资源来自何处，由谁实现。

###4.1.2 IoC的类型

从注入方法上看，IoC主要可以分为三种类型：构造函数注入，属性(setter方法）注入，和接口注入。

(1) 构造函数注入
在构造期即创建一个完整、合法的对象”，所有依赖关系均在构造函数中设定，<font color="red">依赖关系集中呈现，更加易读</font>。 由于没有setter方法，依赖关系在构造时由容器一次性设定，因此组件在被创建之后即处于相对“不变”的稳定状态，无需担心上层代码在调用过程中执行setter方法对组件依赖关系产生破坏，特别是对于Singleton模式的组件而言，这可能对整个系统产生重大的影响。同样，由于关联关系仅在构造函数中表达，只有组件创建者需要关心组件内部的依赖关系。对调用者而言，组件中的依赖关系处于黑盒之中。对上层屏蔽不必要的信息，也为系统的层次清晰性提供了保证。通过构造子注入，意味着我们可以在构造函数中决定依赖关系的注入顺序，对于一个大量依赖外部服务的组件而言，依赖关系的获得顺序可能非常重要.
```java
public class UserService{
    private UserDao userDao;
    public UserService(UserDao userDao){
        this.userDao = userDao;
    }
}
```
(2) 属性注入
在各种类型的依赖注入模式中，设值注入模式在实际开发中得到了最广泛的应用（其中很大一部分得力于Spring框架的影响）。基于设置模式的依赖注入机制<font color="red">灵活方便</font>。即通过类的setter方法完成依赖关系的设置。  如果依赖关系（或继承关系）较为复杂，属性注入往往更为简洁。 对于某些第三方类库而言，可能要求我们的组件必须提供一个默认的构造函数，此时构造函数依赖注入机制就体现出其局限性。
```java
public class UserService{
    private UserDao userDao;
    public void setUserDao(UserDao){
        this.userDao = userDao;
    }
}
```
（3）接口注入
将调用类所有依赖注入的方法抽取到一个接口中，调用类通过实现该接口提供相应的注入方法。接口注入模式因为历史较为悠久，在很多容器中都已经得到应用。但由于其在灵活性、易用性上不如其他两种注入模式。
``` java
public interface InjectUserDao{
	public void setUserDao(UserDao userDao);
}

public class UserService implements InjectUserDao{
	private UserDao userDao;
	public void setUserDao(UserDao userDao){        
        this.userDao = userDao;
	}    
}

```
### 4.1.3 通过容器完成依赖关系注入

spring容器通过配置文件或注解描述类和类之间的依赖关系，自动完成类的初始化和依赖工作。

spring 配置文件片段如下：

```xml

<bean id="userDao"  class="UserDao"/>
<bean   id="userService"   class="UserService"  
		p:userDao-ref="userDao"/>

```
在容器启动时， Spring根据配置文件的描述信息，自动实例化bean并完成依赖关系的装配，从容器中返回准备就绪的bean实例，然后直接使用。
##4.2 相关Java基础知识
###4.2.1 类装载器ClassLoader
1.类的装载器工作机制
类装载器就是寻找类的解码文件并构造出来在JVM内部表示对象的方法。步骤如下：
（1）装载：查找和导入class文件
（2）链接：执行校验，准备和解析步骤，其中解析步骤是可以选择的。
   1）校验：检查载入class文件数据的正确性；
   2）准备：给类的静态变量分配存储空间；
   3）解析：将符号引用转换为直接引用；
（3）初始化：对类的静态变量，静态代码块执行初始化工作。
类加载工作有ClassLoader及其子类负责。ClassLoader是一个重要的Java运行时系统组件，它负责运行时查找和装入class字节码文件。JVM运行时会产生3个ClassLoader:根加载器，ExtClassLoader(扩展加载器）和AppClassLoader(应用类加载器)。其中，根加载器不是ClassLoader的子类，它使用c++语言编写，因而在java中看不到它，根加载器负责加载JRE的核心类库，如：JRE目标下的rt.jar，charsets.jar等。ExtClassLoader和AppClassLoader都是ClassLoader子类，其中ExtClassLoader负责加载JRE扩展目录ext中的JAR类包；AppClassLoader负责加载Classpath路径下类包。继承关系如下：
祖父 ClassLoader>>ExtClassLoader>>AppClassLoader。
JVM装载类时使用全盘负责委托机制
##4.3 资源访问利器
###  4.3.1资源抽象接口
##  4.4 BeanFactory 和ApplicationContext
Spring通过配置文件描述bean和bean之间依赖关系，利用Java语言的反射功能实例化Bean并创建Bean之间依赖关系。
Bean工厂（com.springframework.beans.factory.BeanFactory)是Spring框架最核心的接口，它提供了高级IoC配置机制	。BeanFactory使管理不同类型的Java对象成为可能，应用上下文（org.springframework.context.ApplicationContext)建立在BeanFactory基础之上。提供了更多面向应用的可能，提供了国家化支持和框架事件体系，更容易创建创建实际应用。
BeanFactory是Spring框架基础设施，面向Spring本身；ApplicationContext面向使用Spring框架的开发者，几乎所有的应用场合都可以使用ApplicationContext而不是BeanFactory。

###4.4.1 BeanFactory
BeanFactory是类的通用工厂，它可以创建并管理类的各种类的对象。提供了多种实现，建议使用XmlBeanDefinitionReader,DefaultListableBeanFactory。BeanFactory体系结构如下图：

![BeanFactory](/images/BeanFactory.jpg)

``` java
private static void LifeCycleInBeanFactory(){
       //①下面两句装载配置文件并启动容器
 	   Resource res = new ClassPathResource("com/smart/beanfactory/beans.xml");
       BeanFactory bf= new DefaultListableBeanFactory();
       XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader((DefaultListableBeanFactory)bf);
       reader.loadBeanDefinitions(res);

       //②向容器中注册MyBeanPostProcessor后处理器
       ((ConfigurableBeanFactory)bf).addBeanPostProcessor(new MyBeanPostProcessor());

       //③向容器中注册MyInstantiationAwareBeanPostProcessor后处理器
       ((ConfigurableBeanFactory)bf).addBeanPostProcessor(
               new MyInstantiationAwareBeanPostProcessor());
       //④第一次从容器中获取car，将触发容器实例化该Bean，这将引发Bean生命周期方法的调用。
       Car car1 = (Car)bf.getBean("car");
       car1.introduce();
       car1.setColor("红色");

       //⑤第二次从容器中获取car，直接从缓存池中获取
       Car car2 = (Car)bf.getBean("car");

       //⑥查看car1和car2是否指向同一引用
       System.out.println("car1==car2:"+(car1==car2));//true
       //⑦关闭容器
       ((DefaultListableBeanFactory)bf).destroySingletons();

    }

```
XMLBeanDefinitionReader通过Resource 装载Spring配置信息并启动IoC容器，然后就可以通过BeanFactory#getBean(beanName)方法从IoC容器中获取Bean。通过BeanFactory启动IoC容器时，并不会初始化配置文件中定义的Bean,初始化动作发生在第一个调用时，对于单实例的Bean来说，BeanFactory会缓存Bean实例，所以第二次使用getBean获取Bean时，将直接从IoC容器缓存中获取Bean实例。Spring在DefaultSingletonBeanRegister类中提供了一个用于缓存单实例Bean的缓存器，他是一个HashMap实现的缓存器，单实例的Bean以BeanName为键保存。

### 4.4.2 ApplicationContext

(1) ApplicationContext主要有实现类有ClassPathXmlApplicationContext和FileSystemXmlApplicationContext,

前者默认是从类路径加载配置文件com/smart/context/beans.xml等于classpath:com/smart/context/beans.xml，后者默认从文件系统中加载文件com/smart/context/beans.xml=file:com/smart/context/beans.xml。**BeanFacotory在初始化容器时并未实例化Bean,直到第一次访问某个Bean时才实例化目标Bean;而ApplicationContext在初始化应用上下文时就实例化所有单实例的Bean。

ApplicationContext ctx=new ClassPathXmlApplicationContext("com/smart/context/beans.xml")

ApplicationContext ctx = new FileSystemXmlApplicationContext("com/smart/context/beans.xml")

![applicationcontext](/images/applicationcontext.jpg)

（2）WebApplicationContext

作用： 
1. 它允许从相对于Web根目录的路径中加载配置文件完成初始化工作。从WebApplicationContext中可以获取ServletContext引用，整个Web应用上下文对象将作为属性放置在ServletContext中，以便Web应用环境可以访问Spring上下文。 
2. WebApplicationContext还为Bean提供了三个新的作用域，request、session和globalsession。 
  其中两个参数HttpServletRequest：服务器从客户端拿去数据 
  HttpServletResponse：服务器向前台传送数据