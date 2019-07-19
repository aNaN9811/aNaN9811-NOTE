match_parent：当前元素和父元素一样宽

wrap_content：当前元素高度只要刚好能包含里面的内容就行

android:layout_width：制定元素的宽度

android:layout_height：制定元素的高度

android:layout_weight：制定元素的权重

 android:layout_margin="5dp"                                                                                                                                               //使用 layout_margin 属性来让自相之间互留一点间距，这样不至于所有子项都紧贴在一起

<fragment> 标签在布局中添加碎片还需要通过 android:name 属性来显示指明你要添加的碎片类名，注意一定要将类的包名也加上

<TextView>

android:text="Button 1" //制定了元素中显示的文字内容

 android:gravity="center"//指定文字的对齐方式								//可以通过使用 android:gravity 来指定文字的对齐方式，可选值有 top、bottom、left、right、center等，可以用 “|” 来同时指定多个值，这里我们指定的 center，效果等同于 center_vertical | center_horizontal，表示文字在垂直和水平方向都居中对齐

<Button>

android:text="Button"											                  //在布局文件里面设置的文字是 ”Button“，但最终显示结果却是 "BUTTON"，这是由于系统对 Button 中的所有英文字母自动进行大写转换，如果不想要这效果，可以使用下面配置来禁用这一默认特性
//禁用 Button 中的所有英文字母自动进行大写转化
android:textAllCaps="false"

### 创建和加载布局

在活动 onCreate() 方法中 setContentView() 方法给当前活动加载一个布局，在方法中，我们一般都会传入一个布局文件的 id，项目中添加的任何资源都会在R文件中生成一个相应的资源id

```java
public class FirstActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.first_layout);
    }
}
```



### 在 AndroidMainfest 文件中注册

所有的活动都要在 AndroidManifest.xml 中进行注册才能生效，但仅仅注册了活动的程序仍然是不能运行的，必须还要配置主活动，配置主活动的方法：在 <activity> 标签的内部加入 <intent-filter> 标签，并在这个标签中添加这两句声明即可：

```java
<action android:name="android.intent.action.MAIN"/>
<category android:name="android.intent.category.LAUNCHER"/>
```

```java
<activity
    android:name=".FirstActivity"
    android:label="This is FirstActivity">//android:label
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

同时我们可以使用 android:label 指定活动中的标题栏内容，而且还会成为启动器（Launcher）中应用程序显示的名称，代码写在 <activity> 标签里，<intent-filter> 标签之前

如果你的应用中没有声明任何一个活动作为主活动，这个 程序仍然是可以正常安装的，只是你无法在启动器中看到或者打开这个程序，这种程序一般都是作为第三方服务提供其他应用杂内部进行调用



### 在活动中使用 Toast

Toast 是 Android 系统提供的一种非常好的提醒方式，在程序中可以使用它将一些短小的信息通知给用户，这些信息会在一段时间后自动消失，并且不会占用任何屏幕空间。在onCreate方法中添加：

```java
Button button1 = (Button) findViewById(R.id.button_1);
button1.setOnClickListener(new View.OnClickListener(){
    @Override
    public void onClick(View v){
        //以下两行若改为finish()；则点击按钮成为销毁活动，相当于点击Back
        Toast.makeText(FirstActivity.this,"You clicker Button 1",
                Toast.LENGTH_SHORT).show();
    }
});
```

findViewById() 方法获取到在布局文件中定义的元素，我们传入 R.id.button_1 来得到按钮实例，返回的是一个 View 的对象，我们需要向下转型将它转成 Button 对象

得到按钮的实例之后，调用 setOnClickListener() 方法为按钮注册一个监听器，点击按钮就会执行监听器中的 onClick() 方法

Toast 的用法非常简单，通过静态方法 makeText() 创建出一个 Toast 对象，然后调用 show() 将 Toast 显示出来就可以了

makeText() 需要传入3个参数，第一个参数是 Context ，也就是 Toast 要求的上下文，由于活动本身就是 Context 对象，因此这里直接传入 FirstActivity.this，第二个参数是 Toast 显示的文本内容，第三个参数是 Toast 现实的时长，有两个内置常量可以选择 Toast.LENGTH_SHORT 和 Toast.LENGTH_LONG



### 在活动中使用 Menu

```java
<item
    android:id="@+id/add_item"
    android:title="Add"/>
