# Spring概述
&emsp;&emsp;Spring 的主要作用就是为代码“解耦”，降低代码间的耦合度。就是让对象和对象（模块和模块）之间的关系不通过代码直接关联，而是通过配置来说明。减轻对项目模块之间的管理， 类和类之间的管理， 帮助开发人员创建对象，管理对象之间的关系。当需要修改某一模块时不会因为与其他模块有关联而需要一起修改。其核心特性就是IoC和AOP

# IoC&DI

## 什么是IOC？
&emsp;&emsp;IoC是Spring提供的容器，它的全称是Inversion of Control，直译为**控制反转**。

&emsp;&emsp;**控制**：指的是对实现类的控制，如创建对象，对象的属性赋值，对象之间的关系管理等。
&emsp;&emsp;*不反转的情况：开发人员直接在code中new对象，需要开发人员在code中主动管理对象。*
&emsp;&emsp;**反转**：反转指的是这种控制权从调用类中移除，交给第三方决定。即某一接口具体实现类的选择控制权从调用类中移除，转交给第三方决定。**对象的创建、初始化、等操作交由Spring容器进行管理。**


## 为什么要用IoC
&emsp;&emsp;目的就是减少对代码的改动， 也能实现不同的功能。实现解耦合。
&emsp;&emsp;首先，需要理解软件系统中对象之间的耦合关系。
<center>

