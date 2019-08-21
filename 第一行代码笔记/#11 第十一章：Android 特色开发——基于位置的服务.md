#  第十一章：Android 特色开发——基于位置的服务





## 基于位置的服务简介

基于位置的服务简称 LBS，有了 Android 系统作为载体，我们可以利用定位出的位置进行许多丰富多彩的操作

首先要清楚，基于位置服务所围绕的核心就是要先确定出用户所在的位置。通常有两种技术方式可以实现：一种是通过 GPS 定位，一种是通过网路定位。

GPS 定位的工作原理是基于手机内置的 GPS 硬件直接和卫星交互来获取当前的经纬度信息，这种定位方式精确度非常高，但缺点是只能在室外使用，室内基本无法接收到卫星的信号。

网络定位的工作原理是根据手机当前网络附近的三个基站进行测速，一次计算出手机和每个基站之间的距离，再通过三角定位确定出一个大概位置，这种定位方式精确度一般，但有点是在室内室外都可以使用。

Android 对于这两种定位方式都提供了相应的 API 支持，但国内 Google 的网络服务在国内无法使用，GPS 虽不需要网络，又只能在室外才可以使用，室内会出现无法定位的问题，因此我们可以使用国内第三方公司的 SDK，有百度、高德，本章我们学习百度在 LBS 的功能





## 申请 API Key

想要在自己的应用程序里使用百度的 LBS 功能，首先必须申请一个 API Key，你得拥有一个百度账号才能进行申请

有了百度账号后我们就可以申请成为一名百度开发者，打开 http://developer.baidu.com/user/reg 

接着访问 http://lbsyun.baidu.com/apiconsole/key ，然后同意百度开发者协议，创建应用就可以申请 API Key 了，应用名称随便填，应用类型选择 Android SDK，启用服务保持默认即可

SHA1 指的是打开程序时所用签名文件的 SHA1 指纹，可以通过 Android Studio 右侧工具栏 Gradle -> 项目名 -> :app -> Tasks -> android，双击 signingReport 就可以找到 SHA1 的字段





## 使用百度定位

新建 LBSTest 项目



### 准备 LBS SDK

先下载百度 LBS 开方平台的 SDK，下载地址：http://lbsyun.baidu.com/sdk/download 勾选基础地图和定位功能这两个 SDK

解压后会有一个 libs 目录，libs 目录下的内容又分为两部分，BaiduLBS_Android.jar 这个文件是 Java 层要使用到的，其中他子目录下的 so 文件是 Native 层要用到的。so 文件使用 C/C++ 语言进行编写，然后再用 NDK 编译出来的

把 Jar 包放在 app模块 libs 目录下是用来存放所有的 Jar 包

在 src/main 目录下右击 -> New -> Directory，创建一个 jniLibs 的目录，这里就是专门用来存放 so 文件的（即压缩包里其他所有目录）

虽然 app/build.gradle 默认将所有以 .jar 结尾的文件添加到当前项目的引用中

```java
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
	...
}
```

由于我们是直接将 Jar 包复制到 libs 目录下的，并没有修改 gradle 文件，因此不会弹出 Sync Now，所以记得点击 Android Studio 顶部工具栏的 Sync 按钮，不然项目将无法使用到 Jar 包中提供的任何接口

![](E:\Android笔记\第十一章笔记图片\微信截图_20190423000006.jpg)

![](E:\Android笔记\第十一章笔记图片\微信截图_20190423015610.jpg)

点击 Sync 按钮之后，libs 目录下的 jar 文件就会多出一个向右的箭头，这就表示项目已经能引用到这些 Jar 包了

![](E:\Android笔记\第十一章笔记图片\微信截图_20190423001028.jpg)



### 确定自己位置的经纬度

修改 activity_main.xml

```java
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/position_text_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

</LinearLayout>
```

只有一个 TextView 用于稍后显示当前位置的经纬度信息

修改 AndroidManifest.xml

```java
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.materialtest">

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />


    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        //这个标签的 android:name 部分是固定的，必须是 com.baidu.lbsapi.API_KEY，android:value 则应该填入之前申请到的 API Key
        <meta-data
            android:name="com.baidu.lbsapi.API_KEY"
            android:value="TbC00YbrR4GhxMAgbIs2ZCuUXkT1kV4R" />
        
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        
        <service android:name="com.baidu.location.f" android:enabled="true"
            android:process=":remote">
        </service>
        
    </application>

</manifest>
```

