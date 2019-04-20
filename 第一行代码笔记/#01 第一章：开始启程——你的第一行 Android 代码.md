# 第一章：开始启程——你的第一行 Android 代码

### Android架构：

Linux内核层：为各种硬件提供了底层的驱动

系统运行库层：通过一些C/C++库来为Android提供了主要的特性支持；还有运行时库，主要提供了一些核心库，能够允许开发者使用Java语言来编写Android应用，另外，Android运行时库还包含了Dalvik虚拟机（5.0系统之后改为ART运行环境），它使得每一个Android应用都能运行在独立的进程当中，并且拥有一个自己的Dalvik虚拟机实例。

应用框架层：	提供了构建应用程序是可能用到的各种API

应用层：所有安装在手机上的应用程序

### 四大组件

活动（Activity）：看到的东西

服务（Service）：后台运行

广播接收器（Broadcast Receiver）：允许你的应用接收来自各处的广播消息，比如电话、短信等，你的应用也可以向外发出广播消息

内容提供器（Content Provider）：为应用程序提供了可能，比如读取系统电话薄中的联系人

### Android应用开发特色

1、四大组件

2、丰富的系统控件

3、SQLite数据库

4、强大的多媒体

5、地理位置定位

### Android所需的工具

JDK：JDK是Java语言的软件开发工具包，它包含了java的运行环境、工具集合、基础类库等内容

Android SDK：Android SDK是谷歌提供的Android开发工具包，在开发Android程序是，我们需要通过引入该工具包来使用Android的相关API

Android Studio

### 分析第一个Android程序

```java
//HelloWorld/app/src/res/AndroidManifest.xml
<activity android:name=".HelloWorldActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
</activity>
```

##### 这段代码表示对HelloWorldActivity这个活动进行注册，没有在AndroidManifest.xml里注册的活动是不能使用的。

其中intent-filter里面的两行代码非常重要，两行代码表示HelloWorldActivity是这个项目的主活动，在手机上点击应用图标，首先启动的就是这个活动。

```java
//HelloWorld/app/src/main/java/com.example.helloworld/HelloWorldActivity
public class HelloWorldActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.hello_world_layout);
    }
}
```

HelloWorldActivity是继承自AppCompatActivity的，这是一种向下兼容的Activity，可以将Activity在各个系统版本中增加的特性功能最低兼容到Android2.1系统。

Activity是Android系统提供的一个活动基类，我们项目中所有的活动都必须继承它或者它的子类才能拥有活动的特性（AppCompatActivity是Activity的子类）

Android程序的设计讲究逻辑和视图的分离，所以不推荐在活动中直接编写界面，而应该在布局文件中编写界面，然后再活动中引入进来。//onCreate（）方法的第二行调用了setContentView（）方法，就是这个方法引入了hello_world_layout布局。

布局文件都定义在res/layout目录下

```java
//HelloWorld/app/src/main/res/layout/hello_world_layout.xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/hello_world_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="com.example.helloworld.HelloWorldActivity">
//TextView是Android系统提供的一个控件，用于在布局中显示文字
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!" />
</RelativeLayout>
```

![](E:\Android笔记\第一章笔记图片\1-1.png)

drawable开头的文件夹都是用来存放图片的

mipmap开头的文件夹都是用来存放应用图标的

values开头的文件夹都是用来放字符串、样式、颜色等配置的

layout文件夹是用来放布局文件的

```java
//res/valuer/strings.xml
<resources>
    <string name="app_name">HelloWorld</string>
</resources>
```

在代码中通过 R.string.app_name 可以获得该字符串的引用

在XML中通过 @string/app_name 可以获得该字符串的引用

如果引用的是图片资源，就可以替将 string 换成 drawable ，以此类推

```java
//AndroidManifest.xml
<application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"//应用图标
        android:label="@string/app_name"//应用名称
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".HelloWorldActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
```

### 详解  build.gradle 文件