<item
    android:id ="@+id/remove_item"
    android:title="Remove"/>
```

<item>标签就是用来创建具体的某一个菜单项，然后通过 android:id 给这个菜单项指定一个唯一的标识符，通过 android:title 给这个菜单项指定一个名称

使用 Ctrl + o 重写 onCreateOptionsMenu() 方法

```java
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.main,menu);
    return true;
}
```

inflate() 方法接收两个参数，第一个参数：指定一个资源文件来创建菜单，第二个参数：用于指定菜单项将添加到哪一个Menu对象当中

返回 true 表示允许创建的菜单显示出来，返回 false 创建的菜单无法显示

仅仅让菜单显示是不够，需要能用，因此还要再定义菜单响应事件，在活动中重写 onOptionsItemSelected() 方法

```java
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()){
        case R.id.add_item:
            Toast.makeText(this, "You clicked Add", Toast.LENGTH_SHORT).show();
            break;
        case R.id.remove_item:
            Toast.makeText(this, "You clicked Remove", Toast.LENGTH_SHORT).show();
            break;
        default:
    }
    return true;
}
```



### 使用显示 Intent

Intent是Android 程序中各组件之间进行交互的一种重要方式，它不仅可以指明当前组件想要执行的动作，还可以在不同组件想要执行的动作，还可以在不同组件之间传递数据。Intent 一般可被用于启动活动、启动服务以及发送广播等场景

Intent 大致可以分为两种：显示 Intent 和隐式 Intent。Intent 有过个构造函数的重载，其中一个是 Intent （Context packageContext, Class<?>cls）第一个参数 Context 要求提供一个启动活动的上下文，第二个参数 Class 则是指定想要启动的目标活动

Activity 类中提供了一个 startActivity（）方法，这个方法是专门用于启动活动的

```java
button1.setOnClickListener(new View.OnClickListener(){
    @Override
    public void onClick(View v){
        //传入 FirstActivity.this 作为上下文
        //传入 SecondActivity.class 作为目标活动
        //“意图”非常明显，即在 FirstActivity 这个活动基础上打开 SecondActivity 这个活动
        Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
        //通过 startActivity() 方法来执行这个 Intent
        startActivity(intent);
    }
});
```



### 使用隐式 Intent

隐式 Intent 并不明确我么你想要启动哪一个活动，而是制定了一系列更为抽象的 action 和 category 等信息，然后交由系统去分析这个 Intent，并帮我么那找出合适的活动去启动

通过在 <avtivity> 标签下配置 <intent-filter> 的内容，可以指定当前活动能够响应的 action 和 category，在 AndroidManifest.xml 添加以下代码

```java
<activity android:name=".SecondActivity">
    <intent-filter>
    	//指明当前活动可以响应com.activitytest.ACTION_START 这个 action
        <action android:name="com.example.activitytest.ACTION_START"/>
        //该标签包含了一些附加信息，更加精确地指明了当前的活动能够响应的 Intent 中还可能带有的 category
        //android.intent.category.DEFAULT 是一种默认的 category，在调用的 stattActivity() 方法的时候会自动将这个 category 添加到 Intent 中
        <category android:name="android.intent.category.DEFAULT"/>
        //只有 <action> 和 <category> 中的内容同时能够匹配上 Intent 中指定的 action 和 category 这个活动才能响应该 Intent
    </intent-filter>
</activity>
```

修改 FirstActivity 中按钮的点击事件：

```java
button1.setOnClickListener(new View.OnClickListener(){
    @Override
    public void onClick(View v){
        //直接传入 action 即启动该活动，category 则被默认添加了
        Intent intent = new Intent("com.example.activitytest.ACTION_START");
        startActivity(intent);
    }
});
```

每个 Intent 中只能指定一个 action，但却能指定多个 category

当在活动中添加一个 category 直接运行的话会遇到程序崩溃，因为没有任何响应我们的 Intent，当在 SecondActivity 的 <intent-filtert> 标签中再添加一个 category 的声明，再次重新运行就正常了

```java
button1.setOnClickListener(new View.OnClickListener(){
    @Override
    public void onClick(View v){
        Intent intent = new Intent("com.example.activitytest.ACTION_START");
        intent.addCategory("com.example.activitytest.MY_CATEGORY");//该方法添加一个 category
        startActivity(intent);
    }
});
```

```java
<activity android:name=".SecondActivity">
    <intent-filter>
        <action android:name="com.example.activitytest.ACTION_START"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <category android:name="com.example.activitytest.MY_CATEGORY"/>//添加一个 category 声明
    </intent-filter>
