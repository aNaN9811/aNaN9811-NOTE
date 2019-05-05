# 第十二章：最佳的 UI 体验——Material Design 实战





## 什么是 Material Design

Material Design 是解决 Android 平台界面风格不统一的问题

新建一个 MaterialTest 项目





## Toolbar

Toolbar 是我们接触的第一个 Material 控件，它的另一个相关控件 ActionBar 就是我们编写的所有程序每个活动最顶部的那个标题栏

ActionBar 由于其设计的原因，被限定只能位于活动的顶部，从而不能实现一些 Material Design 的效果，因此官方现在已经不再建议使用 ActionBar，所以我们直接使用 Toolbar

Toolbar 的强大功能之处在于它不仅继承了 ActionBar 的所有功能，而且灵活性很高，可以配合其他空间来完成一些 Material Design 的效果

首先要知道任何一个新建的项目，默认都是会显示 ActionBar，其实这是根据项目中指定的主题来显示的，打开 AndroidManifest.xml

```java
<application
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true"
    android:theme="@style/AppTheme">
    ...
</application>
```

可以看到这里使用 android:theme 属性指定了一个 AppTheme 的主题，那这个 AppTheme 在哪里定义的呢？打开 res/values/styles.xml 文件

```java
<resources>

    <!-- Base application theme. -->
    //这里定义了一个叫 AppTheme 的主题，然后指定它的 parent 主题是 Theme.AppCompat.Light.DarkActionBar。这个 DarkActionBar 是一个深色的 ActionBar 主题，我们之前所有的项目中自带的 ActionBar 就是因为指定了这个主题才出现的
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>

</resources>
```

现在我们准备使用 Toolbar 来替代 ActionBar，因此需要指定一个不带 ActionBar 的主题，通常有 Theme.AppCompat.NoActionBar 和 Theme.AppCompat.Light.NoActionBar 表示淡色主题，它会将界面的主体颜色设成深色，配称颜色设成淡色。而 Theme.AppCompat.Light.NoActionBar 表示淡色主题，它会将界面的主体颜色设成淡色，陪衬颜色设成深色。由于我们之前的程序一直都是以淡色为主的，所以就选用淡色主题

```java
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>

</resources>
```

AppTheme 其他属性

![](F:\Android笔记\第十二章笔记图片\微信图片_20190422003350.jpg)



现在我们已经把 ActionBar 隐藏起来了，那么接下来看一看怎么使用 Toolbar 来替代 ActionBar，修改 activity_main.xml

```java
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    //这里使用 xmlns:app 指定了一个新的命名空间
    //思考一下，正是由于每个布局文件都会使用 xmlns:android 来指定一个命名空间，因此我们才能一直使用 android:id 、android:layout_width 等写法，那么这里指定了 xmlns:app ，也就是说现在可以使用 app:attribute 这样的写法了。但是为什么这里要指定一个 xmlns:app 的命名空间呢？这是由于 Material Design 是在 Android 5.0 系统中才出现的，而很多的 Material 属性在 5.0 之前的系统中并不存在，那么为了能够兼容之前的老系统，我们就不能使用 android:attribute 这样的写法了， 而是应该使用 app:attribute
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

	//定义一个 Toolbar 控件，这个控件是由 appcompat-v7 库提供的。这里我们给 Toolbar 指定了一个 id ，将它的宽度设置为 match_parent，高度设置为 avtionBar 的高度，背景色设置为 colorPrimary。不过由于我们刚才在 style.xml 中将程序的主题指定成了淡色主题，因此 Toolbar 现在也是淡色主题，而 Toolbar 上面的各种元素就会自动使用深色系，这是为了和主题颜色区别开。但是这个效果看起来就会很差，之前使用 ActionBar 时文字都是白色的，现在变成黑色的会很难看。那么为了能让 Toolbar 单独使用深色主题，这里我们使用 android:theme 属性，将 Toolbar 的主题指定成了 ThemeOverlay.AppCompat.Dark.ActionBar。但是这样又会出现新的问题，如果 Toolbar 中有菜单按钮，那么弹出的菜单项也会变成深色主题，这样就再次变得十分难看，于是这里使用了 app:popupTheme 属性单独将弹出的菜单项指定成了淡色主题
	//之所以使用 app:popupTheme 也是上面所讲的原因，为了兼容 Android 5.0 以下的系统
    <android.support.v7.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        android:background="?attr/colorPrimary"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
        app:popupTheme="@style/ThemeOverlay.AppCompat.Light" />

</FrameLayout>
```

修改 MainActivity

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //首先通过 findViewById() 得到 Toolbar 的实例，然后调用 setSupportActionBar() 方法并将 Toolbar 的实例传入，这样我们就做到既使用了 Toolbar，又让它外观与功能和 ActionBar 一致了
        Toolbar toolbar = findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);
    }
}
```

虽然标题栏看上去和之前的标题栏没什么两样，但其实它已经是 Toolbar 而不是 ActionBar 了，因此它具备了实现 Material Design 效果的能力



修改标题栏上显示的文字内容，这段内容是在 AndroidManifest.xml 中指定的

```java
<application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity"
            //给 activity 增加了一个 android:label 属性，用于指定在 Toolbar 中显示的文字内容，如果没有指定的话，会默认使用 application 中指定的 label 内容，也就是我们的应用名称
            android:label="Fruits">
            ...
        </activity>
    </application>
```



只有一个标题的 Toolbar 看起来太单调了，我们还可以再添加一些 action 按钮来让 Toolbar 更加丰富一些，这里提前准备一些 action 按钮来让 Toolbar 更加丰富一下，将提前准备好的一些图片放在 drawable-xxhdpi 目录下，右击 res 目录 -> New -> Directory 创建一个 menu 文件夹，右击 menu 文件夹 -> New -> Menu resource file，创建一个 toolbar.xml 文件，并修改：

```java
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto" >

    //通过 <item> 标签来定义 action 按钮，android:id 用于指定按钮的 id，android:icon 用于指定按钮的图标，android:title 用于指定按钮的文字
    <item
        android:id="@+id/backup"
        android:icon="@drawable/ic_backup"
        android:title="Backup"
	    //接着使用 app:showAsAction 来指定按钮的显示位置，有以下几种值可选：always 表示永远显示在 Toolbar 中，如果屏幕空间不够则不显示；ifRoom 表示屏幕空间足够的情况下显示在 Toolbar 中，不够的话就显示在菜单当中；never 则表示永远显示在菜单当中。注意，Toolbar 中的 action 按钮只会显示图标，菜单中的 action 按钮只会显示文字            
        app:showAsAction="always" />

    <item
        android:id="@+id/delete"
        android:icon="@drawable/ic_delete"
        android:title="Delete"
        app:showAsAction="ifRoom" />

    <item
        android:id="@+id/setting"
        android:icon="@drawable/ic_settings"
        android:title="Settings"
        app:showAsAction="never" />

</menu>
```



修改 MainActivity

```java
public class MainActivity extends AppCompatActivity {

	...

