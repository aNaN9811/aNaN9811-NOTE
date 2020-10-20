# String 中"=="与 equals() 的区别

- “==“比较的是内存中存放的位置
- equals()比较的是字符序列

```java
String str1 = "a" + "b"+ "c";
String str2 = "abc";
String str3 = new String("abc");
System.out.println(str1 == str2);//true
System.out.println(str1 == str3);//false
System.out.println(str2 == str3);//false
System.out.println(str1.equals(str2));//true
System.out.println(str2.equals(str3));//true
System.out.println(str1.equals(str3));//true
System.out.println(str1.hashCode()+" "+str2.hashCode()+" "+str3.hashCode());//相等
```

str1和str2内存地址相同，在常量池；str3在堆内存中所以内存地址不同

hashCode()调用的都是堆内存中的方法，所以如果堆内存中存放相同的数据，则hashCode()相同

![](.\picture\equals.jpg)