</activity>
```



### 更多隐式 Intent 的用法

使用隐式 Intent，我们不仅可以启动自己程序内的活动，还可以启动其他程序的活动，这使得 Android 多个应用程序之间的功能共享成为了可能

修改 FirstActivity 中按钮点击事件的代码：

```java
button1.setOnClickListener(new View.OnClickListener(){
    @Override
    public void onClick(View v){
        //首先制定了 Intent 的 action 是 Intent.ACTION_VIEW，这是一个 Android 系统内置的动作，其常量值为 android.intent.action.VIEW
        Intent intent = new Intent(Intent.ACTION_VIEW);
        //通过 Uri.parse() 方法，将一个网址字符串解析成一个 Uri 对象，在调用 setData() 方法将这个 Uri 对象传递进去
        intent.setData(Uri.parse("http://www.baidu.com"));
        startActivity(intent);
    }
});
```

setData()：接收一个 Uri 对象，主要用于指定当前 Intent 正在操作的数据， 而这些数据通常都是以字符串形式传入到 Uri.parse() 方法中解析产生的

我们还可以在 <intent-filter> 标签中在配置一个 <data> 标签，用于更加精确地指定当前活动能够响应什么类型的数据。<data> 标签中主要可以配置一下内容

- android:scheme。用于指定数据的协议部分，如上例中的 http 部分
- android:host。用于指定数据的主机名部分，如上例中的 www.baidu.com 部分
- android:port。用于指定数据的端口部分，一般紧随在主机名之后
- android:path。用于指定主机名和端口之后的部分，如一段王志忠那个跟在域名之后的内容
- android:mimeType。用于指定可以处理的数据类型，允许使用通配符的方式进行指定

只有 <data> 标签中指定的内容和 Intent 中携带的 Data 完全一致时，当前活动才能够响应该 Intent 。不过一般在 <data> 标签中都不会指定过多的内容

在 com.example.activitytest 包新建 ThirdActivity，修改 third_layout.xml：

```java
<LenearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <Button
        android:id="@+id/button_3"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Button 3"
        />
</LenearLayout>
```

修改 AndroidManifest.xml 中 ThirdActivity 的注册信息

```java
<activity android:name=".ThirdActivity">
	//配置了当前活动能够响应的 action 和 Intent.ACTION_VIEW 的常量值，而 category 则指定了默认的 category
    <intent-filter>
        <action android:name="android.intent.action.VIEW"/>
        <category android:name="android.intent.category.DEFAULT"/>
        //指定了 http 协议
        <data android:scheme="http"/>
    </intent-filter>
</activity>
```

除了 http 协议外，geo 表示显示地理位置、tel 表示拨打电话

```java
button1.setOnClickListener(new View.OnClickListener(){
    @Override
    public void onClick(View v){
        //指定了 Intent 的 action 是 Intent.ACTION_DIAL，这是 Android 系统的内置动作
        Intent intent = new Intent(Intent.ACTION_DIAL);
        //data 部分指定了协议是 tel ，号码是 10086
        intent.setData(Uri.parse("tel:10086"));
        startActivity(intent);
    }
});
```



Android 控件的可见属性，所有的 Android 控件都有这个属性，可以通过 android:visibility 进行制定，可选值有3种：visible（表示控件是可见的，这个只是默认值，不指定 android:visibility 时，控件都是可见的）、invisible（表示控件不可见的，但是它仍然占据着原来的位置和大小，可以理解成控件编程透明状态了） 和 gone（表示控件不仅不可见，而且不再占用任何屏幕空间），此外还可以通过代码来设置控件的可见性，使用 setVisibililty() 方法，可以传入 View.WISIBLE、View.INVISIBLE 和 View.GONE 这三种值