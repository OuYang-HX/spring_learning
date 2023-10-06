# Spring概述
Spring 的主要作用就是为代码“解耦”，降低代码间的耦合度。就是让对象和对象（模块和模块）之间的关系不通过代码直接关联，而是通过配置来说明。减轻对项目模块之间的管理， 类和类之间的管理， 帮助开发人员创建对象，管理对象之间的关系。当需要修改某一模块时不会因为与其他模块有关联而需要一起修改。

# IoC

## 什么是IOC？
IoC是Spring提供的容器，它的全称是Inversion of Control，直译为控制反转，Spring框架中对象创建、赋值、管理工作都交由这个容器（即IoC）实现。

**控制**：即创建对象，对象的属性赋值，对象之间的关系管理。

**反转**：即把原来的开发人员管理，创建对象的权限转移给代码之外的容器实现。由容器代替开发人员管理对象。创建对象，给属性赋值。

## 为什么要用IoC

通常，业务模块在相互调用中需要实例化一个对象时，都是使用类的构造方法来new一个对象，这个过程需要程序员在代码中去作改动，维护起来会很麻烦。而IoC的理念即是将这个过程中交由Spring容器统一管理，自动“注入”。这样可以减少对代码的改动，降低业务对象之间的耦合度，实现**解耦合**。


<details>
<summary>在没有IoC之前，通常的Java组件协作过程</summary>

假定有一个在线商店项目，通过ProductService获取商品：
```java
public class ProductService {
    private HikariConfig config = new HikariConfig();
    private DataSource dataSource = new HikariDataSource(config);

    public Product getProduct(long productId) {
        try (Connection conn = dataSource.getConnection()) {
            ...
            return product;
        }
    }
}
```

为了从数据库查询商品，ProductService持有一个DataSource。为了实例化一个HikariDataSource，又不得不实例化一个HikariConfig。

现在，我们继续编写UserService获取用户：
```java
public class UserService {
    private HikariConfig config = new HikariConfig();
    private DataSource dataSource = new HikariDataSource(config);

    public User getUser(long userId) {
        try (Connection conn = dataSource.getConnection()) {
            ...
            return user;
        }
    }
}
```
因为UserService也需要访问数据库，因此，我们不得不也实例化一个HikariDataSource。

在处理用户购买的CartServlet中，我们需要实例化UserService和ProductService：
```java
public class CartServlet extends HttpServlet {
    private ProductService productService = new ProductService();
    private UserService userService = new UserService();

    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        long currentUserId = getFromCookie(req);
        User currentUser = userService.getUser(currentUserId);
        Product product = productService.getProduct(req.getParameter("productId"));
        cartService.addToCart(currentUser, product);
        ...
    }
}
```
类似的，在购买历史HistoryServlet中，也需要实例化UserService和ProductService：
```java
public class HistoryServlet extends HttpServlet {
    private ProductService productService = new ProductService();
    private UserService userService = new UserService();
}
```

上述每个组件都采用了一种简单的通过new创建实例并持有的方式。仔细观察，会发现以下缺点：

实例化一个组件其实很难，例如，ProductService和UserService要创建HikariDataSource，实际上需要读取配置，才能先实例化HikariConfig，再实例化HikariDataSource。

没有必要让ProductService和UserService分别创建DataSource实例，完全可以共享同一个DataSource，但谁负责创建DataSource，谁负责获取其他组件已经创建的DataSource，不好处理。类似的，CartServlet和HistoryServlet也应当共享ProductService实例和UserService实例，但也不好处理。

很多组件需要销毁以便释放资源，例如DataSource，但如果该组件被多个组件共享，如何确保它的使用方都已经全部被销毁？

随着更多的组件被引入，例如，商品评论，需要共享的组件写起来会更困难，这些组件的依赖关系会越来越复杂。

测试某个组件，例如ProductService，是复杂的，因为必须要在真实的数据库环境下执行。

从上面的例子可以看出，如果一个系统有大量的组件，其生命周期和相互之间的依赖关系如果由组件自身来维护，不但大大增加了系统的复杂度，而且会导致组件之间极为紧密的耦合，继而给测试和维护带来了极大的困难。

因此，核心问题是：

谁负责创建组件？
谁负责根据依赖关系组装组件？
销毁时，如何按依赖顺序正确销毁？
解决这一问题的核心方案就是IoC。

