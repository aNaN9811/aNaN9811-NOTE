# 什么是 support libary ，为什么要引入 support library ？



support libary 是以一个支持库，主要是因为安卓版本有很多，新版本的更新也快，每个版本都会有一个开发版本号
在进行安卓开发的时候，我们通常需要考虑，应该选择什么样的 API 级别进行开发？谷歌为此也给出了解决方案，我们在开发过程中可以给出三个设置项：minSdkVersion <= targetSdkVersion <= compileSdkVersion

运行时AndroidVersion < targetSdkVersion：使用Android系统版本API的接口

运行时 targetSdkVersion <= AndroidVersion：使用targetSdkVersion设置的系统版本的API的接口

支持库的主要作用就是解决向后兼容的问题：来让App运行在低版本手机上的时候能够使用新版本系统才有的特性。

当我们targetSdkVersion为 24 时，需要使用 API 28 的新增功能时，这时需要向后兼容，使用低版本的 API 兼容高版本的 API，而支持库就是这个作用，它会跟着每个新发布的 API 级别同步发布，所以这里支持库我们选择与 compileSdkVersion 一样的版本即可。
 support libary 只能保证我们针对24开发的APP在28上能正常运行，但运行效果不一定相同。