    //在 onCreateOptionsMenu() 方法中加载了 toolbar.xml 这个菜单文件
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.toolbar, menu);
        return true;
    }

    //在 onOptionsItemSelected() 方法中处理各个按钮的点击事件
    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case R.id.backup:
                Toast.makeText(this, "You clicked Backup", Toast.LENGTH_SHORT).show();
                break;
            case R.id.delete:
                Toast.makeText(this, "You clicked Delete", Toast.LENGTH_SHORT).show();
                break;
            case R.id.setting:
                Toast.makeText(this, "You clicked Settings", Toast.LENGTH_SHORT).show();
                break;
            default:
        }
        return true;
    }
    
}
```





## 滑动菜单



### DrawerLayout

所谓的滑动菜单就是将一些菜单选项隐藏起来，而不是放置在主屏幕上，然后可以通过滑动的方式将菜单显示出来，这种方式既节省了屏幕空间，又实现了非常好的动画效果，是 Material Design 中推荐的做法

不过如果是全靠我们自己去实现上述功能，难度恐怕很大，幸运的是 Google 提供了一个 DrawerLayout 控件，借助这个控件，实现滑动菜单简单又方便

DrawerLayout 是一个布局，在布局中允许放入两个直接子控件，第一个子控件是主屏幕中显示的内容，第二个子控件是滑动菜单中显示的内容。修改 activity_main.xml

```java
//最外层的控件使用了 DrawerLayout，这个控件是由 support-v4 库提供的
<android.support.v4.widget.DrawerLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    //第一个子控件使用刚才的 Toolbar，用于作为主屏幕中显示的内容 
    <FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            app:popupTheme="@style/ThemeOverlay.AppCompat.Light" />

    </FrameLayout>
    
    //第二个子控件使用了一个 TextView，用于作为滑动菜单中显示的内容，其实使用什么都可以，DrawerLayout 并没有限制只能使用固定的控件
    <TextView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        android:text="This is menu"
        android:background="#FFF" />

</android.support.v4.widget.DrawerLayout>
```

但是关于第二个子控件有一点需要注意，layout_gravity 这个属性是必须指定的，因为我们需要告诉 DrawerLayout 滑动菜单是在屏幕的左边还是右边，指定 left 表示滑动菜单在左边，指定 right 表示滑动菜单在右边。这里我们指定了 start，表示会根据系统语言进行判断，如果系统语言是从左往右的，比如英语、汉语，滑动菜单就在左边，如果系统语言是从右往左的，比如阿拉伯语，滑动菜单就在右边

不过现在滑动菜单有点问题，因为只有在屏幕的左侧边缘滑动进行拖动才能将菜单拖出来，而很多用户可能根本就不知道有这个功能，为了提示用户有这个功能， Material Design 建议的做法是在 Toolbar 的最左边加入一个导航按钮，点击了按钮也会将滑动的菜单内容展示出来，这样就相当于给用户提供了两种打开滑动菜单的方式，防止一些用户不知道屏幕的左侧边缘是可以拖动的

修改 MainActivity

```java
public class MainActivity extends AppCompatActivity {

    private DrawerLayout mDrawerLayout;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Toolbar toolbar = findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);
        //获取 DrawerLayout 实例
        mDrawerLayout = findViewById(R.id.drawer_layout);
        //调用 getSupportActionBar() 方法得到 ActionBar 实例，虽然这个 ActionBar 的具体实现是由 Toolbar 来完成的
        ActionBar actionBar = getSupportActionBar();
        if (actionBar != null) {
            //接着调用 ActionBar 的 setDisplayHomeAsUpEnabled() 方法让导航按钮显示出来，又调用了 setHomeAsUpIndicator() 方法来设置一个导航按钮图标。实际上，Toolbar 最左侧的这个按钮就叫做 HomeAsUp 按钮，它默认的图标是一个返回的箭头，含义是返回上一个活动。很明显，这里我们将它默认的样式和作用都进行了修改
            actionBar.setDisplayHomeAsUpEnabled(true);
            actionBar.setHomeAsUpIndicator(R.drawable.ic_menu);
        }
    }

    ...

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            //在 onOptionsItemSelected() 方法中中对 HomeAsUp 按钮的点击事件进行处理，HomeAsUp 按钮的 id 永远都是 android.R.id.home，然后调用 DrawerLayout 的 openDrawer() 来将滑动菜单展示出来，该方法要求传入一个 Gravity 参数，为了保证这里了的行为和 XML 中定义的一直，我们传入 GravityCompat.START
            case android.R.id.home:
                mDrawerLayout.openDrawer(GravityCompat.START);
                break;
			...
        }
        return true;
    }

}
```



### NavigationView

我们现在做出的菜单还非常丑，事实上，我们可以在滑动菜单页面定制任意的布局，不过谷歌给我们提供了一种更好的方法——使用 NavigationView

NavigationView 是 Design Support 库中提供的一个控件，它不仅是严格按照 Material Design 的要求来进行设计的，而且还可以将滑动菜单页面的实现变得非常简单

首先将  Design Support 库引入到项目中，打开 app/build.gradle ，在 dependencies 中添加

```java
dependencies {
    //第一行是 Design Support 库
    implementation 'com.android.support:design:28.0.0'
    //第二行是一个开源项目 CircleImageView，它可以用来轻松实现图片图形化的功能
    // CircleImageView 的项目主页是 http://github.com/hdodenhof/CircleImageView
    implementation 'de.hdodenhof:circleimageview:2.1.0'
    ...
}
```

在开始使用 CircleImageView 之前， 我们还需要提前准备好两个东西：menu 和 headerLayout。menu 是用来在 NavigationView 中显示具体的菜单项的，headerLayout 则是用来在 NavigationView 中显示头部布局的

右击 menu 文件夹 -> New -> Menu resource file，创建一个 nav_menu.xml

```java
//首先在 <menu> 标签中嵌套了一个 <group> 标签，然后将 group 的 checkableBehavior 指定为 single 表示组中的所有菜单项只能单选
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <group android:checkableBehavior="single" >
        
        //定义了 5 个 item ，分别使用 android:id 属性指定菜单项的 id， android:icon 属性指定菜单项的图标， android:title 属性指定菜单项显示的文字

        <item
            android:id="@+id/nav_call"
            android:icon="@drawable/nav_call"
            android:title="Call" />

        <item
            android:id="@+id/nav_friends"
            android:icon="@drawable/nav_friends"
            android:title="Friends" />

        <item
            android:id="@+id/nav_location"
            android:icon="@drawable/nav_location"
            android:title="Location" />

        <item
            android:id="@+id/nav_mail"
            android:icon="@drawable/nav_mail"
            android:title="Mail" />

        <item
            android:id="@+id/nav_task"
            android:icon="@drawable/nav_task"
            android:title="Tasks" />
        
    </group>