修改 MainActivity

```java
public class MainActivity extends AppCompatActivity {

    public LocationClient mLocationClient;

    private TextView positionText;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //首先在 onCreate() 方法中创建出一个 LocationClient 的实例，LocationClient 的构建函数接受一个 Context 参数，这里调用 getApplicationContext() 方法来获取一个全局的 Context 参数并传入
        mLocationClient = new LocationClient(getApplicationContext());
        //然后调用 LocationClient 的 registerLocationListener() 方法来注册一个定位监听器，当获取到位置信息的时候，就会回调这个定位监听器
        mLocationClient.registerLocationListener(new MyLocationListener());
        setContentView(R.layout.activity_main);
        positionText = findViewById(R.id.position_text_view);
        //为了在运行时一次性申请 3 个权限，首先创建一个空的 List 集合，然后依次判断这 3 个权限有没有被授权，如果没有被授权，就添加到 List 集合中，最后将 List 转换成数组，再调用 ActivityCompat.requestPermission() 方法一次性申请
        List<String> permissionList = new ArrayList<>();
        if (ContextCompat.checkSelfPermission(MainActivity.this, Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED) {
            permissionList.add(Manifest.permission.ACCESS_FINE_LOCATION);
        }
        if (ContextCompat.checkSelfPermission(MainActivity.this, Manifest.permission.READ_PHONE_STATE) != PackageManager.PERMISSION_GRANTED) {
            permissionList.add(Manifest.permission.READ_PHONE_STATE);
        }
        if (ContextCompat.checkSelfPermission(MainActivity.this, Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED) {
            permissionList.add(Manifest.permission.WRITE_EXTERNAL_STORAGE);
        }
        if (!permissionList.isEmpty()) {
            String[] permissions = permissionList.toArray(new String[permissionList.size()]);
        } else {
            requestLocation();
        }
    }

    //调用了 LocationClient 的 start() 方法就能开始定位了，定位结果会回调到我们前面注册的监听器当中，也就是 MyLocationListener
    private void requestLocation() {
        mLocationClient.start();
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
        switch (requestCode) {
            case 1:
                if (grantResults.length > 0) {
                    //通过一个循环将申请的每个权限都进行了判断，如果有任何一个权限被拒绝，那么就直接调用 finish() 方法关闭当前程序，只有当所有权限都被用户同意了，才会调用 requestLocation() 方法开始地理位置定位
                    for (int result : grantResults) {
                        if (result != PackageManager.PERMISSION_GRANTED) {
                            Toast.makeText(this, "必须同意所有权限才能使用本程序", Toast.LENGTH_SHORT).show();
                            finish();
                            return;
                        }
                    }
                    requestLocation();
                } else {
                    Toast.makeText(this, "发生未知错误", Toast.LENGTH_SHORT).show();
                    finish();
                }
                break;
            default:
        }
    }

    public class MyLocationListener implements BDLocationListener {

        @Override
        public void onReceiveLocation(final BDLocation location) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    StringBuilder currentPosition = new StringBuilder();
                    //这里通过 BDLocation 的 getLatitude() 方法获取当前位置的纬度
                    currentPosition.append("纬度；").append(location.getLatitude()).append("\n");
                    //通过 getLongitude() 方法获取当前的经度
                    currentPosition.append("经线：").append(location.getLongitude()).append("\n");
                    currentPosition.append("定位方式：");
                    //通过 getLocType() 获取当前的定位方式
                    if (location.getLocType() == BDLocation.TypeGpsLocation) {
                        currentPosition.append("GPS");
                    } else if (location.getLocType() == BDLocation.TypeNetWorkLocation) {
                        currentPosition.append("网络");
                    }
                    //最终将结果组装成一个字符串显示到 TextView 上面
                    positionText.setText(currentPosition);
                }
            });
        }

//        @Override
//        public void onConnectHotSpotMessage(String s, int i) {
//
//        }

    }

}
```

不过在默认的情况下，调用 LocationClient 的 start() 方法只会定位一次，如果我们正在快速移动中，怎样才能实时更新当前的位置呢？百度 LBS SDK 提供了一些列的设置方法来允许我们更改默认的行为，修改 MainActivity

