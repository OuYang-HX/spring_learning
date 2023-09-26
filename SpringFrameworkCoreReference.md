# IoC容器

## IoC容器和beans的介绍

&emsp;&emsp;IoC全称Inversion of Control, 译为控制反转, 也叫依赖注入(Dependency Injection). 它是一个过程:在此过程中,对象只能通过构造函数的参数 工厂方法的参数 工厂方法返回或对象实例构造后设置的属性值来决定其依赖项(与之一起使用的其他对象). 容器会在创建bean时注入这些依赖. 这个过程通过使用类的直接构造或者如服务定位器模式的机制, 在根本上反转了bean对其依赖定位和实例化的控制. (因此称为控制反转)
&emsp;&emsp;Spring框架的IoC容器的包基础是org.springframework.beans和org.springframework.context. 顶级接口BeanFactory提供了一个能够管理所有类型对象的高级配置机制, 它的子接口ApplicationContext增加了:
- 易于和Spring AOP功能集成
- Message resource处理
- 事件发布
- Application层特定的context，例如在web应用中使用WebApplicationContext

简单来说, BeanFactory 提供了配置框架和基础功能, 而ApplicationContext 添加了更多特定的企业级功能.  ApplicationContext是 BeanFactory 的完整超集, 在本章中专门用于对 Spring 的 IoC 容器的描述

在 Spring 中, 对象构成了应用的支柱, 这些被Spring IoC 容器管理的对象集, 我们称之为beans. bean 是一个由 Spring IoC 容器实例化, 组装并管理的对象. 除此之外, bean 只是应用中众多普通对象中的一员, 容器使用的配置元数据反应了 beans 以及它们之间的依赖关系.


## 容器概述
org.springframework.context.ApplicationContext 接口表示Spring容器, 负责实例化, 配置和组装beans. 容器通过读取配置元数据获取有关要实例化, 配置和组装哪些对象的指令, 配置元数据以 XML, Java 注解或 Java 代码表示, 配置元数据表达了组成应用程序的对象以及这些对象之间的相互依赖关系. 最常用的定义配置元数据格式是XML, 在XML中可以指示容器使用Java注解或代码作为元数据格式, 只需提供少量XML配置声明来启用对这些添加的元数据格式的支持. 

Spring提供了ApplicationContext接口的多种实现, 在独立运行应用程序中通常创建ApplicationContext接口的ClassPathXmlApplicationContext或者FileSystemXmlApplicationContext实例. 
在大多数应用场景中, 实例化一个或多个IoC容器时并不需要公开业务代码. 例如, 在web应用场景下, 应用程序中web.xml文件上简单的8行左右的web模板就足够了(参考web应用程序的快速ApplicationContext实例化).

### 元数据配置
Spring IoC容器需要使用某种形式的配置元数据, 它们代表开发者告诉Spring容器要怎么实例化, 配置和组装程序中的对象.
**元数据配置格式**
- 基于XML
- 基于注解
- 基于Java
习惯上使用简单和直观的XML格式来提供配置元数据

Spring配置至少要有一个由容器管理的bean, 形式如下：
```xml
<beans>
    <bean .../>
    <bean ...>
        ...
    </beans>
</beans>
```
这些bean定义对应于