</menu>
```

接下来应该准备 headerLayout 了，这是一个可以随意定制的布局，我们就在 headerLayout 中放置头像、用户名、邮箱地址这 3 项内容

右击 layout 文件夹 -> New -> Layout resource file，创建一个 nav_header.xml

```java
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" 
    android:layout_height="180dp"
    android:padding="10dp"
    android:background="?attr/colorPrimary">
    
    // CircleImageView 是一个用于将图片圆形化的控件，它的用法和 ImageView 是完全一样，这里给它指定了一张图片作为头像，然后设置为居中显示
    <de.hdodenhof.circleimageview.CircleImageView
        android:id="@+id/icon_image"
        android:layout_width="70dp"
        android:layout_height="70dp"
        android:src="@drawable/nav_icon"
        android:layout_centerInParent="true"/>
    
    <TextView
        android:id="@+id/mail"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:text="tonygreendev@gmail.com"
        android:textColor="#FFF"
        android:textSize="14sp" />
    
    <TextView
        android:id="@+id/username"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_above="@+id/mail"
        android:text="Tony Green" 
        android:textColor="#FFF"
        android:textSize="14sp" />

</RelativeLayout>
```

修改 activity_main.xml

```java
<android.support.v4.widget.DrawerLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            app:popupTheme="@style/ThemeOverlay.AppCompat.Light" />

    </FrameLayout>

	//将之前的 TextView 换成 NavigationView，这样滑动菜单中显示的内容也就变成了 NavigationView
    <android.support.design.widget.NavigationView
        android:id="@+id/nav_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        //通过 app:menu 和 app:headerLayout 将刚才准备好的 menu 和 headerLayout 设置了进去，这样 NavigationView 就定义完成了
        app:menu="@menu/nav_menu"
        app:headerLayout="@layout/nav_header" />

</android.support.v4.widget.DrawerLayout>
```

NavigationView 虽然定义完成了，但我们还需要去处理菜单项的点击事件才行，修改 MainActivity

```java
public class MainActivity extends AppCompatActivity {

    private DrawerLayout mDrawerLayout;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Toolbar toolbar = findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);
        mDrawerLayout = findViewById(R.id.drawer_layout);
        //首先获取 NavigationView 实例
        NavigationView navView = findViewById(R.id.nav_view);
        ActionBar actionBar = getSupportActionBar();
        if (actionBar != null) {
            actionBar.setDisplayHomeAsUpEnabled(true);
            actionBar.setHomeAsUpIndicator(R.drawable.ic_menu);
        }
        //然后调用它的 setCheckedItem() 方法将 Call 菜单项设置为默认选中
        navView.setCheckedItem(R.id.nav_call);
        //接着调用 setNavigationItemSelectedListener() 方法来设置一个菜单选中事件的监听器，当用户点击了任意菜单项时，就会回调到 onNavigationItemSelected() 方法中，我们可以在这个方法中写相应的逻辑处理，不过这里我们并没有附加任何逻辑，只是调用了 DrawerLayout 的 closeDrawers() 方法将滑动菜单关闭
        navView.setNavigationItemSelectedListener(new NavigationView.OnNavigationItemSelectedListener() {
            @Override
            public boolean onNavigationItemSelected(MenuItem item) {
                mDrawerLayout.closeDrawers();
                return true;
            }
        });
    }

	...

}
```





## 悬浮按钮和可交互提示

立面设计是 Material Design 中一条非常重要的设计思想，也就是说，按照 Material Design 的理念，应用程序的界面不仅仅只是一个平面，而应该是有立体效果的

官方给出的示例中，最简单且最具代表性的立面设计就是悬浮按钮了，这种按钮不属于平面的一部分，而是位于另外一个维度的，因此给人一种悬浮的感觉



### FloatingActionButton

FloatingActionButton 是 Design Support 库中提供的一个控件，这个控件可以帮助我们比较轻松地实现悬浮按钮的效果

它默认会使用 colorAccent 来作为按钮的颜色，我们还可以通过给按钮指定一个图标来表明这个按钮的作用是什么

修改 activity_main.xml

```java
<android.support.v4.widget.DrawerLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            app:popupTheme="@style/ThemeOverlay.AppCompat.Light" />

        //在主屏幕中加入了一个 FloatingActionButton
        <android.support.design.widget.FloatingActionButton
            android:id="@+id/fab"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            // layout_gravity 属性指定将这个控件放置于屏幕的右下角，其中 end 的工作原理和之前的 start 是一样的，即如果系统语言是从左往右的，那么 end 就表示在右边，如果系统语言是从右往左的，那么 end 就表示在左边
            android:layout_gravity="bottom|end"
            //通过 layout_margin 属性给控件的四周留点边距，紧贴着屏幕边缘是不好看的，最后通过 src 属性给 FloatingActionButton 设置了一个图标
            android:layout_margin="16dp"
            android:src="@drawable/ic_done" />

    </FrameLayout>

	...

</android.support.v4.widget.DrawerLayout>
```

仔细观察会发现按钮下面有阴影，这就是悬浮所带来的投影，其实我们还可以指定 FloatingActionButton 的悬浮高度

```java
<android.support.design.widget.FloatingActionButton
    android:id="@+id/fab"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="bottom|end"
    android:layout_margin="16dp"
    android:src="@drawable/ic_done"
    //使用 app:elevation 属性给 FloatingActionButton 指定一个高度值，高度值越大，投影范围也越大，但是投影效果越淡，高度值越小，投影范围也越小，但是投影效果越浓，一般 FloatingActionButton 默认效果就已经足够了
    app:elevation="8dp" />
```

FloatingActionButton 处理点击事件，修改 MainActivity

```java
public class MainActivity extends AppCompatActivity {

    private DrawerLayout mDrawerLayout;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
		...
        //其实 FloatingActionButton 和 Button 没有什么两样，都是调用 setOnClickListener() 方法来注册一个监听器，当点击按钮时，就会执行监听器中的 onClick() 方法，这里我们在 onClick() 方法中弹出了一个 Toast
        FloatingActionButton fab = findViewById(R.id.fab);
        fab.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Toast.makeText(MainActivity.this, "FAB clicked", Toast.LENGTH_SHORT).show();
            }
        });
    }
}
```



### Snackbar

一个 Design Support 库提供的更加先进的提示工具 —— Snackbar

首先要明确，Snackbar 并不是 Toast 的替代品，它们两者之间有着不同的应用场景

Toast 的作用是告诉用户现在发生了什么事情，但同时用户只能被动接收这个事情，因为没有什么办法能让用户选择

而 Snackbar 则在这方面进行了扩展，它允许在提示当中加入一个可交互按钮，当用户点击按钮的时候可以执行一些额外的操作逻辑。打个比方，如果我们在执行删除操作的时候只弹出一个 Toast 提示，那么用户是误删了某个重要数据的话肯定十分抓狂吧，但是如果我们增加了一个 Undo 按钮，就相当于给用户提供了一种弥补措施，从而大大降低了事故发生的概率，提升了用户的体验

Snackbar 的用法也非常简单，它和 Toast 基本相似，只不过可以额外增加一个按钮的点击事件，修改 MainActivity

```java
public class MainActivity extends AppCompatActivity {

