# Java中强引用、软引用、弱引用以及虚引用

- 强引用：Object obj=new Object();
- 软引用（SoftReference）：不会受GC影响，只有当内存不足时回收，做缓存
- 弱引用（WeakReference）：GC到来时回收，处理Activity的内存泄漏
- 虚引用（PhantomReference）：GC回收时可得到一个通知，虚引用是不会单独使用的，构造虚引用需要把虚引用对象（即普通对象）用引用队列管理，在引用队列中，该对象在队列中为null，直到GC后对虚引用回收的信息绑定进虚引用的对象，检测GC是否正常工作