```java
public class MainActivity extends AppCompatActivity {

	...

    private void requestLocation() {
        //增加了一个 initLocation() 方法
        initLocation();
        mLocationClient.start();
    }
    
    //在 initLocation() 方法中我们创建了一个 LocationClientOption 对象，然后调用它的 setScanSpan() 方法来设置更新的间隔，这里传入 5000 表示每 5 秒回更新一下当前的位置
    private void initLocation() {
        LocationClientOption option = new LocationClientOption();
        option.setScanSpan(5000);
        mLocationClient.setLocOption(option);
    }
    
    @Override
    protected void onDestroy() {
        super.onDestroy();
        //最后要记得在活动被销毁的时候一定要调用 LocationClient 的 stop() 方法来停止定位，不然程序会持续在后台不停地进行定位，从而严重消耗手机的电量
        mLocationClient.stop();
    }
    
	...

}
```



### 选择定位模式

上一小节中我们使用的一直是网络定位来获取到手机当前的位置信息，GPS 定位功能必须要由用户主动去启用才行，不然任何程序都无法使用 GPS 来获取到手机当前的位置信息。我们需要在手机中开启位置信息才可以使用 GPS 来获取到手机当前的位置信息

我们可以在 initLocation() 方法中对百度 LBS SDK 的定位模式进行指定，一共有三种模式可选：Hight_Accuracy、Battery_Saving 和 Device_Sensors

Hight_Accuracy：表示高精度模式，会在 GPS 信号正常的情况下优先使用 GPS 定位，在无法接收 GPS 信号的时候使用网络定位

Battery_Saving：表示节电模式，只会使用网络进行定位

Device_Sensors：表示传感器模式，只会使用 GPS 进行定位

Hight_Accuracy 是默认模式



修改 MainActivity 的 initLocation() 方法

```java
private void initLocation() {
    LocationClientOption option = new LocationClientOption();
    option.setScanSpan(50);
    //这里调用了 setLocationMode() 方法来将定位模式指定成传感器模式，也就是说只能使用 GPS 进行定位
    option.setLocationMode(LocationClientOption.LocationMode.Device_Sensors);
    mLocationClient.setLocOption(option);
}
```



### 看得懂的位置信息

百度 LBS SDK 提供了很好的支持，我们只需要进行一些简单的接口调用就能得到当前未知子各种丰富的地址信息

修改 MainActivity

```java
public class MainActivity extends AppCompatActivity {

	...

    private void initLocation() {
        LocationClientOption option = new LocationClientOption();
        option.setScanSpan(50);
        //调用了 LocationClientOption 的 setIsNeedAddress() 方法，并传入 true，这就表示我们需要获取当前位置详细的地址信息
        option.setIsNeedAddress(true);
        mLocationClient.setLocOption(option);
    }
    
	...

    public class MyLocationListener implements BDLocationListener {

        @Override
        public void onReceiveLocation(final BDLocation location) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    StringBuilder currentPosition = new StringBuilder();
                    currentPosition.append("纬度；").append(location.getLatitude()).append("\n");
                    currentPosition.append("经线：").append(location.getLongitude()).append("\n");
                    //接下来在 onReceiveLocation() 方法就可以获取到各种丰富的地址信息，调用 getCountry() 等方法就可以得到当前所在的国家，以此类推
                    currentPosition.append("国家：").append(location.getCountry()).append("\n");
                    currentPosition.append("省：").append(location.getProvince()).append("\n");
                    currentPosition.append("市：").append(location.getCity()).append("\n");
                    currentPosition.append("区：").append(location.getDistrict()).append("\n");
                    currentPosition.append("街道：").append(location.getStreet()).append("\n");
                    currentPosition.append("定位方式：");
                    if (location.getLocType() == BDLocation.TypeGpsLocation) {
                        currentPosition.append("GPS");
                    } else if (location.getLocType() == BDLocation.TypeNetWorkLocation) {
                        currentPosition.append("网络");
                    }
                    positionText.setText(currentPosition);
                }
            });
        }

		...

    }

}
```

有一点需要注意，由于获取地址信息一定需要用到网络，因此即使我们将定位模式指定成了 Device_Sensors，也会自动开启网络定位功能





## 使用百度地图



### 让地图显示出来

修改 activity_main.xml

```java
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/position_text_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        //之前用于显示定位信息的 TextView 现在暂时用不到了，我们将它的 visibility 属性指定成 gone，让它在界面上隐藏起来
        android:visibility="gone" />
    
    //在布局文件中新放置了一个 MapView 控件，并让它填满整个屏幕，这个 MapView 是由百度提供的自定义控件，所以在使用它的时候要将完整的报名加上
    <com.baidu.mapapi.map.MapView
        android:id="@+id/bmapView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:clickable="true" />

</LinearLayout>
```