    private DrawerLayout mDrawerLayout;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
		...
        FloatingActionButton fab = findViewById(R.id.fab);
        fab.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                //调用 Snackbar 的 make() 方法来创建一个 Snackbar 对象
                // make() 的方法第一个参数需要传入一个 View，只要当前界面布局的任意一个 View 都可以，Snackbar 会使用这个 View 来自动查找最外层的布局，用于展示 Snackbar。第二个参数就是 Snackbar 中显示的内容，第三个参数是 Snackbar 显示的时长。这些都和 Toast 类似
                //接着又调用一个 setAction() 方法来设置一个动作，从而让 Snackbar 不仅仅是一个提示，而是可以和用户进行交互的，这里我们在动作按钮的点击事件里面弹出一个 Toast 提示，最后调用 show() 方法让 Snackbar 显示出来
                Snackbar.make(view, "Data deleted", Snackbar.LENGTH_SHORT)
                        .setAction("Undo", new View.OnClickListener() {
                            @Override
                            public void onClick(View v) {
                                Toast.makeText(MainActivity.this, "Data restored", Toast.LENGTH_SHORT).show();
                            }
                        })
                    	.show();
            }
        });
    }

	...

}
```

你会发现运行后 Snackbar 会将我们的悬浮按钮遮挡住，虽然 Snackbar 过一会就会消失，解决的方法只需要借助 CoordinatorLayout 就可以解决



### CoordinatorLayout

CoordinatorLayout 可以说是一个加强版的 FrameLayout，这个布局也是由 Design Support 库提供的，它的普通情况下和 FrameLayout 基本一致，也有一些 Material Design

事实上，CoordinatorLayout 可以监听其所有子控件的各种事件，然后自动帮助我们做出最为合理的响应。举个简单的例子，刚才弹出的 Snackbar 提示将悬浮按钮遮挡住了，而如果我们能让 CoordinatorLayout 监听到 Snackbar 的弹出事件，那么它会自动将内部的 FloatingActionButton 向上偏移，从而确保不会被 Snackbar 遮挡到

使用 CoordinatorLayout 也非常简单，只需要将原来的 FrameLayout 替换一下就好了，修改 activity_main.xml

```java
<android.support.v4.widget.DrawerLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    //由于 CoordinatorLayout 本身就是加强版的 FrameLayout，因此这种替换不会有任何的副作用
    <android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            app:popupTheme="@style/ThemeOverlay.AppCompat.Light" />

        <android.support.design.widget.FloatingActionButton
            android:id="@+id/fab"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="bottom|end"
            android:layout_margin="16dp"
            android:src="@drawable/ic_done"
            app:elevation="8dp" />

    </android.support.design.widget.CoordinatorLayout>

    ...

</android.support.v4.widget.DrawerLayout>
```

回头思考一下，刚才说 CoordinatorLayout 可以监听其所有子控件的各种事件，但是 Snackbar 好像并不是 CoordinatorLayout 的子控件吧，为什么它会被监听到呢？

其实道理很简单，我们在 Snackbar 的 make() 方法中传入的第一个参数，这个参数就是用来指定 Snackbar 是基于那个 View 来触发的，刚才我们传入的是 FloatingActionButton 本身，而 FloatingActionButton 是 CoordinatorLayout 中的子控件，因此这个事件就理所应当能被监听到了。你可以自己实验一下，如果给 Snackbar 的 make() 方法传入一个 DrawerLayout，那么 Snackbar 就会再次遮挡住悬浮按钮，因为 DrawerLayout 不是 CoordinatorLayout 的子控件，CoordinatorLayout 也无法监听到 Snackbar 的弹出和隐藏事件了

```java
FloatingActionButton fab = findViewById(R.id.fab);
fab.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        Snackbar.make(mDrawerLayout, "Data deleted", Snackbar.LENGTH_SHORT)
                .setAction("Undo", new View.OnClickListener() {
                    @Override
                    public void onClick(View v) {
                        Toast.makeText(MainActivity.this, "Data restored", Toast.LENGTH_SHORT).show();
                    }
                })
                .show();
    }
});
```





## 卡片式布局

卡片式布局也是 Materials Design 中提出的一个新的概念，它可以让页面中的元素看起来就像在卡片中一样，并且还能拥有圆角和投影



### CardView

CardView 是用于实现卡片布局效果的重要控件，由 appcompat-v7 库提供，实际上，CardView 也是一个 FrameLayout，只是额外提供了圆角和阴影等效果

CardView 的基本用法

```java
//定义一个 CardView 布局
<android.support.v7.widget.CardView
	android:layout_width="match_parent"
	android:layout_height="wrap_content"
    //通过 app:cardCornerRadius 属性指定卡片圆角的弧度，数值越大，圆角的弧度也越大
	app:cardCornerRadius="4dp"
    //通过 app:elevation 属性指定卡片的高度，高度值越大，投影范围也越大，但是投影效果越淡，高度值越小，投影范围也越小，但是投影效果越浓，这一点和 FloatingActionButton 是一致的
	app:elevation="5dp">
	// CardView 布局中放置了一个 TextView，那么这个 TextView 就会显示在一张卡片当中了
	<TextView
		android:id="@+id/info_text"
		android:layout_width="match_parent"
		android:layout_height="wrap_content" />
</android.support.v7.widget.CardView>	
```

使用 RecyclerView 来填充 MaterialTest 项目的主界面部分，在 app/build.gradle 中先声明 RecyclerView 和 CardView 的依赖库，同时添加了一个 Glide 库的依赖

```java
dependencies {
	...
    implementation 'com.android.support:recyclerview-v7:24.2.1'
    implementation 'com.android.support:cardview-v7:24.2.1'
    implementation 'com.github.bumptech.glide:glide:3.7.0'
}
```

修改 activity_main.xml

```java
<android.support.v4.widget.DrawerLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <android.support.design.widget.CoordinatorLayout 
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            app:popupTheme="@style/ThemeOverlay.AppCompat.Light" />

        //在 CoordinatorLayout 中添加了一个 RecyclerView，给它指定一个 id，然后将高度和宽度设置为 match_parent 使得 RecyclerView 占满整个布局空间
        <android.support.v7.widget.RecyclerView
            android:id="@+id/recycler_view"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />

        <android.support.design.widget.FloatingActionButton
            android:id="@+id/fab"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="bottom|end"
            android:layout_margin="16dp"
            android:src="@drawable/ic_done"
            app:elevation="8dp" />

    </android.support.design.widget.CoordinatorLayout>

	...

</android.support.v4.widget.DrawerLayout>
```

定义一个实体 Fruit 类

```java
public class Fruit {
    
    private String name;
    
    private int imageId;
    
    public Fruit(String name, int imageId) {
        this.name = name;
        this.imageId = imageId;
    }

    public String getName() {
        return name;
    }