Android Studio 是采用 Gradle 来构建项目的，是一个项目构建工具，它使用了一种基于 Groovy 的领域特定语言（DSL）来声明项目的设置，摒弃了基于XML（如 Ant 和 Maven）的各种繁琐配置。

##### 外层目录下的 build.gradle 

```java
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.2.0'//声明了一个 Gradle 插件

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

allprojects {
    repositories {
        jcenter()
    }
}
```

两处闭包都声明了 jcenter() 这行配置，它是一个代码托管仓库，很多的 Android 开元项目都会选择将代码托管到 jcenter 上，声明了这行配置后，我么那就可以在项目上引用任何 jcenter 上的开源项目

##### app目录下的 build.gradle

```java
/*
com.android.application：表示是一个应用程序模块，可以直接运行
com.android.library：表示是一个库模块，依附于别的应用程序模块来运行
*/
apply plugin: 'com.android.application'//表示这是一个应用程序模块

android {
    compileSdkVersion 24//用于指定项目的编译版本，24指的是使用 Android7.0 系统中的 SDK 编译
    buildToolsVersion "28.0.3"//用于指定项目构建工具的版本
    
    defaultConfig {
        applicationId "com.example.helloworld"//指定项目的包名，后面再定义都修改包名在此处修改
        minSdkVersion 15//用于指定项目最低兼容的 Android 系统版本
        targetSdkVersion 24//指定的值表示你在该目标版本上已经做过充分测试，系统将会为你的应用启用一些新特性和功能
        versionCode 1//用于指定项目版本号
        versionName "1.0"//用于指定项目的版本名
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
    //buildTypes 闭包用于指定生成安装文件的相关配置，通常会有两个子闭包，debug 和 release。debug闭包用于指定生成测试版安装文件的配置，release闭包用于指定生成正式版安装文件的配置。
    buildTypes {
        release {
            minifyEnabled false//用于指定是否对项目的代码进行混淆，true表示混淆，false表示不混淆
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'//用于指定混淆规则的文件；proguard-android.txt 是在 Android SDK 目录下，里面是所有项目通用的混淆规则；proguard-rules.pro 是在当前目录下李曼可以编写当前项目特有的混淆规则
        }
    }
}

//dependencies 闭包可以指定当前项目所有的依赖关系，通常 Android Studio 的项目有三种依赖方式：本地依赖，库依赖和远程依赖
//本地依赖可以对本地的 Jar 包或目录添加依赖关系，库依赖可以对项目中的库模块添加依赖关系，远程依赖则可以对jcenter库上的开源项目添加依赖关系
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])//本地依赖声明，表示将libs木留下所有的.jar后缀的文件都添加到项目的构建路径当中
        
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
        
    //远程依赖说明，com.android.support 是域名部分，用语和其他公司的库做区分；appcompat-v7 是组名称，用语	和同一个公司中不同的库做区分；24.2.1 是版本号，用于和同一个库不同的版本做区分
    compile 'com.android.support:appcompat-v7:24.2.1'
    testCompile 'junit:junit:4.12'//用于生命测试用到的例库
}


//库依赖声明没有用到，他的基本格式是 compile project 后面加上要依赖库名称，比如一个库模块的名字叫 heloer ，那么添加这个库的依赖关系只需要加入 compile project(':helper')这句声明即可
```

通过 Android Studio 直接运行项目生成的都是测试版安装软件

## 掌握日志工具的使用

### 使用 Android  的日志工具 Log

Android中的工具类是 Log (android.util.Log)

Log.v()：打印最琐碎、意义最小的日志信息

Log.d()：打印一些调试信息，调试程序和分析问题应该有帮助的

Log.i()：打印一些比较重要的数据，分析用户行为数据

Log.w()：打印一些警告信息，warn

Log.e()：打印程序中的错误信息，error

Log.d（）方法中传入了两个参数：第一个参数是 tag ，一般传入当前的类名就好，主要用于对打印信息进行过滤；第二参数是 msg ，即想打印的具体内容