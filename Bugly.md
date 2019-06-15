# Bugly 热更新



## 前言

热更新能力是Bugly为解决开发者紧急修复线上bug，而无需重新发版让用户无感知就能把问题修复的一项能力。Bugly目前采用**微信Tinker**的开源方案，开发者只需要集成我们提供的SDK就可以实现自动下载补丁包、合成、并应用补丁的功能，我们也提供了热更新管理后台让开发者对每个版本补丁进行管理。

### Tinker是什么：

Tinker是微信官方的Android热补丁解决方案，它支持动态下发代码、So库以及资源，让应用能够在不需要重新安装的情况下实现更新。当然，你也可以使用Tinker来更新你的插件。

### 为什么使用Tinker：  

当前市面的热补丁方案有很多，其中比较出名的有阿里的AndFix、美团的Robust以及QZone的超级补丁方案。但它们都存在无法解决的问题，Tinker我们不仅可以用做bugfix,甚至可以替代功能的发布。Tinker已运行在微信的数亿Android设备上，那么为什么你不使用Tinker呢？



![img](https:////upload-images.jianshu.io/upload_images/4029470-7aa035427289a886.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/415/format/webp)

Tinker热更新的优势

**接入Bugly热更新  接入了热更新就已经接入了异常上报了，所有就不需要在进行异常上报的接入了。**



## 第一步：添加插件依赖  

工程根目录下“build.gradle”文件中添加：

```java
buildscript{repositories{  

​      jcenter()   

 }dependencies{

// tinkersupport插件, 其中lastest.release指拉取最新版本，也可以指定明确版本号，例如1.0.4

​     classpath"com.tencent.bugly:tinker-support:1.1.5"

}

}
```

注意：自**tinkersupport 1.0.3**版本起无需再配tinker插件的classpath。

> 版本对应关系:
>
> tinker-support 1.1.2 对应 tinker 1.9.6
>
> tinker-support 1.1.1 对应 tinker 1.9.1
>
> tinker-support 1.0.9 对应 tinker 1.9.0
>
> tinker-support 1.0.8 对应 tinker 1.7.11
>
> tinker-support 1.0.7 对应 tinker 1.7.9
>
> tinker-support 1.0.4 对应 tinker 1.7.7
>
> tinker-support 1.0.3 对应 tinker 1.7.6
>
> tinker-support 1.0.2 对应 tinker 1.7.5（需配置tinker插件的classpath）



## 第二步：集成SDK  

### 根目录下 gradle 的配置

```java
buildscript {
    repositories {
        google()
        jcenter()
        
    }
    dependencies {
        //注意：gradle 版本改为 3.2.1
        classpath 'com.android.tools.build:gradle:3.2.1'

        // tinkersupport插件, 其中lastest.release指拉取最新版本，也可以指定明确版本号，例如1.0.4
        classpath "com.tencent.bugly:tinker-support:1.1.5"
    }
}
```



### **app/gradle配置**

在app module的“build.gradle”文件中添加（示例配置）：

```java
android {      
    
    defaultConfig {          
        ndk {
            //设置支持的SO库架构
            abiFilters'armeabi'//, 'x86', 'armeabi-v7a', 'x86_64', 'arm64-v8a'}     
        }
    }
    
    signingConfigs {
        release {
            storeFile file('')
            storePassword ''
            keyAlias = ''
            keyPassword ''
        }
    }
    
    buildTypes {
        debug {
            minifyEnabled true
            signingConfig signingConfigs.release
        }
        release {
            zipAlignEnabled true    //Zipalign优化
            shrinkResources false   //删除无用资源
            minifyEnabled true    	//是否开启混淆规则，参数 true 为开启混淆规则
            signingConfig signingConfigs.release
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }

    dependencies{

        implementation 'com.android.support:multidex:1.0.1'
        // 多dex配置
        //注释掉原有bugly的仓库
        //compile 'com.tencent.bugly:crashreport:latest.release'//其中latest.release指代最新版本号，也可以指定明确的版本号，例如1.3.4
        implementation 'com.tencent.bugly:crashreport_upgrade:1.3.6'
        // 指定tinker依赖版本（注：应用升级1.3.5版本起，不再内置tinker）
        implementation 'com.tencent.tinker:tinker-android-lib:1.9.9'
        implementation 'com.tencent.bugly:nativecrashreport:3.6.0'
        
        //注意！！！使用 Glide 的话需要使用以下版本
        implementation 'com.github.bumptech.glide:glide:4.5.0'
        annotationProcessor 'com.github.bumptech.glide:compiler:4.5.0'

}
```

后续更新升级SDK时，只需变更配置脚本中的版本号即可。

> 注意： 升级SDK已经集成crash上报功能，已经集成Bugly的用户需要注释掉原来Bugly的jcenter库； 已经配置过符号表的Bugly用户保留原有符号表配置； Bugly SDK（2.1.5及以上版本）已经将Java Crash和Native Crash捕获功能分开，如果想使用NDK库，需要配置： compile 'com.tencent.bugly:nativecrashreport:latest.release'

在app module的“build.gradle”文件中添加：

```java
// 依赖插件脚本
applyfrom:'tinker-support.gradle'
```



### tinker-support.gradle内容如下所示（示例配置）：

> 注：您需要在同级目录下创建tinker-support.gradle这个文件哦。

```java
apply plugin: 'com.tencent.bugly.tinker-support'

def bakPath = file("${buildDir}/bakApk/")

/**
 * 此处填写每次构建生成的基准包目录
 */
def baseApkDir = "app-0208-15-10-00"

/**
 * 对于插件各参数的详细解析请参考
 */
tinkerSupport {

    // 开启tinker-support插件，默认值true
    enable = true

    // 指定归档目录，默认值当前module的子目录tinker
    autoBackupApkDir = "${bakPath}"

    // 是否启用覆盖tinkerPatch配置功能，默认值false
    // 开启后tinkerPatch配置不生效，即无需添加tinkerPatch
    overrideTinkerPatchConfiguration = true

    // 编译补丁包时，必需指定基线版本的apk，默认值为空
    // 如果为空，则表示不是进行补丁包的编译
    // @{link tinkerPatch.oldApk }
    baseApk = "${bakPath}/${baseApkDir}/app-release.apk"

    // 对应tinker插件applyMapping
    baseApkProguardMapping = "${bakPath}/${baseApkDir}/app-release-mapping.txt"

    // 对应tinker插件applyResourceMapping
    baseApkResourceMapping = "${bakPath}/${baseApkDir}/app-release-R.txt"

    // 构建基准包和补丁包都要指定不同的tinkerId，并且必须保证唯一性
    //基准版：版本-base
    //补丁：版本-patch
    tinkerId = "1.0.1-base"

    // 构建多渠道补丁时使用
    // buildAllFlavorsDir = "${bakPath}/${baseApkDir}"

    // 是否启用加固模式，默认为false.(tinker-spport 1.0.7起支持）
    isProtectedApp = false

    // 是否开启反射Application模式
    enableProxyApplication = false

    // 是否支持新增非export的Activity（注意：设置为true才能修改AndroidManifest文件）
    supportHotplugComponent = true

}

/**
 * 一般来说,我们无需对下面的参数做任何的修改
 * 对于各参数的详细介绍请参考:
 * https://github.com/Tencent/tinker/wiki/Tinker-%E6%8E%A5%E5%85%A5%E6%8C%87%E5%8D%97
 */
tinkerPatch {
    //oldApk ="${bakPath}/${appName}/app-release.apk"
    ignoreWarning = false
    useSign = true
    dex {
        dexMode = "jar"
        pattern = ["classes*.dex"]
        loader = []
    }
    lib {
        pattern = ["lib/*/*.so"]
    }

    res {
        pattern = ["res/*", "r/*", "assets/*", "resources.arsc", "AndroidManifest.xml"]
        ignoreChange = []
        largeModSize = 100
    }

    packageConfig {
    }
    sevenZip {
        zipArtifact = "com.tencent.mm:SevenZip:1.1.10"
//        path = "/usr/local/bin/7za"
    }
    buildConfig {
        keepDexApply = false
        //tinkerId = "1.0.1-base"
        //applyMapping = "${bakPath}/${appName}/app-release-mapping.txt" //  可选，设置mapping文件，建议保持旧apk的proguard混淆方式
        //applyResourceMapping = "${bakPath}/${appName}/app-release-R.txt" // 可选，设置R.txt文件，通过旧apk文件保持ResId的分配
    }
}
```



### enableProxyApplication = false 的情况

> 这是Tinker推荐的接入方式，一定程度上会增加接入成本，但具有更好的兼容性。

集成Bugly升级SDK之后，我们需要按照以下方式自定义ApplicationLike来实现Application的代码（以下是示例）：



#### 自定义Application

```java
public class SampleApplication extends TinkerApplication {
    public SampleApplication() {
        super(ShareConstants.TINKER_ENABLE_ALL, "xxx.xxx.SampleApplicationLike",
                "com.tencent.tinker.loader.TinkerLoader", false);
    }
}
```

> 注意：**这个类集成TinkerApplication类，这里面不做任何操作，所有Application的代码都会放到ApplicationLike继承类当中**
> 参数解析
> 参数1：tinkerFlags 表示Tinker支持的类型 dex only、library only or all suuport，default: TINKER_ENABLE_ALL
> 参数2：delegateClassName Application代理类 这里填写你自定义的ApplicationLike
> 参数3：loaderClassName Tinker的加载器，使用默认即可
> 参数4：tinkerLoadVerifyFlag 加载dex或者lib是否验证md5，默认为false

我们需要您将以前的Applicaton配置为继承TinkerApplication的类：

![配置基准包的tinkerId](https://bugly.qq.com/docs/img/hotfix/android/replace_application.png?v=20180709165613)



#### 自定义ApplicationLike

```java
public class SampleApplicationLike extends DefaultApplicationLike {

    public static final String TAG = "Tinker.SampleApplicationLike";

    public SampleApplicationLike(Application application, int tinkerFlags,
            boolean tinkerLoadVerifyFlag, long applicationStartElapsedTime,
            long applicationStartMillisTime, Intent tinkerResultIntent) {
        super(application, tinkerFlags, tinkerLoadVerifyFlag, applicationStartElapsedTime, applicationStartMillisTime, tinkerResultIntent);
    }


    @Override
    public void onCreate() {
        super.onCreate();
        // 这里实现SDK初始化，appId替换成你的在Bugly平台申请的appId
        // 调试时，将第三个参数改为true
        Bugly.init(getApplication(), "900029763", false);
    }


    @TargetApi(Build.VERSION_CODES.ICE_CREAM_SANDWICH)
    @Override
    public void onBaseContextAttached(Context base) {
        super.onBaseContextAttached(base);
        // you must install multiDex whatever tinker is installed!
        MultiDex.install(base);

        // 安装tinker
        // TinkerManager.installTinker(this); 替换成下面Bugly提供的方法
        Beta.installTinker(this);
    }

    @TargetApi(Build.VERSION_CODES.ICE_CREAM_SANDWICH)
    public void registerActivityLifecycleCallback(Application.ActivityLifecycleCallbacks callbacks) {
        getApplication().registerActivityLifecycleCallbacks(callbacks);
    }

}
```

> 注意：tinker需要你开启MultiDex,你需要在dependencies中进行配置`compile "com.android.support:multidex:1.0.1"`才可以使用MultiDex.install方法； SampleApplicationLike这个类是Application的代理类，以前所有在Application的实现必须要全部拷贝到这里，在`onCreate`方法调用SDK的初始化方法，在`onBaseContextAttached`中调用`Beta.installTinker(this);`。



### enableProxyApplication = true 的情况

```java
public class MyApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        // 这里实现SDK初始化，appId替换成你的在Bugly平台申请的appId
        // 调试时，将第三个参数改为true
        Bugly.init(this, "900029763", false);
    }

    @Override
    protected void attachBaseContext(Context base) {
        super.attachBaseContext(base);
        // you must install multiDex whatever tinker is installed!
        MultiDex.install(base);


        // 安装tinker
        Beta.installTinker();
    }

}
```

> 注：无须你改造Application，主要是为了降低接入成本，我们插件会动态替换AndroidMinifest文件中的Application为我们定义好用于反射真实Application的类（需要您接入**SDK 1.2.2版本** 和 **插件版本 1.0.3**以上）。



## 第四步：AndroidManifest.xml配置

**在AndroidMainfest.xml中进行以下配置：**

**1. 权限配置**

```xml
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.READ_LOGS" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

**2. Activity配置**

```xml
<activity
    android:name="com.tencent.bugly.beta.ui.BetaActivity"
    android:configChanges="keyboardHidden|orientation|screenSize|locale"
    android:theme="@android:style/Theme.Translucent" />
```

**3. 配置FileProvider**

注意：如果您想兼容Android N或者以上的设备，必须要在AndroidManifest.xml文件中配置FileProvider来访问共享路径的文件。

```xml
 <provider
    android:name="android.support.v4.content.FileProvider"
    android:authorities="${applicationId}.fileProvider"
    android:exported="false"
    android:grantUriPermissions="true">
    <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/provider_paths"/>
</provider>
```

如果你使用的第三方库也配置了同样的FileProvider, 可以通过继承FileProvider类来解决合并冲突的问题，示例如下：

```xml
<provider
    android:name=".utils.BuglyFileProvider"
    android:authorities="${applicationId}.fileProvider"
    android:exported="false"
    android:grantUriPermissions="true"
    tools:replace="name,authorities,exported,grantUriPermissions">
    <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/provider_paths"
        tools:replace="name,resource"/>
</provider>
```

这里要注意一下，FileProvider类是在support-v4包中的，检查你的工程是否引入该类库。

在res目录新建xml文件夹，创建provider_paths.xml文件如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<paths xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- /storage/emulated/0/Download/${applicationId}/.beta/apk-->
    <external-path name="beta_external_path" path="Download/"/>
    <!--/storage/emulated/0/Android/data/${applicationId}/files/apk/-->
    <external-path name="beta_external_files_path" path="Android/data/"/>
</paths>
```

这里配置的两个外部存储路径是升级SDK下载的文件可能存在的路径，一定要按照上面格式配置，不然可能会出现错误。

**注：1.3.1及以上版本，可以不用进行以上配置，aar已经在AndroidManifest配置了，并且包含了对应的资源文件。**



## 第五步：混淆配置

为了避免混淆SDK，在Proguard混淆文件中增加以下配置：

```java
-dontwarn com.tencent.bugly.**
-keep public class com.tencent.bugly.**{*;}
# tinker混淆规则
-dontwarn com.tencent.tinker.**
-keep class com.tencent.tinker.** { *; }
```

如果你使用了support-v4包，你还需要配置以下混淆规则：

```java
 -keep class android.support.**{*;}
```

```java
//如果使用了 Glide
-keep public class * implements com.bumptech.glide.module.GlideModule
-keep public class * extends com.bumptech.glide.AppGlideModule
-keep public enum com.bumptech.glide.load.resource.bitmap.ImageHeaderParser$** {
  **[] $VALUES;
  public *;
}
```

到这里为止 Bugly热更新就已经完成了，下面就是如何使用了，我们需要验证热更新的能力：

**注意tinker无法使用热更新修复的情况：**

由于原理与系统限制，Tinker有以下已知问题：

Tinker不支持修改AndroidManifest.xml，Tinker不支持新增四大组件；

由于Google Play的开发者条款限制，不建议在GP渠道动态更新代码；

在Android N上，补丁对应用启动时间有轻微的影响；

不支持部分三星android-21机型，加载补丁时会主动抛出"TinkerRuntimeException:checkDexInstall failed"；

对于资源替换，不支持修改remoteView。例如transition动画，notification icon以及桌面图标。



## 第六步：验证Bugly热更新 必须在联网的情况下才可以使用热更新



### 普通打包  



#### 1、编译基准包  



**配置基准包的tinkerId （注意这里的base版本必须联网上报 才可以上传补丁包）**

**设置你的基准版本号 最好是和app version对应**



![img](https:////upload-images.jianshu.io/upload_images/4029470-95bd49610d8a5437.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

Bugly基线版本



**执行assembleRelease编译生成基准包：** 

**在android studio的右边选择gradle找到我们接入热更新的model 进入build中选择生成的基准包类型：这里可以是Release和Debug版本** 



![img](https:////upload-images.jianshu.io/upload_images/4029470-f65681e9de657974.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/379/format/webp)

生成基准包



**这个会在build/outputs/bakApk路径下生成每次编译的基准包、混淆配置文件、资源Id文件，如下图所示：**





![img](https:////upload-images.jianshu.io/upload_images/4029470-64647219ab1e80b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/344/format/webp)

生成的基准Apk目录



**这里面有的app-release.apk就是我们需要上线的基线版本，请注意保存线上发布版本的基准apk包、mapping文件、R.txt文件，如果线上版本有bug，就可以借助我们tinker-support插件进行补丁包的生成。**

**如果你没有开启混淆是不会有mapping.txt文件的，需要在Moudle的build.gradle文件中开启混淆**

![img](https:////upload-images.jianshu.io/upload_images/4029470-09502d906eb0ec62.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/647/format/webp)

开启基线混淆



如何生成签名文件这里就不介绍了。



#### **2、对基线版本的bug修复**

上面生成了base包了，那就需要去生成patch包了。

基准包的代码：

![img](https:////upload-images.jianshu.io/upload_images/4029470-98feb42103fea6cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/809/format/webp)

base包的代码



补丁包（patch）的代码：

![img](https:////upload-images.jianshu.io/upload_images/4029470-240cfc77f61375d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/864/format/webp)

pacth代码



#### 3、根据基线版本生成补丁包

这里需要注意的：**基准包目录必须是你上次生成的base包**，注意这里是按照时间命名的，检查是否正确，tinkerId 需要生成patch包必须使用 tinkerId="patch-base版本号对应"

![img](https:////upload-images.jianshu.io/upload_images/4029470-a64d7ab07daa5c8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/672/format/webp)

patch补丁生成配置



**执行构建补丁包的task**

![img](https:////upload-images.jianshu.io/upload_images/4029470-b780164ec7ec64b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/413/format/webp)

patch补丁包的task



如果你要生成不同编译环境的补丁包，只需要执行TinkerSupport插件生成的task，比如buildTinkerPatchRelease就能生成release编译环境的补丁包。

生成的补丁包在**build/outputs/patch**目录下，其中7zip.apk就是我们需要使用的补丁包：

![img](https:////upload-images.jianshu.io/upload_images/4029470-ab5840f13e69cce3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/340/format/webp)

补丁包目录



打开补丁包可以看到里面会有一个YAPATCH.MF，这里面包含了我们是从base版本到patch版本：以及app的version信息

![img](https:////upload-images.jianshu.io/upload_images/4029470-e7e5e53f305c116b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/935/format/webp)

补丁包



#### 4、上传补丁包到平台

![img](https:////upload-images.jianshu.io/upload_images/4029470-d881c2ae500820d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



**进入到对应appid的app中，选择应用升级，热更新，发布新补丁。**

发布patch版本，选择补丁文件之后会自动从补丁包的YAPATCH.MF去寻找base版本以及app版本，注意这里的base版本必须联网上报（必须在联网的情况下启动app），才可以上传补丁成功。如果没有



![img](https:////upload-images.jianshu.io/upload_images/4029470-38385f1fe34ea95c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/484/format/webp)

上传补丁包

上传了补丁包可以选择下发的范围：

开发设备：仅仅针对设置了开发设备。

利用代码：Bugly.setIsDevelopmentDevice(this, true);设置为开发者设备。

全量设备：针对所有的用户。

自定义：可以设置下发范围:随机下发设置的版本数量，还可以设置android版本范围，仅仅在这个范围内才会下发并且生效。

下发补丁之后：

![img](https:////upload-images.jianshu.io/upload_images/4029470-0e8102d847695593.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/999/format/webp)



下发补丁的撤回

里面包括了激活的用户数量以及下发的数量，在操作过程中我们可能对于上个补丁不太满意，我们需要发布一个新的补丁，bugly提供了2中操作。

停止下发：点击之后将不会在下发bugly补丁，但是之前已经下载并且激活的不会删除掉补丁。

撤回：点击之后将停止下发补丁，并且之前已经下载并且激活的用户将会删除掉补丁包，并且恢复到基准包的版本。

**检查patch包是否生效：**

**基准包执行效果图：**

![img](https:////upload-images.jianshu.io/upload_images/4029470-bead66cfddf6d44a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/475/format/webp)

基准包效果图



修复之后的效果图：

![img](https:////upload-images.jianshu.io/upload_images/4029470-667ddbcbd4a627af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/415/format/webp)

补丁合成效果图



不能立即生效，需要吧app至于后台一段时间才能生效。

如果你的手机root的话，可以在data/data/app包名目录下有一个tinker目录，里面就有对于的patch补丁包下载在里面。



![img](https:////upload-images.jianshu.io/upload_images/4029470-e911bbcc3ef56a47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/998/format/webp)

补丁包生效统计

如果用户以及补丁下载成功以及激活的都会有统计的。

热更新加固：

之前的tinker只有1.7.5之前支持加固，tinker 1.7.8 可以通过 isProtectedApp 开启加固支持，这种模式仅仅可以使用在加固应用中。

isProtectedApp=true  

**加固厂商**                         **测试**

腾讯云·乐固                  Tested

爱加密                           Tested

梆梆加固                        Tested

360加固暂不支持，需要等待新版本发布

其他请自行测试，只要满足下面规则的都可以支持



## 注意小坑

build.gradel 的版本，tinkerid 是否有改，混淆规则是否开启，glide 的版本，AndroidManifest.xml 的 Application 是否更改，手机是否联网，enableProxyApplication 的参数设置，appid 是否修改为自己的，tinker、bugly 的版本问题