    public int getImageId() {
        return imageId;
    }
}
```

Fruit 类中只有两个字段，name 表示水果的名字，imageId 表示水果对应图片资源的 id

然后需要为 RecyclerView 的子项制定一个我们自定义的布局，在 layout 目录下新建 fruit_item.xml 

```java
//使用 CardView 作为子项的最外层布局，从而使得 RecyclerView 中的每个元素都是在卡片当中的。CardView 由于是一个 FrameLayout，因此它没有什么方便的定位方式，这里我们只好在 CardView 中再嵌套一个 LinearLayout，然后在 LinearLayout 中放置具体的内容
<android.support.v7.widget.CardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="5dp"
    app:cardCornerRadius="4dp" >

    <LinearLayout
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

            <ImageView
                android:id="@+id/fruit_image"
                android:layout_width="match_parent"
                android:layout_height="100dp"
                // scaleType 这个属性可以指定图片的缩放模式
                //由于各张水果图片的长宽比例可能都不一致，为了让所有的图片都能充满整个 ImageView，这里使用了 centerCrop 模式，它可以让图片保持原有比例填充满 ImageView，并将超出的部分裁减掉
                android:scaleType="centerCrop" />

            <TextView
                android:id="@+id/fruit_name"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="center_horizontal"
                android:layout_margin="5dp"
                android:textSize="16sp" />
    </LinearLayout>

</android.support.v7.widget.CardView>
```

接下来为 RecyclerView 准备适配器，新建 FruitAdapter 类

```java
public class FruitAdapter extends RecyclerView.Adapter<FruitAdapter.ViewHolder> {

    private Context mContext;

    private List<Fruit> mFruitList;

    static class ViewHolder extends RecyclerView.ViewHolder {
        CardView cardView;
        ImageView fruitImage;
        TextView fruitName;

        public ViewHolder(View view) {
            super(view);
            cardView = (CardView) view;
            fruitImage = view.findViewById(R.id.fruit_image);
            fruitName = view.findViewById(R.id.fruit_name);
        }
    }

    public FruitAdapter(List<Fruit> fruitList) {
        mFruitList = fruitList;
    }

    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        if (mContext == null) {
            mContext = parent.getContext();
        }
        View view = LayoutInflater.from(mContext).inflate(R.layout.fruit_item, parent, false);
        return new ViewHolder(view);
    }

    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        Fruit fruit = mFruitList.get(position);
        holder.fruitName.setText(fruit.getName());
        //使用 Glide 来加载水果图片
        //首先调用 Glide.with() 方法并传入一个 Context、Activity 或 Fragment 参数，然后调用 load() 方法去加载图片，可以是一个 URL 地址，也可以是一个本地路径，或者是一个资源 id，最后调用 into() 方法将图片设置到某一个 ImageView 中就可以了
        //使用 Glide 而不是传统的设置图片的方式是因为这次我们从网上找到的这些水果图片像素都非常高，如果不进行压缩就直接显示的话就会引起内存溢出，而使用 Glide 就完全不需要担心这回事，因为 Glide 在内部做了许多非常复杂的逻辑操作，其中就包括了图片压缩，我们只需要安心按照 Glide 的标准去加载图片就可以了
        Glide.with(mContext)
                .load(fruit.getImageId())
                .into(holder.fruitImage);
    }

    @Override
    public int getItemCount() {
        return mFruitList.size();
    }
    
}
```

修改 MainActivity

```java
public class MainActivity extends AppCompatActivity {

    private DrawerLayout mDrawerLayout;

    //定义一个数组，数组里面存放了很多个 Fruit 的实例，每个实例都代表一种水果
    private Fruit[] fruits = {new Fruit("Apple", R.drawable.apple), new Fruit("Banana", R.drawable.banana),
                                new Fruit("Orange", R.drawable.orange), new Fruit("Watermelon", R.drawable.watermelon),
                                new Fruit("Pear", R.drawable.pear), new Fruit("Grape", R.drawable.grape),
                                new Fruit("Pineapple", R.drawable.pineapple), new Fruit("Strawberry", R.drawable.strawberry),
                                new Fruit("Cherry", R.drawable.cherry), new Fruit("Mango", R.drawable.mango)};

    private List<Fruit> fruitList = new ArrayList<>();

    private FruitAdapter adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ...
        initFruits();
        //在第三章中我们已经学过了 LinearLayoutManager 和 StaggeredGridLayoutManager ，现在使用了 GridLayoutManager 终于将所有的布局方式补齐了
        // GridLayoutManager 的用法也没有什么特别之处，它的构造函数接收两个参数，第一个是 Context，第二个是列数，这里我们希望每一行中会有两列数据
        RecyclerView recyclerView = findViewById(R.id.recycler_view);
        GridLayoutManager layoutManager = new GridLayoutManager(this,2);
        recyclerView.setLayoutManager(layoutManager);
        adapter = new FruitAdapter(fruitList);
        recyclerView.setAdapter(adapter);
    }

    //在 initFruits() 方法中先是清空了一下 fruitList 中的数据，接着使用一个随机函数，从刚才定义的 Fruit 数组中随机天选一个水果放入到 fruitList 当中，这样每一次打开程序看到的水果数据都会是不同的
    private void initFruits() {
        fruitList.clear();
        for (int i = 0; i < 50; i ++) {
            Random random = new Random();
            int index = random.nextInt(fruits.length);
            fruitList.add(fruits[index]);
        }
    }

}
```

运行后发现 Toolbar 被 RecyclerView 给挡住了，解决需要借助到另外一个工具了——AppBarLayout



### AppBarLayout

RecyclerView 为什么会把 Toolbar 给挡住呢？其实并不难理解，由于 RecyclerView 和 Toolbar 都是放置在 CoordinatorLayout 中的，而前面已经说过， CoordinatorLayout 就是一个加强版的 FrameLayout，那么 FrameLayout 中的所有控件在不进行明确定位的情况下，默认都会摆放在布局的左上角，从而也就产生了遮挡现象。其实这已经不是我们第一次遇到这种情况了，我们在 第三章第三小节的学习中早就见识过控件与控件之间遮挡的效果

传统情况下，使用偏移是唯一的解决方法，即让 RecyclerView 向下偏移一个 Toolbar 的高度，从而不会遮挡 Toolbar

由于我们使用的是 CoordinatorLayout，因此自然会有更加巧妙的方法，这里我们使用 Design Support 库中提供的另外一个工具—— AppBarLayout，它实际上是一个垂直方向的 LinearLayout，它在内部做了很多滚动事件的封装，并应用了一些 Material Design 的设计理念

我们解决覆盖的问题只需要两步就可以了，第一步将 Toolbar 嵌套到 AppBarLayout 中，第二步给 RecyclerView 指定一个布局行为，修改 activity_main.xml

```java
<android.support.v4.widget.DrawerLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <android.support.design.widget.CoordinatorLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

		//首先定义了一个 AppBarLayout，并将 Toolbar 放置在了 AppBarLayout 里面
        <android.support.design.widget.AppBarLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content" >

            <android.support.v7.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                android:background="?attr/colorPrimary"
                android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
                app:popupTheme="@style/ThemeOverlay.AppCompat.Light" />

        </android.support.design.widget.AppBarLayout>

        <android.support.v7.widget.RecyclerView
            android:id="@+id/recycler_view"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            //然后在 RecyclerView 中使用 app:layout_behavior 属性指定了一个布局行为，其中 appbar_scrolling_view_behavior 这个字符串也是由 Design Support 库提供的
            app:layout_behavior="@string/appbar_scrolling_view_behavior" />

        <android.support.design.widget.FloatingActionButton
            android:id="@+id/fab"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="bottom|end"
            android:layout_margin="16dp"
            android:src="@drawable/ic_done"
            app:elevation="8dp" />

    </android.support.design.widget.CoordinatorLayout>

    ...

