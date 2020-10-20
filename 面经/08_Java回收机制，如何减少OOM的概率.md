# Java回收机制，如何减少OOM的概率



## Java回收机制

Java的回收机制主要是通过判断对象的存活情况来进行的，主要采用的是可达性分析算法（以我们对象能不能到达GC Roots来作为一个标准）

在Java, 可作为**GC Roots**的对象包括:

1. 方法区: 类静态属性的对象；
2. 方法区: 常量的对象；
3. 虚拟机栈(本地变量表)中的对象；
4. 本地方法栈JNI(Native方法)中的对象。

1和2是存在于一个公共区域，3和4是局部变量（本地变量）

3和4方法执行时，对象都存在于GC Roots中，会跟对象的生命周期一起，方法执行完后会剔除出GC Roots，剔除后就很容易被GC回收

3和4方法执行完后运行可达性分析算法，一般会释放，因为引用链中的对象均不再存在于GC Roots中，除非引用链一个引用对象的关系存在于GC Roots中，那么这些对象都不会被回收

```java
public class ExampleUnitTest {

    /**
     * 可达性分析算法
     */
    Object o =new Object();
    static Object GCRoot1 =new Object(); //GC Roots
    final  static Object GCRoot2 =new Object();//GC Roots
    public void method() {
        //可达
        Object object1 = GCRoot1; //=不是赋值，在对象中是引用，传递的是右边对象的地址
        Object object2 = object1;
        Object object3 = object1;
        Object object4 = object3;
    }
    public void king(){
        //不可达（方法运行完后可回收）
        Object object5 = o;//o不是GCRoots
        Object object6 = object5;
        Object object7 = object5;
    }
    //本地变量表中引用的对象
    public void stack(){
        Object ostack =new Object();    //本地变量表的对象
        Object object9 = ostack;
        //以上object9 在方法没有(运行完)出栈前都是可达的
    }
}
```



## 如何减少OOM的概率

1. 尽可能少的发生内存泄漏
2. 尽可能不在循环中申请内存
3. 尽可能不在调用次数多的函数中申请内存