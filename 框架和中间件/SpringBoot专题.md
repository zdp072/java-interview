## 什么是springboot
springboot是用来简化spring应用的初始搭建以及开发过程的。

## 为什么要用springboot
1. 独立运行spring项目（jar）
2. 内嵌Servlet容器
3. 提供starter简化maven配置
4. 自动配置spring bean
5. 准生产的应用监控
6. 无xml配置

## springboot的不足
因为自动装配spring bean的功能，我们可能无法知道，哪些bean被创建了，这时如果我们想要自定义一些bean可能存在冲突。

## springboot核心配置文件
application.yml：用于springboot项目的初始化配置
bootstrap.yml：由父ApplicationContext加载，比application优先加载，且bootstrap里面的属性不能被覆盖