# 广播和EventBus的区别



## 结构上

广播是四大组件之一，EvenBus是开源框架



## 执行上

广播不能直接执行耗时操作，如果超过10秒，会导致ANR（Application Not Responding）应用程序无响应



## 资源消耗上

广播非常消耗资源，EvenBus 非常轻量



## 数据传递上

广播容易获取Context和Intent

EvenBus修改线程非常方便，只需要修改注解就可以了

广播可以跨进程，EvenBus不可以



## 总结

系统级的事件通过广播来通知，比方说网络的变化，短信的发送和接受的状态

线程间的通信，除了比较难获取的Context和Intent地方，其它用EvenBus