</android.support.v4.widget.DrawerLayout>
```

AppBarLayout 到底能实现什么样的 Material Design 效果呢？当 AppBarLayout 接收到滚动事件的时候，它内部的子控件其实是可以指定如何去影响这些事件的，通过 app:layout_scrollFlags 属性就能实现，修改 activity_main.xml

```java
<android.support.v4.widget.DrawerLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <android.support.design.widget.CoordinatorLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <android.support.design.widget.AppBarLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content" >

            <android.support.v7.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                android:background="?attr/colorPrimary"
                android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
                app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
                //在 Toolbar 中添加了一个 layout_scrollFlags 属性，并将这个属性的值指定成了 scroll|enterAlways|snap
                // scroll 表示当 RecyclerView 向上滚动的时候，Toolbar 会跟着一起向上滚动并实现隐藏
                // enterAlways 表示当 RecyclerView 向下滚动的时候，Toolbar 会跟着一起向下滚动并重新显示
                // snap 表示当 Toolbar 还没有完全隐藏或显示的时候，会根据当前滚动的距离，自动选择是隐藏还是显示
                app:layout_scrollFlags="scroll|enterAlways|snap"/>

        </android.support.design.widget.AppBarLayout>

   	...

</android.support.v4.widget.DrawerLayout>
```

像这种功能，如果是使用  ActionBar 的话那就完全不可能实现的，Toolbar 的出现为我们提供了更多的可能



## 下拉刷新

SwipeRefreshLayout 就是用于实现下拉刷新功能的核心类，它是由 support-v4 库提供的，我们把想要实现下拉刷新功能的控件放置到 SwipeRefreshLayout 中，就可以迅速让这个控件支持下拉刷新，那么在 MaterialTest 项目中，应该支持下拉刷新的功能的控件自然就是 RecyclerView 了

修改 activity_main.xml

```java
//在 RecyclerView 的外面又嵌套了一层 SwipeRefreshLayout，这样 RecyclerView 就自动拥有下拉刷新功能了
<android.support.v4.widget.SwipeRefreshLayout
    android:id="@+id/swipe_refresh"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    //另外需要注意的是，由于 RecyclerView 现在变成了 SwipeRefreshLayout 的子控件，因此之前使用 app:layout_behavior 声明的布局行为现在也要移到 SwipeRefreshLayout 中才行
    app:layout_behavior="@string/appbar_scrolling_view_behavior" >

    <android.support.v7.widget.RecyclerView
        android:id="@+id/recycler_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</android.support.v4.widget.SwipeRefreshLayout>
```

虽然 RecyclerView 已经支持下拉刷新功能了，但是我们还要在代码中处理具体的刷新逻辑才行，修改 MainActivity

```java
public class MainActivity extends AppCompatActivity {

    ...

    private SwipeRefreshLayout swipeRefreshLayout;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ...
        //首先通过 findViewById() 方法拿到 SwipeRefreshLayout 的实例，然后调用 setColorSchemeResources() 方法来设置下拉刷新进度条的颜色，这里我们使用主题中的 colorPrimary 作为进度条的颜色，接着调用了 setOnRefreshListener() 方法来设置一下下拉刷新的监听器，当触发了下拉刷新操作的时候就会回调这个监听器的 onRefresh() 方法，我们在这里去处理具体的刷新逻辑就好了
        swipeRefreshLayout = findViewById(R.id.swipe_refresh);
        swipeRefreshLayout.setColorSchemeResources(R.color.colorPrimary);
        swipeRefreshLayout.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
            @Override
            //通常情况下，onRefresh() 方法中应该是去网络上请求最新的数据，然后再将这些数据展示出来，这里简单起见就不和网络进行交互，而是调用一个 refreshFruits() 方法进行本地刷新操作
            public void onRefresh() {
                refreshFruits();
            }
        });
    }

    // refreshFruits() 方法中先是开启了一个线程，然后将线程沉睡两秒钟，这样做是因为本地刷新操作速度非常快，如果不将线程沉睡的话，刷新立刻就结束了，从而看不到刷新的过程
    private void refreshFruits() {
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                //沉睡结束后使用了 runOnUiThread() 方法将线程切换回主线程，然后调用 initFruits() 方法重新生成数据，接着再调用 FruitAdapter 的 notifyDataSetChanged() 方法通知数据发生了变化，最后调用 SwipeRefreshLayout 的 setRefreshing() 方法并传入 false，用于表示刷新事件结束，并隐藏刷新进度条
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        initFruits();
                        adapter.notifyDataSetChanged();
                        swipeRefreshLayout.setRefreshing(false);
                    }
                });
            }
        }).start();
    }

    ...
    
}
```





## 可折叠式标题栏

虽然我们现在的标题栏是使用 Toolbar 来编写的，不过它看上去和传统的 ActionBar 其实没有什么两样，只不过可以响应 RecyclerView 的滚动事件来进行隐藏和显示。而 Material Design 中并没有限定标题栏必须是长这个样子的，事实上我们可以根据自己的喜好来定制标题栏的样式，我们就来实现一个可折叠式标题栏的效果，需要借助 CollapsingToolbarLayout 这个工具



### CollapsingToolbarLayout

CollapsingToolbarLayout 是一个作用于 Toolbar 基础之上的布局，它也是由 Design Support 库提供的。 CollapsingToolbarLayout 可以让 Toolbar 的效果变得更加丰富，不仅仅是展示一个标题栏，而是能够实现非常华丽的效果

不过，CollapsingToolbarLayout 是不能独立存在的，它在设计的时候就被限定只能作为 AppBarLayout 的直接子布局来使用，而 AppBarLayout 又必须是 CoordinatorLayout 的子布局



首先我们需要一个额外的活动来作为水果的详情展示界面，右击 com.example.materialtest 包 -> New -> Activity -> Empty Activity，创建一个 FruitActivity，并将布局名指定成 activity_fruit.xml，然后开始编写水果详情展示界面的布局

由于整个布局比较复杂，这里准备采用分段编写的方式，activity_fruit.xml 中的内容主要分为两部分，一个是水果标题栏，一个是水果内容详情

```java
//首先实现标题栏部分，这里使用 CoordinatorLayout 来作为最外层布局
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    //注意始终要定义一个 xmlns:app 的命名空间，在 Material Design 的开发中会经常用到它
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

</android.support.design.widget.CoordinatorLayout>
```

接着在 CoordinatorLayout 中嵌套一个 AppBarLayout

```java
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    
    <android.support.design.widget.AppBarLayout
        android:id="@+id/appBar"
        android:layout_width="match_parent"
        android:layout_height="250dp">
    </android.support.design.widget.AppBarLayout>