![Alt text](http://3ms.huawei.com/hi/staticimages/hi3msf/images/2017/0925/12/59c8803b771db.jpg)
软件系统中耦合的对象
</center>

&emsp;&emsp;软件系统中的对象就像图中的齿轮一样，互相啮合，互相协作，这种互相嵌入互相啮合的关系即是**耦合**。这种耦合关系同时也互相牵制着，例如一旦对象B这个齿轮不能正常工作，那与之耦合的A、C、D都将不能正常工作，最终导致整个系统的崩溃，且我们修复时需要对所有具有耦合关系的对象一起修复，这无疑是很麻烦的事。
&emsp;&emsp;而控制反转（IoC）正是为减少系统中的代码耦合度而设计的，其基本思想是：借助于一个第三方平台（即IoC容器）来实现解耦。
<center>

![Alt text](http://3ms.huawei.com/hi/staticimages/hi3msf/images/2017/0925/12/59c8803b77a06.jpg)
IoC容器解耦示意图
</center>

&emsp;&emsp;在没有引入IoC容器之前，对象A要创建和使用对象B，都必须亲力亲为，控制权在对象A手上。但是，在引入IoC容器之后，对象A与对象B之间没有了直接联系，创建和使用对象的控制权转移到了IoC平台这个“第三方”上。当对象A运行到需要对象B的时候，IoC容器就会主动创建一个对象B并注入到对象A需要的地方。
&emsp;&emsp;通过前后的对比，我们不难看出来：对象A获得依赖对象B的过程,由主动行为变为了被动行为，控制权颠倒过来了，这就是“控制反转”这个名称的由来。


## 什么是DI
&emsp;&emsp;DI全称是Dependency Injection，直译为**依赖注入**，是实现IoC（控制反转）的手段。它让调用类对某一接口实现类的依赖关系由第三方（IoC）注入，以移除调用类对某一接口实现类的依赖。
&emsp;&emsp;首先，需要理解什么是依赖。例如在ClassA中，有一个ClassB实例，则称ClassA对ClassB有一个依赖。示例代码：
```Java
public class A{
    B b;
    public A(){
        b = new B();
    }
}
```
&emsp;&emsp;可以看到，b的初始化被写死在A的构造函数中。上面将依赖在构造函数中直接初始化是一种 Hard init 方式，弊端在于两个类不够独立，不方便测试和修改。我们还有另外一种 Init 方式，如下：
```java
public class A{
    B b;
    public A(B b){
        this.b = b;
    }
}
```
&emsp;&emsp;上面代码中，我们将 b 对象作为构造函数的一个参数传入。在调用 A 的构造方法之前外部就已经初始化好了 B 对象。像这种非自己主动初始化依赖，而通过外部来传入依赖的方式，我们就称为依赖注入。
&emsp;&emsp;依赖注入就是将实例变量传入到一个对象中去(Dependency injection means giving an object its instance variables)。
&emsp;&emsp;简单来说依赖注入主要有两个好处：
- 解耦，将依赖之间解耦。
- 因为已经解耦，所以方便做单元测试，尤其是 Mock 测试。

## 控制反转和依赖注入的关系
&emsp;&emsp;控制反转是一种思想，依赖注入是一种设计模式。IoC框架通常使用依赖注入作为实现控制反转的方式，但二者不等同，IoC还有其他的实现控制反转的方式。
&emsp;&emsp;简单理解，IoC（控制反转）就是控制权的转移，即把创建（new）对象的权力，反转给第三方Spring 框架去创建（new）。也就是把对象的创建的权利及对象的生命周期的管理过程交由Spring 框架来处理，从此在开发过程中不再需要关注对象的创建和生命周期的管理，而是在需要时由 Spring 框架提供，这个由 Spring 框架管理对象创建和生命周期的机制称之为控制反转。而 IoC 容器在创建被调用者的实例时，会自动将调用者需要的外部资源（如对象实例、常量数据等）注入给调用者，这样，调用者通过 IoC 容器获得被调用者资源，这称为依赖注入，也即 DI。
&emsp;&emsp;依赖注入和控制反转是对同一件事情的不同描述，从某个方面讲，就是它们描述的角度不同。依赖注入是从应用程序的角度在描述，可以把依赖注入描述完整点：应用程序依赖容器创建并注入它所需要的外部资源；而控制反转是从容器的角度在描述，描述完整点：容器控制应用程序，由容器反向的向应用程序注入应用程序所需要的外部资源。


## 依赖注入的三种常见注入方式
DI的三种常见注入方式为：
- 接口注入
- setter注入
- 构造器注入

### 接口注入
```Java
public class ClassA {  
    private InterfaceB clzB;  
    public void doSomething() {  
        Ojbect obj = Class.forName(Config.BImplementation).newInstance();  
        clzB = (InterfaceB)obj;  
        clzB.doIt();   
    }  
    ……  
}
```
&emsp;&emsp;上述代码中,ClassA依赖于InterfaceB的实现，在我们获取InterfaceB的实现实例时，传统的方法是在代码中创建InterfaceB实现类的实例，并将其赋予clzB，ClassA在编译期就依赖于InterfaceB的实现。为了将调用者与实现者在编译期分离，便有了上述代码。根据预先在配置文件中设定的实现类的类名（Config.BImplementation），动态加载实现类，并通过InterfaceB强制转型后为ClassA所用，这便是接口注入的雏形。

### Setter方法注入
setter注入模式在实际开发中有非常广泛的应用，setter方法更加直观，如下是一个spring的配置文件：
```xml
<?xml version="1.0" encoding="UTF-8"?>    
<beans xmlns="http://www.springframework.org/schema/beans"    
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"    
         xmlns:aop="http://www.springframework.org/schema/aop"    
         xmlns:tx="http://www.springframework.org/schema/tx"    
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.1.xsd    
           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.1.xsd    
           http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.1.xsd">    
    
    <!-- 使用spring管理对象的创建，还有对象的依赖关系 -->    
    <bean id="userDao4Mysql" class="com.tgb.spring.dao.UserDao4MysqlImpl"/>    
    
    <bean id="userDao4Oracle" class="com.tgb.spring.dao.UserDao4OracleImpl"/>    
        
    <bean id="userManager" class="com.tgb.spring.manager.UserManagerImpl">    
        <!-- (1)userManager使用了userDao，Ioc是自动创建相应的UserDao实现，都是由容器管理-->    
        <!-- (2)在UserManager中提供构造函数，让spring将UserDao实现注入（DI）过来 -->    
        <!-- (3)让spring管理我们对象的创建和依赖关系，必须将依赖关系配置到spring的核心配置文件中 -->    
    
        <property name="userDao" ref="userDao4Oracle"></property>    
    </bean>    
        
</beans>
```
接着使用setter方法来注入
```Java
import com.tgb.spring.dao.UserDao;    
    
public class UserManagerImpl implements UserManager{        
    private UserDao userDao;    
    //使用set方法赋值    
    public void setUserDao(UserDao userDao) {    
        this.userDao = userDao;    
    }    
    @Override    
    public void addUser(String userName, String password) {    
        userDao.addUser(userName, password);    
    }    
}
```

### 构造器注入
Spring的配置文件同Setter方法中。
使用构造器的注入写法：
```Java
import com.tgb.spring.dao.UserDao;    
        
public class UserManagerImpl implements UserManager{    
    private UserDao userDao;    
    //使用构造方式赋值    
    public UserManagerImpl(UserDao userDao) {    
        this.userDao = userDao;    
    }    
    @Override    
    public void addUser(String userName, String password) {    
        userDao.addUser(userName, password);    
    }    
}
```


# AOP
<!-- 
Reference:https://www.jianshu.com/p/e2a49af63a33
 -->
## 什么是AOP？
&emsp;&emsp;AOP是Aspect Oriented Programing的简称，面向切面编程。
&emsp;&emsp;我的理解是：比如模拟一个有多个线程的进程，希望在其中某个线程前后打印一下日志，那么可以形象的理解成将进程在该线程前后切开，然后前后插入打印日志的线程，因此成为面向切面编程。
最直接的办法是在该线程的code中直接添加日志打印的调用，但这样当我们不需要该操作时，又需要去code中删除，这样会很麻烦，为了不用每次去修改源对象，进而使用了**代理模式**来实现面向切面编程。

## 什么是代理模式
&emsp;&emsp;代理是指为某一对象创建一个代理对象，通过代理对象来控制对原对象的访问。它的设计思路是：定义一个抽象接口，让代理类和真实类分别去实现它。
&emsp;&emsp;真实类：实现了抽象接口，定义了真实类所需要实现的业务逻辑，供代理类调用，它只关注真正的业务逻辑。
&emsp;&emsp;代理类：实现了抽象接口，是真实类的代理，通过真实类的业务逻辑方法来实现抽象方法，并且可以在其中附加自己的操作。
&emsp;&emsp;真实类和代理类的关系可以类比于房东和中介。
&emsp;&emsp;代理模式分为**静态代理**和**动态代理**


## 静态代理
&emsp;&emsp;以房屋出租为例，根据上面的设计思路，首先创建出租房屋这个抽象接口
```Java
public interface Renter {
    // 提供房屋
    void HouseProviding();
}
```
&emsp;&emsp;静态代理需要创建真实角色(房东)和代理角色(中介)，分别实现出租这个接口。真实角色（房东）直接实现即可，因为真实角色（房东）的主要任务就是提供房屋。
```Java
public class RealRenter implements Renter {
    @Override
    public void Renting() {
        System.out.println("看房:房东提供房屋");
    }
}
```
&emsp;&emsp;而代理角色（中介）则有额外的工作要实现，比如租房前后与租房者的联系，但提供房屋这件事只能由房东亲自上阵，中介做不了。所以在代理类中需要将真实类的对象传进来，由真实类来提供核心功能（提供房屋）。
```Java
public class ProxyRenter implements Renter {
    //接收真实类
    private Renter renter;
    //通过构造方法传入真实的出租者
    public ProxyRenter(Renter renter) {
        this.renter = renter;
    }
    @Override
    public void Renting() {
        System.out.println("中介找租房的人");
        //由房东提供房屋
        this.renter.Renting();
        System.out.println("与中介签订合同");
    }
}
```
&emsp;&emsp;上述便是静态代理的一个基本逻辑。在代理类中，可以看到，维护了一个`Renter`对象，通过构造方法传进来一个真实的`Renter`对象给其赋值，然后出租这个方法里，使用真实对象来提供出租的房屋。面谈、签合同都是由代理对象来做，只是房屋是由代理对象让真实对象来提供。
```Java
public class Test {
    public static void main(String[] args) {
        Renter realRenter = new RealRenter();
        Renter proxy = new ProxyRenter(realRenter);
        proxy.Renting();
    }
}
```

## 动态代理
&emsp;&emsp;相比于静态代理，动态代理使用更加广泛，因为在动态代理模式中，不需要我们自己来生成代理类，我们完全可以交给工具去生成代理类。动态代理一般有两种方式：**JDK动态代理**和**CGLIB动态代理**。

### JDK动态代理
&emsp;&emsp;在静态代理中，我们是在手动给每个真实对象（房东）分配一个代理对象（中介），当我们由很多个真实对象（房东）都要分配代理对象（中介）时，一个个去手动分配就很繁琐。而在动态代理中不需要我们去创建代理类，而是编写了一个动态处理器，真正的代理对象由JDK在运行时为我们动态创建，这样便大大简化了我们的开发任务。
```Java
public class JdkProxyHandler {
    //用来接收房东
    private Object realRenter;
    //通过构造方法传进来房东
    public JdkProxyHandler(Renter renter) {
        super();
        this.realRenter = renter;
    }
    //真实对象生成一个代理对象实例
    public Object getProxyInstance() {
    return Proxy.newProxyInstance(realRenter.getClass().getClassLoader(),
    realRenter.getClass().getInterfaces(), (proxy, method, args) -> {
         System.out.println("中介找人");
         // 房东自己提供房屋
         Object object = method.invoke(realRenter, args);
         System.out.println("与中介签合同");
         return object;
     });
}
}
```
&emsp;&emsp;上述的Proxy.newProxyInstance() 方法接收三个参数：第一个参数指定当前目标对象使用的类加载器,获取加载器的方法是固定的；第二个参数指定目标对象实现的接口的类型；第三个参数指定动态处理器，执行目标对象的方法时,会触发事件处理器的方法。
```Java
public class Test {
    //测试JDK动态代理结果
    public static void main(String[] args) {
        Renter realRenter = new RealRenter();
        // 创建一个代理对象实例
        Renter proxy = (Renter) new JdkProxyHandler(realStar).getProxyInstance();

        proxy.Renting();
    }
}
```
&emsp;&emsp;可以看出，任意传入一个真实对象（房东），JdkProxyHandler都可以创建一个对应的代理对象（中介）。
&emsp;&emsp;综上可以看出：相对于静态代理，JDK 动态代理大大减少了我们的开发任务，同时减少了对业务接口的依赖，降低了耦合度。JDK 动态代理是利用反射机制生成一个实现代理接口的匿名类，在调用具体方法前调用InvokeHandler 来处理。但是 JDK 动态代理有个缺憾，或者说特点：JDK 实现动态代理需要实现类通过接口定义业务方法。也就是说它始终无法摆脱仅支持 interface 代理的桎梏，这是它的设计所注定的。

### CGLIB 动态代理
&emsp;&emsp;由上面的分析可知，JDK 实现动态代理需要实现类通过接口定义业务方法，那对于没有接口的类，想要实现动态代理呢就需要 CGLIB 了。
&emsp;&emsp;CGLIB 采用了非常底层的字节码技术，其原理是通过字节码技术为一个类创建子类，并在子类中采用方法拦截的技术拦截所有父类方法的调用，顺势织入横切逻辑。但因为采用的是继承，所以不能对final修饰的类进行代理。如下是一个CGLIB代理类。
```Java
public class CglibProxyHandler implements MethodInterceptor {
    //维护目标对象
    private Object target;
    public Object getProxyInstance(final Object target) {
        this.target = target;
        // Enhancer类是CGLIB中的一个字节码增强器，它可以方便的对你想要处理的类进行扩展
        Enhancer enhancer = new Enhancer();
        // 将被代理的对象设置成父类
        enhancer.setSuperclass(this.target.getClass());
        // 回调方法，设置拦截器
        enhancer.setCallback(this);
        // 动态创建一个代理类
        return enhancer.create();
    }
    @Override
    public Object intercept(Object object, Method method, Object[] args,
    MethodProxy methodProxy) throws Throwable {
        System.out.println("中介找人");
        // 房东提供房屋
        Object result = methodProxy.invokeSuper(object, args);
        System.out.println("与中介签合同");
        return result;
    }
}
```
&emsp;&emsp;使用 CGLIB 需要实现 MethodInterceptor 接口，并重写intercept 方法，在该方法中对原始要执行的方法前后做增强处理。该类的代理对象可以使用代码中的字节码增强器来获取。
```Java
public class Test {
    //测试Cglib动态代理结果
    public static void main(String[] args) {
        Renter realRenter = new RealRenter();
        Renter proxy = (Renter) new CglibProxyHandler().getProxyInstance(realRenter);
        proxy.Renting();
    }
}
```
&emsp;&emsp;这个测试程序和 JDK 动态代理的逻辑一模一样，也可以看出，动态代理的两种方式，其实思路都是相同的，只是使用了不同的技术。
&emsp;&emsp;总结：CGLIB 创建的动态代理对象比 JDK 创建的动态代理对象的性能更高，但是 CGLIB 创建代理对象时所花费的时间却比 JDK 多得多。所以对于单例的对象，因为无需频繁创建对象，用 CGLIB 合适，反之使用JDK方式要更为合适一些。同时由于 CGLIB 由于是采用动态创建子类的方法，对于final修饰的方法无法进行代理。

### Spring AOP采用哪种代理方式
&emsp;&emsp;下列为Spring5中的部分源码：
```Java
public class DefaultAopProxyFactory implements AopProxyFactory, Serializable {

    @Override
    public AopProxy createAopProxy(AdvisedSupport config) throws AopConfigException {
        if (config.isOptimize() || config.isProxyTargetClass() || hasNoUserSuppliedProxyInterfaces(config)) {
            Class<?> targetClass = config.getTargetClass();
            if (targetClass == null) {
                throw new AopConfigException("TargetSource cannot determine target class: " + "Either an interface or a target is required for proxy creation.");
            }
            // 判断目标类是否是接口或者目标类是否Proxy类型，若是则使用JDK动态代理
            if (targetClass.isInterface() || Proxy.isProxyClass(targetClass)) {
                return new JdkDynamicAopProxy(config);
            }
            // 配置了使用CGLIB进行动态代理或者目标类没有接口，那么使用CGLIB的方式创建代理对象
            return new ObjenesisCglibAopProxy(config);
        }
        else {
            // 上面的三个方法没有一个为true，那使用JDK的提供的代理方式生成代理对象
            return new JdkDynamicAopProxy(config);
        }
    }
    //其他略……
}
```
&emsp;&emsp;从上述源码片段可以看出，是否使用 CGLIB 是在代码中进行判断的，判断条件是 `config.isOptimize()`、`config.isProxyTargetClass()` 和 `hasNoUserSuppliedProxyInterfaces(config)`。
其中，`config.isOptimize()` 与 `config.isProxyTargetClass()`默认返回都是 `false`，这种情况下判断结果就由`hasNoUserSuppliedProxyInterfaces(config)`的结果决定了。
也就是说，`hasNoUserSuppliedProxyInterfaces(config)` 就是在判断代理的对象是否有实现接口，有实现接口的话直接走 JDK 分支，即使用 JDK 的动态代理。

&emsp;&emsp;所以基本上 Spring AOP 中的代理使用逻辑可以总结如下：如果目标对象实现了接口，默认情况下会采用 JDK 的动态代理实现 AOP；如果目标对象没有实现了接口，则采用 CGLIB 库，Spring 会自动在 JDK 动态代理和 CGLIB 动态代理之间转换。
