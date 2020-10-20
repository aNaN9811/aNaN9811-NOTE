# 什么是Fragment？它和activity的关系



Fragment是依赖于Activity的，不能独立存在，Activity是Fragment的一个容器。

一个Activity里可以有多个Fragment。

一个Fragment可以被多个Activity重用。

Fragment有自己的生命周期，并能接收输入事件。

我们能在Activity运行时动态地添加或删除Fragment，UI的编写更加灵活。



**Fragment的使用能解决以下问题：**

模块化（Modularity）：我们不必把所有代码全部写在Activity中，而是把代码写在各自的Fragment中，以方便不同业务的UI可以分离出来。

可重用（Reusability）：多个Activity可以重用一个Fragment。

可适配（Adaptability）：根据硬件的屏幕尺寸、屏幕方向，能够方便地实现不同的布局，这样用户体验更好。