传统的应用程序中，控制权在程序本身，程序的控制流程完全由开发者控制，例如：

CartServlet创建了ProductService，在创建ProductService的过程中，又创建了DataSource组件。这种模式的缺点是，一个组件如果要使用另一个组件，必须先知道如何正确地创建它。
</details>

<details>
<summary>IoC方式</summary>

在IoC模式下，控制权发生了反转，即从应用程序转移到了IoC容器，所有组件不再由应用程序自己创建和配置，而是由IoC容器负责，这样，应用程序只需要直接使用已经创建好并且配置好的组件。为了能让组件在IoC容器中被“装配”出来，需要某种“注入”机制，例如，ProductService自己并不会创建DataSource，而是等待外部通过setDataSource()方法来注入一个DataSource：
```java
public class ProductService {
    private DataSource dataSource;

    public void setDataSource(DataSource dataSource) {
        this.dataSource = dataSource;
    }
}
```
不直接new一个DataSource，而是注入一个DataSource，这个小小的改动虽然简单，却带来了一系列好处：

ProductService不再关心如何创建DataSource，因此，不必编写读取数据库配置之类的代码；
DataSource实例被注入到ProductService，同样也可以注入到UserService，因此，共享一个组件非常简单；
测试ProductService更容易，因为注入的是DataSource，可以使用内存数据库，而不是真实的MySQL配置。
因此，IoC又称为依赖注入（DI：Dependency Injection），它解决了一个最主要的问题：将组件的创建+配置与组件的使用相分离，并且，由IoC容器负责管理组件的生命周期。

因为IoC容器要负责实例化所有的组件，因此，有必要告诉容器如何创建组件，以及各组件的依赖关系。一种最简单的配置是通过XML文件来实现，例如：
```xml
<beans>
    <bean id="dataSource" class="HikariDataSource" />
    <bean id="productService" class="ProductService">
        <property name="dataSource" ref="dataSource" />
    </bean>
    <bean id="userService" class="UserService">
        <property name="dataSource" ref="dataSource" />
    </bean>
</beans>
```
上述XML配置文件指示IoC容器创建3个JavaBean组件，并把id为dataSource的组件通过属性dataSource（即调用setDataSource()方法）注入到另外两个组件中。

在Spring的IoC容器中，我们把所有组件统称为JavaBean，即配置一个组件就是配置一个Bean。

依赖注入方式
我们从上面的代码可以看到，依赖注入可以通过set()方法实现。但依赖注入也可以通过构造方法实现。

很多Java类都具有带参数的构造方法，如果我们把ProductService改造为通过构造方法注入，那么实现代码如下：
```java
public class ProductService {
    private DataSource dataSource;

    public ProductService(DataSource dataSource) {
        this.dataSource = dataSource;
    }
}
```
Spring的IoC容器同时支持属性注入和构造方法注入，并允许混合使用。

无侵入容器
在设计上，Spring的IoC容器是一个高度可扩展的无侵入容器。所谓无侵入，是指应用程序的组件无需实现Spring的特定接口，或者说，组件根本不知道自己在Spring的容器中运行。这种无侵入的设计有以下好处：

应用程序组件既可以在Spring的IoC容器中运行，也可以自己编写代码自行组装配置；
测试的时候并不依赖Spring容器，可单独进行测试，大大提高了开发效率。
</details>

IOC创建对象的方式
1. 默认使用无参构造
2. 有参构造
- 下标赋值构造
- 通过类型创建，一般不建议使用
- 参数名
在配置文件加载的时候，容器中管理的对象就已经初始化了

spring配置
1. 别名
如果添加了别名，也可以通过别名获取对象
2. Bean配置
id bean的唯一标识符，相当于对象名
class bean对象对应的全限定名：包名+类型
name：也是别名，而且name可以同时取多个别名
3. import
一般用于团队开发，将多个bean合并到一起

依赖注入（DI）
1. 构造器注入
2. Set方式注入
- 依赖注入
    - 依赖：bean对象的创建依赖于容器
    - 注入：bean对象中的所有属性，都由容器注入


# DI

## 什么是DI
DI是IoC的技术实现机制，它的全称是Dependency Injection，直译为依赖注入，是实现IoC（控制反转）的一种方式。