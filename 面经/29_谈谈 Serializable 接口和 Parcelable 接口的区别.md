# 谈谈 Serializable 接口和 Parcelable 接口的区别



Serializable（Java自带）：
Serializable是序列化的意思，表示**将一个对象转换成可存储或可传输的状态**。序列化后的对象**可以在网络上进行传输，也可以存储到本地。**

Parcelable（android 专用）：
除了Serializable之外，使用Parcelable也**可以实现相同的效果**，不过不同于将对象进行序列化，Parcelable方式的实现原理是**将一个完整的对象进行分解**，而**分解后的每一部分都是Intent所支持的数据类型，这样也就实现传递对象的功能了。**



## 使用情况

内存中使用Parcelable：
使用内存进行序列化传输的时候最好用Parcelable性能会更高，因为它是将一个完整的对象进行分解，而分解后的每一部分都是Intent支持的数据类型

持久化使用Serializable：
更趋向于使用二进制的方式进行序列化的操作，Serializable 在序列化的时候会产生大量的临时变量，这中间会频繁地引起GC的操作，如果在一个Activity往另一个Activity中传输对象使用Serializable会产生比较多的临时变量消耗性能，所以在程序运行的过程中需要序列化的操作最好使用 Parcelable，如果在持久化的时候，因为直接转为二进制的方式，占用空间会比 Parcelable 更低，在反序列化过程中，Serializable也不会因为对象地增减字段而反序列化失败，所以一般在持久化的时候建议使用 Serializable 会更好

