# Handler原理



## 概念

- ThreadLocal：当前线程存储的数据仅能从当前线程取出。
- MessageQueue：具有时间优先级的消息队列。
- Looper：轮询消息队列，看是否有新的消息到来。
- Handler：具体处理逻辑的地方，内存共享方案，对wait/notify这部分功能进行了Linux层的封装。
- Tread：动力。



## 流程

开始 handler.sendMessage()、handler.post() 生产  

--->  handler.sendMessage() 最终调用 MessageQueue.enqueueMessage()  消息入队列

--->  Thread  调用 Looper.prepare() 【主线程ActivityThread，AMS，已经 Looper.prepare() 和 Looper.loop()】

​		  --->  先 Looper.prepare() 【静态方法】

​		  --->  Looper.prepare() 通过 Looper 类中 final static ThreadLocal 保证一个唯一的  ThreadLocal  同时 ThreadLocal 的 key 是唯一的（ThreadLocal的hash值是唯一的，因为 static），接着 new 一个 Looper 实现线程的隔离，此时线程的上下文独一无二

​		  --->  再 Looper.loop() 【静态方法】通过 myLooper() 取出 ThreadLocol 中的 Looper，取出其中的 MessageQueue 接着死循环（MessageQueue    只有在 Looper 构造函数中 new 出来）

--->  接着 Thread 调用 Looper.loop() 死循环，当有 Message 则调用 MessageQueue.next() 消息出队列

--->  结束 handler.dispatchMessage()  回调 handler.handleCallback() 和 handler.handleMessage()

![](.\Picture/Handler_sendMessage.jpg)

MessageQueue.enqueueMessage() 和 MessageQueue.next() 都加了 synchronized 保证唯一生产者和消费者

handler.dispatchMessage() 责任链（RxJava）

handler.obtain()：使用了享元模式，在 handler.dispatchMessage() 处理完 Message 后，调用 Message.recycleUnchecked()，成为空的 Message，为了减少内存碎片，避免OOM，所以最好使用obtain()

内存共享：有一些对象可以复用，为了减少GC，避免内存抖动，这个时候使用享元模式



## 过程

1. 准备工作：创建Handler，如果是在子线程中创建，还需要调用Looper#prepare()，在Handler的构造函数中，会绑定其中的Looper和MessageQueue。
2. 发送消息：创建消息，使用Handler发送。
3. 进入MessageQueue：因为Handler中绑定着消息队列，所以Message很自然的被放进消息队列。
4. Looper轮询消息队列：Looper是一个死循环，一直观察有没有新的消息到来，之后从Message取出绑定的Handler，最后调用Handler中的处理逻辑，这一切都发生在Looper循环的线程，这也是Handler能够在指定线程处理任务的原因。

Message只是一个内存，因为Handler是new出来的，所以MessageQueue管理的是内存



##  Looper在主线程中死循环为什么没有导致界面的卡死？

1. 导致卡死的是在Ui线程中执行耗时操作导致界面出现掉帧，甚至ANR，Looper.loop()这个操作本身不会导致这个情况。
2. 有人可能会说，我在点击事件中设置死循环会导致界面卡死，同样都是死循环，不都一样的吗？Looper会在没有消息的时候阻塞当前线程，释放CPU资源，等到有消息到来的时候，再唤醒主线程。
3. App进程中是需要死循环的，如果循环结束的话，App进程就结束了。



## 注意点

子线程Looper.loop()什么时候结束

在子线程创建Looper经常会有内存泄漏，因为，Looper没有释放，需要在结束的时候调用quit()，它会调用removeMessageLocked()，将所有的Message清空，接着唤醒线程执行Looper.loop()，因为为空所以Looper.loop()结束