修改 MainActivity





### 移动到我的位置































## Git 时间——版本控制工具的高级用法

打开 Git Bash 并进入到 LBSTest 项目的根目录，然后执行提交操作：

```java
git init
git add .
git commit -m "First Commit"
```



### 分支用法

分支是版本控制工具中比较高级且比较重要的一个概念，它主要的作用就是在现有代码的基础上开辟一个分叉口，使得代码可以在主干线和分支线上同时进行开发，且相互之间不会影响

当版本出现问题的时候如果不建立分支的话，你会非常头疼。举个例子，比如你的公司开发了一个软件，推出了 1.0 版本，但是领导马上给你提出新的需求让你投入到 1.1 版本的开发工作当中，过不久发现 1.0 版本有几个重大 bug ，你要立刻修复这些 bug，并重新发布 1.0 版本，但这个时候你会发现你已经根本无法修改这些 bug 了，因为现在 1.1 版本已开发到一半了，如果在现有的基础上修复这些 bug，那么更新的 1.0 版本将会带有一半 1.1 版本的功能。现在你进退两难，但是如果你使用了分支的话，就完全不会存在这个让人头疼的问题了，你只需要在发布 1.0 版本的时候建立一个分支，然后在主干线上继续开发 1.1 版本的功能，当 1.0 版本上发现任何 bug 的时候，就在分支线上进行修改，然后发布新的 1.0 版本，并记得将修改后的代码合并到主干线上。这样的话，不仅可以轻松解决 1.0 版本存在的 bug，而且保证了主干线上的代码也已经修复了这些 bug，当 1.1 版本发布的时候就不会有同样的 bug 存在了

分支的英文名是 branch ，如果想要查看当前的版本库中有哪些分支，可以使用 git branch 这个命令，由于 LBSTest 项目中还没有创建过任何分支，因此只有一个 master 分支存在，这也就是前面所说的主干线

创建一个分支

```java
git branch version1.0
```

再次输入 git branch 检查，发现有一个叫做 version1.0 的分支出现，master 分支前面有一个 “ * ”，说明目前我们的代码还是在 master 分支上的，切换到 version1.0 上只需要使用 checkout 命令即可

```java
git checkout version1.0
```

再次输入 git branch 来进行检查，可以看到我们已经成功把代码切换到 version1.0 这个分支上了

需要注意的是，在 version1.0 分支上修改并提交的代码将不会影响到 master 分支，同样的，在 master 分支上修改并提交的代码也不会影响到 version1.0 分支，因此如果我们在 version1.0 分支上修复了一个 bug，在 master 分支上这个 bug 仍然存在，这是将修改的代码一行行复制到 master 分支上显然不是一种聪明的做法，最好的办法是使用 merge 命令来完成合并操作，如下所示：

```java
git checkout master
git merge version1.0
```

当不需要分支的时候，可以使用如下命令将这个分支删掉：

```java
git branch -D version1.0
```



### 与远程版本库协作

比如现在有一个远程版本库的 Git 地址是 https://github.com/example/test.git ，可以使用以下命令将代码下载到本地

```java
git clone https://github.com/example/test.git 
```

之后你在这份代码的基础上进行了一些修改和提交，接下来借助 push 命令来把本地修改的内容同步到远程版本库上

```java
git push origin master
```

其中 origin 部分指定的是远程版本库的 Git 地址，master 部分指定的是同步到哪一个分支上，上述命令就完成了将本地代码同步到 http://github.com/example/test.git 这个版本库的 master 分支上的功能

Git 提供了两种命令来完成将远程版本库上的修改同步到本地的方法，分别是 fetch 和 pull，fetch 的语法规则和 push 是差不多的

```java
git fetch origin master
```

执行这个命令后，就会将远程版本库上的代码同步到本地，不过同步下来的代码并不会合并到任何分支上去，而是会存放到一个 origin/master 分支上，这时我们可以通过 diff 命令来查看远程版本库上到底修改了哪些东西

```java
git diff origin/master
```

之后再调用 merge 命令将 origin/master 分支上的修改合并到主分支上即可

```java
git merge origin/master
```

而 pull 命令则是相当于将 fetch 和 merge 这两个命令放在一起执行了，它可以从远程版本库上获取最新的代码并且合并到本地

```java
git pull origin master
```

