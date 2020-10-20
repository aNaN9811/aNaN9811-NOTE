# 什么是ANR_如何避免ANR



## 概念

ANR(Application Not responding)，是指应用程序未响应，Android系统对于一 些事件需要在一定的时间范围内完成，如果超过预定时间能未能得到有效响应或 者响应时间过长，都会造成ANR



## 如何避免ANR

1. 主线程尽量只做UI相关的操作，避免耗时操作，例如文件的IO操作，网络操作，过度复杂的UI绘制等
2. 避免主线程和工作线程发生锁的竞争，减少系统耗时Binder的调用，谨慎使用 SharePrefersence（尽量apply方法不要commit，因为apply为异步提交，commit为原子提交及同步提交，若commit时比较耗时会产生ANR），注意主线程执行provider query操作

总之，尽可能减少主线程的负载，让其空闲待命，以期可随时响应用户的 操作