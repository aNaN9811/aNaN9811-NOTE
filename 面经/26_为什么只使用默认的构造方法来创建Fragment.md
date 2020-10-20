# 为什么只使用默认的构造方法来创建Fragment



## 系统UI的策略

一般情况下，没有这个空构造器也不会出什么大问题。但当系统内存不足的情况下，app处于后台条件下，内存会被系统回收，这时用户将app切换到前台，系统会重新将之前回收的内容实例化回来。

这个过程是Android系统通过两个方法来实现的:

onSaveInstantceState();

onRestoreInstantceState();

onSaveInstantceState是系统要回收该界面(Activity、Fragment)时调用的方法，用于保存该(Activity、Fragment)的实例变量到外存。

onRestoreInstantceState是系统恢复该(Activity、Fragment)时调用的方法，用于恢复之前被回收的(Activity、Fragment)实例。



## 为什么Fragment需要这么一个空构造器。

当app回到前台时，系统会调用Activity 的 onRestoreInstantceState();

在实例化得时候，会重新创建一个Fragment，通过 Fragment.instantiate() 方法创建：

```java
public static Fragment instantiate(Context context, String fname, @Nullable Bundle args) {
        try {
            Class<?> clazz = sClassMap.get(fname);
            if (clazz == null) {
                // Class not found in the cache, see if it's real, and try to add it
                clazz = context.getClassLoader().loadClass(fname);
                sClassMap.put(fname, clazz);
            }
            Fragment f = (Fragment)clazz.newInstance();
            if (args != null) {
                args.setClassLoader(f.getClass().getClassLoader());
                f.mArguments = args;
            }
            return f;
        } catch (ClassNotFoundException e) {
            throw new InstantiationException("Unable to instantiate fragment " + fname
                    + ": make sure class name exists, is public, and has an"
                    + " empty constructor that is public", e);
        } catch (java.lang.InstantiationException e) {
            throw new InstantiationException("Unable to instantiate fragment " + fname
                    + ": make sure class name exists, is public, and has an"
                    + " empty constructor that is public", e);
        } catch (IllegalAccessException e) {
            throw new InstantiationException("Unable to instantiate fragment " + fname
                    + ": make sure class name exists, is public, and has an"
                    + " empty constructor that is public", e);
        }
    }
```

最终调用该Fragment的Class类的newInstance方法，无参构造器。如果有要恢复的参数，会再次赋给mArguments。这点很重要，我之前一直在纠结如果有个无参构造器，那参数初始化的问题咋办。其实**这个无参构造器系统只是用它来实例化而已！**



## 总结：

当系统因为内存紧张杀死非前台进程(并非真正的杀死)，然后我们将系统杀掉的非前台app带回前台，如果有Fragment，那么会因为restore造成Fragment需要通过反射实例对象，从而将之前save状态还原，而这个时候需要Fragment有public的空构造器。