</android.support.design.widget.CoordinatorLayout>
```

接下来在 AppBarLayout 中再嵌套一个 CollapsingToolbarLayout

```java
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.design.widget.AppBarLayout
        android:id="@+id/appBar"
        android:layout_width="match_parent"
        android:layout_height="250dp">

        <android.support.design.widget.CollapsingToolbarLayout
            android:id="@+id/collapsing_toolbar"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            // android:theme 属性指定了一个 ThemeOverlay.AppCompat.Dark.ActionBar 的主题，其实对于这部分我们也并不陌生，因为之前在 activity_main.xml 中给 Toolbar 指定的也是这个主题，只不过这里要实现更加高级的 Toolbar 效果，因此需要将这个主题的指定提到上一层来
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            // app:contentScrim 属性用于指定 CollapsingToolbarLayout 在趋于折叠状态以及折叠之后的背景色，其实 CollapsingToolbarLayout 在折叠之后就是一个普通的 Toolbar，那么背景色就应该是 colorPrimary
            app:contentScrim="?attr/colorPrimary"
            // app:layout_scrollFlags 其实我们也是见过的，只不过之前是给 Toolbar 指定的，现在已到外面来了，其中，scroll 表示 CollapsingToolbarLayout 会随着水果内容详情的滚动一起滚动，exitUntilCollapsed 表示当 CollapsingToolbarLayout 随着滚动完成折叠之后就保留在界面上，不再移出屏幕
            app:layout_scrollFlags="scroll|exitUntilCollapsed" >
        </android.support.design.widget.CollapsingToolbarLayout>

    </android.support.design.widget.AppBarLayout>

</android.support.design.widget.CoordinatorLayout>
```

在 CollapsingToolbarLayout 中定义标题栏的具体内容

```java
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.design.widget.AppBarLayout
        android:id="@+id/appBar"
        android:layout_width="match_parent"
        android:layout_height="250dp">

		//在 CollapsingToolbarLayout 中定义了一个 ImageView 和 Toolbar，也就意味着，这个高级版的标题栏将是由普通的标题栏加上图片组合而成的
        <android.support.design.widget.CollapsingToolbarLayout
            android:id="@+id/collapsing_toolbar"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            app:contentScrim="?attr/colorPrimary"
            app:layout_scrollFlags="scroll|exitUntilCollapsed" >
            
            <ImageView
                android:id="@+id/fruit_image_view"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:scaleType="centerCrop"
                // app:layout_collapseMode 用于指定当前控件在 CollapsingToolbarLayout 折叠过程中的折叠模式，其中 Toolbar 指定成 pin，表示在折叠的过程中位置始终保持不变，ImageView 指定成 parallax，表示会在折叠过程中缠身一定的错位偏移，这种模式的视觉效果会非常好
                app:layout_collapseMode="parallax" />
            
            <android.support.v7.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                app:layout_collapseMode="pin" />
            
        </android.support.design.widget.CollapsingToolbarLayout>

    </android.support.design.widget.AppBarLayout>

</android.support.design.widget.CoordinatorLayout>
```

这样我们就将水果标题栏的界面编写完成了，下面开始编写水果内容详情部分，继续修改 activity_fruit.xml

```java
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.design.widget.AppBarLayout
        android:id="@+id/appBar"
        android:layout_width="match_parent"
        android:layout_height="250dp">

        <android.support.design.widget.CollapsingToolbarLayout
            android:id="@+id/collapsing_toolbar"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            app:contentScrim="?attr/colorPrimary"
            app:layout_scrollFlags="scroll|exitUntilCollapsed" >

            <ImageView
                android:id="@+id/fruit_image_view"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:scaleType="centerCrop"
                app:layout_collapseMode="parallax" />

            <android.support.v7.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                app:layout_collapseMode="pin" />

        </android.support.design.widget.CollapsingToolbarLayout>

    </android.support.design.widget.AppBarLayout>

	//水果内容详情的最外层布局使用了一个 NestedScrollView，注意它和 AppBarLayout 是平级的，我们在第九章第二节学过了 ScrollView 用法，它允许使用滚动的方式来查看屏幕以外的数据，而 NestedScrollView 在此基础上还增加了嵌套响应滚动事件的功能，由于 CoordinatorLayout 本身已经可以响应滚动事件了，因此我们在它的内部就需要使用 NestedScrollView 或 RecyclerView 这样的布局。
    <android.support.v4.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        //另外这里还通过 app:layout_behavior 属性指定了一个布局行为，这和之前 RecyclerView 中的用法是一模一样的
        app:layout_behavior="@string/appbar_scrolling_view_behavior" >
    </android.support.v4.widget.NestedScrollView>

</android.support.design.widget.CoordinatorLayout>
```

不管是在 ScrollView 还是 NestedScrollView，它们的内部都只允许存在一个直接子布局，因此，如果我们想要在里面放入很多东西的话，通常都会先嵌套一个 LinearLayout，然后再在 LinearLayout 中放入具体内容就可以了，例如：

```java
<android.support.v4.widget.NestedScrollView
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:layout_behavior="@string/appbar_scrolling_view_behavior" >
    
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
    </LinearLayout>
            
</android.support.v4.widget.NestedScrollView>
```

接下来在 LinearLayout 中放入具体的内容，这里准备使用一个 TextView 来显示水果的内容详情，并将在 TextView 放在一个卡片式布局当中

```java
<android.support.v4.widget.NestedScrollView
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:layout_behavior="@string/appbar_scrolling_view_behavior" >

    <LinearLayout
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

		//为了让界面更美观，CardView 和 TextView 都加了一些边距，其中 CardView 的 marginTop 加了 35dp 的边距是为了下面要编写的东西留出空间
        <android.support.v7.widget.CardView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:layout_marginLeft="15dp"
            android:layout_marginRight="15dp"
            android:layout_marginTop="35dp"
            app:cardCornerRadius="4dp" >

            <TextView
                android:id="@+id/fruit_content_text"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_margin="10dp" />

        </android.support.v7.widget.CardView>

    </LinearLayout>

</android.support.v4.widget.NestedScrollView>
```

准备在 activity_fruit.xml 中加入一个悬浮按钮表示评论作用

```java
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    
    ...
    
    // FloatingActionButton 和 AppBarLayout 以及 NestedScrollView 是平级的
	<android.support.design.widget.FloatingActionButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="16dp"
        android:src="@drawable/ic_comment"
        // app:layout_anchor 属性指定了一个锚点，我们将锚点设置为 AppBarLayout，这样悬浮按钮就会出现在水果标题栏的区域内，接着又使用 app:layout_anchorGravity 属性将悬浮按钮定位在标题栏区域的右下角
        app:layout_anchor="@id/appBar"
        app:layout_anchorGravity="bottom|end">
        
    </android.support.design.widget.FloatingActionButton>

</android.support.design.widget.CoordinatorLayout>
```

接下来开始编写功能逻辑，修改 FruitActivity

```java
public class FruitActivity extends AppCompatActivity {

    public static final String FRUIT_NAME = "fruit_name";

