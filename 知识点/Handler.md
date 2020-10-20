# Handler

Handler 其实是消息管理机制

所有代码都是 Handler

Lancher -> JVM -> ActivityThread.main

![Handler工作流程](E:\Android_知识点\picture\Handler工作流程.jpg)

![](E:\Android_知识点\picture\源码分析.jpg)

从子线程发消息

-> handler.send 

-> handler.enqueueMessage（把消息插入到消息队列）（优先级队列，单链表）

-> Looper.loop() 调用 messageQueue.next()（出队列）（主线程）

-> handler.dispatchMessage()

-> handler.handleMessage()

到主线程处理消息

整个过程就是 Message 在动的过程，也就是内存的共享，子线程到主线程，因为内存不分子线程和主线程





## MessageQueue

是一块内存的容器，不属于谁

MessageQueue 里有一个 Message，Message 里有 next 指针

优先级队列，有先后顺序，时间（执行时刻快的先执行），排序（插入排序，按执行时间排序）

永远取队列头部的Message去执行





## Looper

核心：构造函数，loop()，ThreadLocal

构造函数是私有的，所以创建 Looper 对象要调用 prepare() 方法来进行对象的初始化，是由 ActivityThread 来进行调用，preoare() 方法 set 了 ThreadLocal，该 ThreadLocal 是一个 static final 的变量所以只有一个，即一个线程只有一个 Looper 同时不可修改，Key 为 this 是唯一的 ThreadLocal，每个线程只有唯一的一个 ThreadLocalMap 也就是 ThreadLocal 是唯一的，保证了 Looper 的唯一

ThreadLocal 是一个线程的上下文存储变量，线程隔离

ThreadLocal 本身不存储数据，存储数据是因为里面有 ThreadLocalMap

每一个线程 Thread 里面有自己的 ThreadLocalMap 成员变量

ThreadLocalMap 里面有一个 Entry 的弱引用类，Key 为 ThreadLocal，Value 为保存的对象

Looper 构造函数里 new 了一个 MessageQueue，且是 final 确保了 Message 的唯一性





## 消息睡眠和唤醒机制

生产者--消费者设计模式

两个方面的阻塞：阻塞把整个线程阻塞

1. 消息没到执行时间，自动唤醒
2. 消息队列为空，无限等待状态，直到添加 message





## 面试

### 一个线程有几个 Handler？

可以有好几个

### 一个线程有几个Looper？如何保证？

一个，通过 ThreadLocal 保证，如果创建过 ThreadLocal 存储在 map 中则会抛出异常

### Handler 内存泄漏的原因？为什么其他的内部类没有说过有这个问题？

内部类持有了外部类的对象

生命周期问题：enqueueMessage 会引用 Handler，所以 Handler 是 enqueueMessage 的一部分，Handler 持有了 Activity

GC 可达性分析算法 JVM 不可回收

即外部类Activity中的内部类Handler被别的生命周期Looper持有了，导致该外部类Activity无法释放造成内存泄漏

### 为何主线程可以 new Handler？如果想要在子线程中new Handler 要做些什么准备？

主线程的 ActivityThread 已启动就执行了 Looper.prepare() 和 Looper.loop()

子线程要进行 Looper.prepare() 和 Looper.loop()

### 子线程中维护的Looper，消息队列无消息的时候的处理方案是什么？有什么用？

主线无消息的时候会阻塞，（主线程无法调用quit()，会报异常）子线程一定会调用 quit() 唤醒线程退出 Looper.loop()，同时清空消息队列的所有消息

### 既然可以存在多个 Handler 往 MessageQueue 中添加数据（发消息时各个 Handler 可能处于不同线程），那它内部是如何确保线程安全的？

锁

enqueueMessage：synchronize 内置锁（JVM自动完成），锁的是同一个对象的 MessageQueue，其中一个线程只有一个MessageQueue对象，即一个线程只有一个地方可以操作 Message

next()：取Message也是有加锁，确保线程安全，因为可以在新加一个Message需要立即执行而不是当前排序到的Message，所以当插入的时候无法取出当前队列中的 Message

### 我们使用 Message 时应该如何创建它？

享元设计模式（内存复用），message 消息处理完后重新初始化 message 构出一个新的队列，为了防止 OOM

### Looper死循环为什么不会导致应用卡死

ANR 是一定时间内没有处理完 message

MessageQueue 没消息会让自身进入阻塞，交出 CPU 执行权



## 同步屏障（需要立刻执行的消息）

往 MessageQueue 里发送的 Message 的 msg.target 设为 null，MessageQueue.next() 会轮询消息队列找到需要立刻处理的消息，找到后退出轮序然后处理该消息，处理完消息后移除同步屏障



## HandlerThread

是 Thread 的子类，就是一个线程，只是它在自己的线程里面帮我们创建了 Looper

1. 方便使用：方便初始化，方便获取线程 Looper
2. 保证了线程安全，Looper.prepare() 没执行完，即 Looper 可能还没创建就 getLooper() 会出错。解决方法是加锁，当 getLooper() 获取不到 Looper 会进入 wait() 释放锁，线程 prepare() 会拿到锁进行 Looper 的创建，然后重新唤醒 getLooper() 完全释放锁后才执行 getLooper()



## IntentService

Handler 的应用

service 处理后台耗时任务给子线程运行

处理完 message 后 service 自动停止，内存释放

只有一个 message

保证同一个线程执行 -- Looper，HandlerThread 绑定了一个 Looper

保证按顺序执行：消息队列