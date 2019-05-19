## 为什么重写了equals方法还要重写hashcode方法
object是所有类的父类，object中的hashCode和equals是怎样的？
原生的hashCode的值是根据内存地址换算出来的一个值。
原生的equals方法是通过判断对象的内存地址是否一致。

在我们的业务系统中判断对象有时候需要的不是一种严格意义上的相等，而是一种业务上的对象相等。在这种情况下，原生的equals方法就不能满足我们的需求了。所以这个时候我们需要重写equals方法，来满足我们业务系统上的需求。

因为相等的对象必须具有相等的散列码（hashCode），所以重写了equals方法的话我们必须要重写hashCode方法。

## 解决hash冲突的方法
1. 链地址法（hashMap做法）
2. 再hash法：发生冲突的时候使用另外一个hash函数计算，直到不发生冲突
3. 建立公共溢出区：将哈希表分为公共表和溢出表，当溢出发生时，将所有溢出数据统一放到溢出区。

## Java中异常与错误的区别?
Exception和Error都是Throwable类的子类。
Exception用于用户程序需要捕获的异常条件。
Error定义了用户程序不可预见的异常。


## Java中的两种异常是什么?它们之间的区别?
Java有两种类型的异常:checked(检查)与unchecked(未检查)异常. 
checked异常必须通过方法或构造函数的throws子句显式声明. 
unchecked异常可能会在方法或构造函数执行时被抛出, 它们不需要要在方法或构造函数中声明throws子句. 

## finally和finalize有什么区别
finally：不管是否抛出异常语句块都会被执行, 它通常用于释放程序持有的资源. 
finalize：是Object类中的一个protected方法, 当一个对象被gc回收前它会被jvm调用.

## OverLoading重载和Overriding重写有什么区别
OverLoading重载
多个同名方法，具有不同的参数个数或参数类型。各方法返回类型不一定相同。

Overriding重写
子类可以继承父类的方法，如需要修改则用重写。

## ==和equals有什么区别
“==”和“equals”的作用是相同的，他们都是比较两个对象的引用的。
我们可以看一下Object中是如何实现equals方法的：

```
public boolean equals(Object obj) {
    return (this == obj);
}
```
源码中equals()方法返回的就是使用==比较两个对象的结果。

二者区别在哪？
“==”是操作符，equals是超类Object中的方法，这是最基本的区别。
子类可以通过重写equals方法而实现不同的功能。
String因为重写了equals方法，从比较引用地址变成了比较引用内容。