    public static final String FRUIT_IMAGE_ID = "fruit_image_id";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_fruit);
        //通过 Intent 获取到了传入的水果名和水果图片的资源 id，然后通过 findViewById() 方法获取各个控件的实例
        Intent intent = getIntent();
        String fruitName = intent.getStringExtra(FRUIT_NAME);
        int fruitImageId = intent.getIntExtra(FRUIT_IMAGE_ID, 0);
        Toolbar toolbar = findViewById(R.id.toolbar);
        CollapsingToolbarLayout collapsingToolbar = findViewById(R.id.collapsing_toolbar);
        ImageView fruitImageView = findViewById(R.id.fruit_image_view);
        TextView fruitContentText = findViewById(R.id.fruit_content_text);
        //将 Toolbar 作为 ActionBar 显示，并启用 HomeAsUpEnableed 按钮。由于 HomeAsUpEnableed 按钮的默认图标就是一个返回箭头，这正是我们所期望的，因此就不用再额外设置别的图标了
        setSupportActionBar(toolbar);
        ActionBar actionBar = getSupportActionBar();
        if (actionBar != null) {
            actionBar.setDisplayHomeAsUpEnabled(true);
        }
        //调用 CollapsingToolbarLayout 的 setTitle 方法将水果名设置成当前界面的标题，然后使用 Glide 加载传入的水果图片，并设置到标题栏的 ImageView 上
        collapsingToolbar.setTitle(fruitName);
        Glide.with(this)
                .load(fruitImageId)
                .into(fruitImageView);
        //接着需要填充水果的内容详情，这里使用了一个 generateFruitContent() 方法将水果名循环了 500 次，从而生成了一个表较长的字符串，将它设置到了 TextView 上
        String fruitContent = generateFruitContent(fruitName);
        fruitContentText.setText(fruitContent);
    }
    
    private String generateFruitContent(String fruitName) {
        StringBuilder fruitContent = new StringBuilder();
        for (int i = 0; i < 500; i++) {
            fruitContent.append(fruitName);
        }
        return fruitContent.toString();
    }
    
    //最后在 onOptionsItemSelected() 方法中处理了 HomeAsUp 按钮的点击事件，当点击了这个按钮时，就调用 finish() 方法关闭当前活动，从而返回上一个活动
    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case android.R.id.home:
                finish();
                return true;
        }
        return super.onOptionsItemSelected(item);
    }
    
}
```

处理 RecyclerView 的点击事件，不然无法打开 FruitActivity，修改 FruitAdapter

```java
public class FruitAdapter extends RecyclerView.Adapter<FruitAdapter.ViewHolder> {

    ...
    
    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        if (mContext == null) {
            mContext = parent.getContext();
        }
        View view = LayoutInflater.from(mContext).inflate(R.layout.fruit_item, parent, false);
        //给 CardView 注册了一个点击事件监听器，然后在点击事件中获取当前点击项的水果名和水果图片资源 id，把它们传入到 Intent 中，最后调用 startActivity() 方法启动 FruitActivity
        final ViewHolder holder = new ViewHolder(view);
        holder.cardView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                int position = holder.getAdapterPosition();
                Fruit fruit = mFruitList.get(position);
                Intent intent = new Intent(mContext, FruitActivity.class);
                intent.putExtra(FruitActivity.FRUIT_NAME, fruit.getName());
                intent.putExtra(FruitActivity.FRUIT_IMAGE_ID, fruit.getImageId());
                mContext.startActivity(intent);
            }
        });
        return holder;
    }
	
    ...

}
```



### 充分利用系统状态栏空间

在 Android 5.0 之前，我们是无法对状态栏的背景或颜色进行操作的，在 Android 5.0 及之后的系统中，使用背景图和状态栏融合的模式，在之前的系统使用普通模式

想要让背景图能够和系统状态栏融合，需要借助 android:fitsSystemWindows 这个属性来实现

在 CoordinatorLayout、AppBarLayout、CollapsingToolbarLayout 这种嵌套结构的布局中，将控件的 android:fitsSystemWindows 属性指定成 true，就表示该控件会出现在系统状态栏里。对应到我们的程序，那就是水果标题栏中的 ImageView 应该设置这个属性了。不过只给 ImageView 设置这个属性是没有用的，我们必须将 ImageView 布局结构中的所有父布局都设置上这个属性才可以，修改 activity_fruit.xml

```java
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true" >

    <android.support.design.widget.AppBarLayout
        android:id="@+id/appBar"
        android:layout_width="match_parent"
        android:layout_height="250dp"
        android:fitsSystemWindows="true" >

        <android.support.design.widget.CollapsingToolbarLayout
            android:id="@+id/collapsing_toolbar"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            app:contentScrim="?attr/colorPrimary"
            app:layout_scrollFlags="scroll|exitUntilCollapsed"
            android:fitsSystemWindows="true" >

            <ImageView
                android:id="@+id/fruit_image_view"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:scaleType="centerCrop"
                app:layout_collapseMode="parallax"
                android:fitsSystemWindows="true" />
                
     ...

</android.support.design.widget.CoordinatorLayout>
```

但是即使我们将 android:fitsSystemWindows 属性都设置好了还是没有用的，因为还必须在程序的主题中将状态栏颜色指定成透明色才行

指定成透明色的方法很简单，在主题中将 android:statusBarColor 属性的值指定成 @android:color/transparent 就可以了。但问题在于，android:statusBarColor 这个属性是从 API 21 也就是 Android 5.0 开始才有的，之前的系统是无法指定这个属性的，那么，系统的差异型功能实现就要从这里开始了

右击 res 目录 -> New -> Directory，创建一个 values-v21 目录，然后右击 values-v21 目录 -> New -> Values resource file ，创建一个 styles.xml 文件

```java
//这里专门定义了一个 FruitActivityTheme 主题，它是专门给 FruitActivity 使用的
<resources>
    
    // FruitActivityTheme 的 parent 主题是 AppTheme，也就是说，它继承了 AppTheme 中的所有特性。然后我们在 FruitActivityTheme 中将状态栏的颜色指定成透明色，由于 values-v21 目录是只有 Android 5.0 及以上的系统才会去读取的，因此这么声明是没有问题的
    <style name="FruitActivityTheme" parent="AppTheme">
        <item name="android:statusBarColor">@android:color/transparent</item>
    </style>
    
</resources>
```

但是在 Android 5.0 之前的系统却无法是别 FruitActivityTheme 这个主题，因此我们还需要对 values/style.xml 文件进行修改

```java
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>
    
    //可以看到这里也定义了一个 FruitActivityTheme 主题，并且 parent 主题也是 AppTheme，但是它的内部是空的，因为 Android 5.0 之前的系统是无法指定状态栏的颜色，因此这里什么都不用做就可以了
    <style name="FruitActivityTheme" parent="AppTheme">
        
    </style>

</resources>
```

最后，我们还需要让 FruitActivity 使用这个主题才可以，修改 AndroidManifest.xml

```java
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.materialtest">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".FruitActivity"
            //使用 android:theme 属性单独给 FruitActivity 指定了 android:theme 这个主题
            android:theme="@style/FruitActivityTheme">
        </activity>
    	...
    </application>
</manifest>
```



## 总结

Material Design 的官方文章 http://material.google.com