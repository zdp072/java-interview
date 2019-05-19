## SpringMVC的请求处理流程

1. 客户端发来一个http请求
2. DispatcherServlet接受到请求后，凭借请求Url和参数找到对应的处理器
3. 从HandleMapping中得到请求对应的处理器后，它将请求分派给这个处理器，处理器根据请求的信息执行相应的业务逻辑。
4. 处理器完成业务逻辑的处理后，将返回一个ModelAndView给DispatcherServlet，ModelAndView中包含视图对象，视图的解析工作通过ViewResolver来完成。
5. 得到真实的视图对象后，DispatcherServlet将请求分派给View对象，由其完成Model数据的渲染工作。
6. 最终客户端得到返回的响应，这可能是一个普通的HTML页面或者是一个Excel表格。


## 什么是Spring IoC
Ioc - inverse of control 控制反转

在没有引入Ioc容器之前，如果对象A依赖于对象B，那么对象A在初始化或者运行到某一点的时候，自己必须主动去创建对象B，这时控制权在自己手上。

引入Ioc容器之后，这种情形就完全改变了，当对象A运行到需要对象B的时候，Ioc容器会主动创建一个对象B注入到对象A需要的地方。

Ioc中最基本的技术就是反射编程，通俗的来讲就是根据给出的类名（字符串）来动态的生成对象。

[http://www.cnblogs.com/DebugLZQ/archive/2013/06/05/3107957.html](http://www.cnblogs.com/DebugLZQ/archive/2013/06/05/3107957.html)

## 什么是Spring Aop
在运行时，动态地将代码切入到类的指定位置上编程的思想就是面向切面编程（AOP）。使用AOP技术，可以将一些系统性相关的编程工作，提取出来独立实现，然后通过AOP切入进系统。从而避免在代码中混入过多的和业务无关的逻辑，比如：权限管理、事务管理、日志记录等。使用AOP技术可以达到将关注点分离的效果。

Spring的Aop为动态代理模式，实现的技术为：JDK提供的动态代理和CGLIB（字节码增强技术），都是生成一个代理对象。
JDK动态被代理类实现同一个接口，只有接口中的方法才能够被代理。
CGLIB字节码生成技术，其实就是继承被代理类，然后重写被代理的方法，插入我们自己的代码。

[http://www.cnblogs.com/songanwei/p/9417343.html](http://www.cnblogs.com/songanwei/p/9417343.html)


## spring bean的生命周期
(创建 -> 初始化 -> 销毁)
1. 实例化bean对象(通过构造方法或者工厂方法)
2. 设置对象属性(setter等)（依赖注入）
3. 如果Bean实现了BeanNameAware接口，工厂调用Bean的setBeanName()方法传递Bean的ID。
4. 如果Bean实现了BeanFactoryAware接口，工厂调用setBeanFactory()方法传入工厂自身
5. 将Bean实例传递给Bean的前置处理器的postProcessBeforeInitialization(Object bean, String beanname)方法
6. 调用Bean的初始化方法
7. 将Bean实例传递给Bean的后置处理器的postProcessAfterInitialization(Object bean, String beanname)方法
8. 使用Bean
9. 容器关闭之前，调用Bean的销毁方法

bean的前置、后置处理器，是为了对bean的一个增强
https://blog.csdn.net/w_linux/article/details/80086950