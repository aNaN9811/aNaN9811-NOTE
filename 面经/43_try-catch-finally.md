# try_catch_finally

## 没有异常的try且有return

```java
static void Main(string[] args) {
    string result = GetStr();
    Console.WriteLine(result);
    Console.ReadLine();
}

public static string GetStr() {
    try {
        Console.WriteLine("走到：try");
        return "这里是try返回值";
    } catch (Exception e) {
        Console.WriteLine("走到：catch");
        return "这里是catch返回值";
    } finally {
        Console.WriteLine("走到：finally");
    }
    return "这里是方法底部返回值";// 此句编译会报错，因为上面有return了
}
```

![](./picture/try_catch_finally_1.png)

没有发生异常，然后没有走catch，执行顺序是try=>finally=>return；

一个还不确定的结果在GetStr方法中不会执行方法自己的return；

finally方法块都会执行

## try中出现异常且有return

```java
public static string GetStr() {
    try {
        int value = 0;
        Console.WriteLine("走到：try");
        var i = 1 / value;//这里会出错0不能被整除
        return "这里是try返回值";
    } catch (Exception e) {
        Console.WriteLine("走到：catch");
        return "这里是catch返回值";
    } finally {
        Console.WriteLine("走到：finally");
    }
    return "这里是方法底部返回值";
}
```

![](./picture/try_catch_finally_2.png)

这里在try发生了异常，然后没有正常返回，进入到了catch方法块：try=>catch=>finally=>return；

- 不管try有没有出错finally方法块都会被执行。【快记笔记，知识点。】
- 就算try和catch方法都有return，finally都会执行；
- 只要try或者catch return返回，try catch 之外的return都无效；（此处因为try先抛异常再return所以try的return没有执行到）

## 验证return的值

```java
public static string GetStr() {
    string str = "";
    try {
        str = "修改成了a";
        Console.WriteLine("走到：try");
        // return "这里是try返回值";
        return str;
    } catch (Exception e) {
        Console.WriteLine("走到：catch");
        return "这里是catch返回值";
    } finally {
        str = "修改成了b";
        Console.WriteLine("走到：finally");
    }
    return "这里是方法底部返回值";
}
```

![](./picture/try_catch_finally_3.png)

有异常还是老样子：执行顺序是try=>finally=>return；

但是我们在finally修改了str字符串，但是通过输出结果我们得到的还是a字符串，

所有我们得到结论：虽然finally方法会被执行但是，返回结果不会被改变，也就是如果finally是在return之后执行的那么他会把返回结果先保存起来，然后不管finally代码执行了什么，都不会影响到返回结果，等finally执行完成try再返回结果。

## try_catch_finally 中都有return

```java
public static String try_catch_finally() {
    try {
        return "一口一个大西瓜1";
    } catch (Exception e) {
        return "一口一个大西瓜2";
    } finally {
        return "一口一个大西瓜3";
    }
}
```

结果为：一口一个大西瓜3

## 多个重复try

那么我们可以写多个try｛｝try｛｝这样的语句吗？不行，会直接报错，其实这样写没有任何意义。

## try_finally 都对变量进行修改

```java
public static String try_catch_finally() {
	String CQNU = "重庆师范大学";
	try {
		return CQNU;
	} finally {
		CQNU = "重庆师范大学计算机信息科学学院";
	}
}
```
结果返回：重庆师范大学

这为什么返回了"重庆师范大学"？难道finally中的代码没有执行吗？随后我在finally中又打印了一遍CQNU变量，但值是改变了的。查阅资料后发现，返回值在try中被缓存了，后面的更改并不能修改到缓存了的返回值。但是在finally中写return是可以返回成功的。

```java
import java.util.*;

public class Main {

    static List<Integer> list = new ArrayList<>();

    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        System.out.println(try_catch_finally().toString());
    }

    public static List<Integer> try_catch_finally() {
        try {
            list.add(1);
            return list;
        } finally {
            list.add(2);
        }
    }
}
```

如果是 list 则，list 中还是存在 finally 中添加的元素，结果为：[1, 2]

## try中出现异常没有finally

```java
public static string GetStr() {
    try {
        Console.WriteLine("走到：try");
        int value = 0;
        int s = 1 / value;// 抛出异常
        return "这里是try返回值";
    } catch (Exception e) {
        Console.WriteLine("走到：catch");
        return "这里是catch返回值";
    }
    return "这里是方法底部返回值";
}
```

![](./picture/try_catch_finally_4.png)

- finally也不是必须的。
- 如果catch没有return 就会返回底部return方法。这是我们的常识。

## 主方法调用抛异常的方法

```java
static void Main(string[] args) {
    string result = "";
    try {
        result = GetStr();
    } catch (Exception e) {
        Console.WriteLine("主方法catch：");
    }
    Console.WriteLine(result);
    Console.ReadLine();
}

public static string GetStr() {
    try {
        Console.WriteLine("走到：try");
        int value = 0;
        int s = 1 / value;
        return "这里是try返回值";
    } finally {
        Console.WriteLine("走到：finally");
    }

    return "这里是方法底部返回值";
}
```

![](./picture/try_catch_finally_5.png)

try发生了异常，但是因为finally始终都会执行所有也会执行，然后异常被调用方法内的catch捕获执行顺序：try=>finally=>catch(主方法)

所有我们得到结果：

- try-finally可以运行
- try如果没有catch但是发生异常会向上找catch方法块来捕获。知道没有系统崩溃。

## 性能的影响 

try{ }部分和不加try/catch语句块的效率几乎一样, catch{}部分似乎需要100倍以上的时间 ,所以只要不把try{}catch{}作为你的程序的逻辑,这种设计就是合理的。

