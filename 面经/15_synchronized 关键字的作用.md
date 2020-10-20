# synchronized 关键字的作用



synchronized 关键字可以在多线程环境下用来作为线程安全的同步锁。



## synchronized关键字主要有以下三种用法：

1. 修饰代码块：锁指定的对象
2. 修饰成员方法：锁this
3. 修饰静态方法：锁Class对象