# 为什么说 String 不可变的



## 代码验证

```java
String str = new String("abc");
System.out.println(str.hashCode());//96354
str += "a";
System.out.println(str.hashCode());//2987071
str += "b";
System.out.println(str.hashCode());//92599299
String str2="abcab";
System.out.println(str2.hashCode()+" "+str.hashCode());//92599299 92599299
```

如果String是不可变的，那么三次打印的hashCode()是不一样的；如果是可变的，那么三次打印的hasnCode()是相等的。

结果可以看出，我们修改String的内容，三次打印的hashCode()是不一样的，此时String的对象已经不是同一个对象了；如果String内容相同，打印的hashCode()是一样的，此时两个String我们可以看成是同一个对象。



## 源码验证

1. String类定义的时候就是final类型
2. String的长度是final
3. UID也是final
4. 同时里面的内容也是final不可修改的