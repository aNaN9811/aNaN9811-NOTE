# Glide 4概述

刚才有说到，有些朋友觉得Glide 4相对于Glide 3改动非常大，其实不然。之所以大家会有这种错觉，是因为你将Glide 3的用法直接搬到Glide 4中去使用，结果IDE全面报错，然后大家可能就觉得Glide 4的用法完全变掉了。

其实Glide 4相对于Glide 3的变动并不大，只是你还没有了解它的变动规则而已。一旦你掌握了Glide 4的变动规则之后，你会发现大多数Glide 3的用法放到Glide 4上都还是通用的。

我对Glide 4进行了一个大概的研究之后，发现Glide 4并不能算是有什么突破性的升级，而更多是一些API工整方面的优化。相比于Glide 3的API，Glide 4进行了更加科学合理地调整，使得易读性、易写性、可扩展性等方面都有了不错的提升。但如果你已经对Glide 3非常熟悉的话，并不是就必须要切换到Glide 4上来，因为Glide 4上能实现的功能Glide 3也都能实现，而且Glide 4在性能方面也并没有什么提升。

但是对于新接触Glide的朋友而言，那就没必要再去学习Glide 3了，直接上手Glide 4就是最佳的选择了。

好了，对Glide 4进行一个基本的概述之后，接下来我们就要正式开始学习它的用法了。刚才我已经说了，Glide 4的用法相对于Glide 3其实改动并不大。在前面的七篇文章中，我们已经学习了Glide 3的基本用法、缓存机制、回调与监听、图片变换、自定义模块等用法，那么今天这篇文章的目标就很简单了，就是要掌握如何在Glide 4上实现之前所学习过的所有功能，那么我们现在就开始吧。

# 开始

要想使用Glide，首先需要将这个库引入到我们的项目当中。新建一个Glide4Test项目，然后在app/build.gradle文件当中添加如下依赖：

```groovy
dependencies {
    implementation 'com.github.bumptech.glide:glide:4.4.0'
    annotationProcessor 'com.github.bumptech.glide:compiler:4.4.0'
}
```

- 1
- 2
- 3
- 4

注意，相比于Glide 3，这里要多添加一个compiler的库，这个库是用于生成Generated API的，待会我们会讲到它。

另外，Glide中需要用到网络功能，因此你还得在AndroidManifest.xml中声明一下网络权限才行：

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

- 1

就是这么简单，然后我们就可以自由地使用Glide中的任意功能了。

# 加载图片

现在我们就来尝试一下如何使用Glide来加载图片吧。比如这是一张图片的地址：

```html
http://guolin.tech/book.png
```

- 1

然后我们想要在程序当中去加载这张图片。

那么首先打开项目的布局文件，在布局当中加入一个Button和一个ImageView，如下所示：

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Load Image"
        android:onClick="loadImage"
        />

    <ImageView
        android:id="@+id/image_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
    
</LinearLayout>
```

- 1
- 2
- 3
- 4
- 5
- 6
- 7
- 8
- 9
- 10
- 11
- 12
- 13
- 14
- 15
- 16
- 17
- 18

为了让用户点击Button的时候能够将刚才的图片显示在ImageView上，我们需要修改MainActivity中的代码，如下所示：

```java
public class MainActivity extends AppCompatActivity {

    ImageView imageView;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);

        setContentView(R.layout.activity_main);

        imageView = (ImageView) findViewById(R.id.image_view);

    }

    public void loadImage(View view) {
        String url = "http://guolin.tech/book.png";
        Glide.with(this).load(url).into(imageView);
    }

}
```

- 1
- 2
- 3
- 4
- 5
- 6
- 7
- 8
- 9
- 10
- 11
- 12
- 13
- 14
- 15
- 16
- 17

没错，就是这么简单。现在我们来运行一下程序，效果如下图所示：



![img](https://img-blog.csdn.net/20171216175441966)



可以看到，一张网络上的图片已经被成功下载，并且展示到ImageView上了。

你会发现，到目前为止，Glide 4的用法和Glide 3是完全一样的，实际上核心的代码就只有这一行而已：

```java
Glide.with(this).load(url).into(imageView);
```

- 1

仍然还是传统的三步走：先with()，再load()，最后into()。对这行代码的解读，我在 [Android图片加载框架最全解析（一），Glide的基本用法](http://blog.csdn.net/guolin_blog/article/details/53759439) 这篇文章中讲解的很清楚了，这里就不再赘述。

好了，现在你已经成功入门Glide 4了，那么接下来就让我们学习一下Glide 4的更多用法吧。

# 占位图

观察刚才加载网络图片的效果，你会发现，点击了Load Image按钮之后，要稍微等一会图片才会显示出来。这其实很容易理解，因为从网络上下载图片本来就是需要时间的。那么我们有没有办法再优化一下用户体验呢？当然可以，Glide提供了各种各样非常丰富的API支持，其中就包括了占位图功能。

顾名思义，占位图就是指在图片的加载过程中，我们先显示一张临时的图片，等图片加载出来了再替换成要加载的图片。

下面我们就来学习一下Glide占位图功能的使用方法，首先我事先准备好了一张loading.jpg图片，用来作为占位图显示。然后修改Glide加载部分的代码，如下所示：

```java
RequestOptions options = new RequestOptions()
        .placeholder(R.drawable.loading);

Glide.with(this)
     .load(url)
     .apply(options)
     .into(imageView);
```

- 1
- 2
- 3
- 4
- 5
- 6

没错，就是这么简单。这里我们先创建了一个RequestOptions对象，然后调用它的placeholder()方法来指定占位图，再将占位图片的资源id传入到这个方法中。最后，在Glide的三步走之间加入一个apply()方法，来应用我们刚才创建的RequestOptions对象。

不过如果你现在重新运行一下代码并点击Load Image，很可能是根本看不到占位图效果的。因为Glide有非常强大的缓存机制，我们刚才加载图片的时候Glide自动就已经将它缓存下来了，下次加载的时候将会直接从缓存中读取，不会再去网络下载了，因而加载的速度非常快，所以占位图可能根本来不及显示。

因此这里我们还需要稍微做一点修改，来让占位图能有机会显示出来，修改代码如下所示：

```java
RequestOptions options = new RequestOptions()
        .placeholder(R.drawable.loading)
        .diskCacheStrategy(DiskCacheStrategy.NONE);

Glide.with(this)
     .load(url)
     .apply(options)
     .into(imageView);
```

- 1
- 2
- 3
- 4
- 5
- 6
- 7

可以看到，这里在RequestOptions对象中又串接了一个diskCacheStrategy()方法，并传入DiskCacheStrategy.NONE参数，这样就可以禁用掉Glide的缓存功能。

关于Glide缓存方面的内容我们待会儿会进行更详细的讲解，这里只是为了测试占位图功能而加的一个额外配置，暂时你只需要知道禁用缓存必须这么写就可以了。

现在重新运行一下代码，效果如下图所示：



![img](https://img-blog.csdn.net/20171216180317519)



可以看到，当点击Load Image按钮之后会立即显示一张占位图，然后等真正的图片加载完成之后会将占位图替换掉。

除了这种加载占位图之外，还有一种异常占位图。异常占位图就是指，如果因为某些异常情况导致图片加载失败，比如说手机网络信号不好，这个时候就显示这张异常占位图。

异常占位图的用法相信你已经可以猜到了，首先准备一张error.jpg图片，然后修改Glide加载部分的代码，如下所示：

```java
RequestOptions options = new RequestOptions()
        .placeholder(R.drawable.ic_launcher_background)
        .error(R.drawable.error)
        .diskCacheStrategy(DiskCacheStrategy.NONE);

Glide.with(this)
     .load(url)
     .apply(options)
     .into(imageView);
```

- 1
- 2
- 3
- 4
- 5
- 6
- 7
- 8

很简单，这里又串接了一个error()方法就可以指定异常占位图了。

其实看到这里，如果你熟悉Glide 3的话，相信你已经掌握Glide 4的变化规律了。在Glide 3当中，像placeholder()、error()、diskCacheStrategy()等等一系列的API，都是直接串联在Glide三步走方法中使用的。

而Glide 4中引入了一个RequestOptions对象，将这一系列的API都移动到了RequestOptions当中。这样做的好处是可以使我们摆脱冗长的Glide加载语句，而且还能进行自己的API封装，因为RequestOptions是可以作为参数传入到方法中的。

比如你就可以写出这样的Glide加载工具类：

```java
public class GlideUtil {

    public static void load(Context context,
                            String url,
                            ImageView imageView,
                            RequestOptions options) {
        
        Glide.with(context)
             .load(url)
             .apply(options)
             .into(imageView);
    }

}
```

- 1
- 2
- 3
- 4
- 5
- 6
- 7
- 8
- 9
- 10
- 11
- 12
- 13

# 指定图片大小

实际上，使用Glide在大多数情况下我们都是不需要指定图片大小的，因为Glide会自动根据ImageView的大小来决定图片的大小，以此保证图片不会占用过多的内存从而引发OOM。

不过，如果你真的有这样的需求，必须给图片指定一个固定的大小，Glide仍然是支持这个功能的。修改Glide加载部分的代码，如下所示：

```java
RequestOptions options = new RequestOptions()
        .override(200, 100);

Glide.with(this)
     .load(url)
     .apply(options)
     .into(imageView);
```

- 1
- 2
- 3
- 4
- 5
- 6

仍然非常简单，这里使用override()方法指定了一个图片的尺寸。也就是说，Glide现在只会将图片加载成200*100像素的尺寸，而不会管你的ImageView的大小是多少了。

如果你想加载一张图片的原始尺寸的话，可以使用Target.SIZE_ORIGINAL关键字，如下所示：

```java
RequestOptions options = new RequestOptions()
        .override(Target.SIZE_ORIGINAL);

Glide.with(this)
     .load(url)
     .apply(options)
     .into(imageView);
```

- 1
- 2
- 3
- 4
- 5
- 6

这样的话，Glide就不会再去自动压缩图片，而是会去加载图片的原始尺寸。当然，这种写法也会面临着更高的OOM风险。

# 缓存机制

Glide的缓存设计可以说是非常先进的，考虑的场景也很周全。在缓存这一功能上，Glide又将它分成了两个模块，一个是内存缓存，一个是硬盘缓存。

这两个缓存模块的作用各不相同，内存缓存的主要作用是防止应用重复将图片数据读取到内存当中，而硬盘缓存的主要作用是防止应用重复从网络或其他地方重复下载和读取数据。

内存缓存和硬盘缓存的相互结合才构成了Glide极佳的图片缓存效果，那么接下来我们就来分别学习一下这两种缓存的使用方法。

首先来看内存缓存。

你要知道，默认情况下，Glide自动就是开启内存缓存的。也就是说，当我们使用Glide加载了一张图片之后，这张图片就会被缓存到内存当中，只要在它还没从内存中被清除之前，下次使用Glide再加载这张图片都会直接从内存当中读取，而不用重新从网络或硬盘上读取了，这样无疑就可以大幅度提升图片的加载效率。比方说你在一个RecyclerView当中反复上下滑动，RecyclerView中只要是Glide加载过的图片都可以直接从内存当中迅速读取并展示出来，从而大大提升了用户体验。

而Glide最为人性化的是，你甚至不需要编写任何额外的代码就能自动享受到这个极为便利的内存缓存功能，因为Glide默认就已经将它开启了。

那么既然已经默认开启了这个功能，还有什么可讲的用法呢？只有一点，如果你有什么特殊的原因需要禁用内存缓存功能，Glide对此提供了接口：

```java
RequestOptions options = new RequestOptions()
        .skipMemoryCache(true);

Glide.with(this)
     .load(url)
     .apply(options)
     .into(imageView);
```

- 1
- 2
- 3
- 4
- 5
- 6

可以看到，只需要调用skipMemoryCache()方法并传入true，就表示禁用掉Glide的内存缓存功能。

接下来我们开始学习硬盘缓存方面的内容。

其实在刚刚学习占位图功能的时候，我们就使用过硬盘缓存的功能了。当时为了禁止Glide对图片进行硬盘缓存而使用了如下代码：

```java
RequestOptions options = new RequestOptions()
        .diskCacheStrategy(DiskCacheStrategy.NONE);

Glide.with(this)
     .load(url)
     .apply(options)
     .into(imageView);
```

- 1
- 2
- 3
- 4
- 5
- 6

调用diskCacheStrategy()方法并传入DiskCacheStrategy.NONE，就可以禁用掉Glide的硬盘缓存功能了。

这个diskCacheStrategy()方法基本上就是Glide硬盘缓存功能的一切，它可以接收五种参数：

- DiskCacheStrategy.NONE： 表示不缓存任何内容。
- DiskCacheStrategy.DATA： 表示只缓存原始图片。
- DiskCacheStrategy.RESOURCE： 表示只缓存转换过后的图片。
- DiskCacheStrategy.ALL ： 表示既缓存原始图片，也缓存转换过后的图片。
- DiskCacheStrategy.AUTOMATIC： 表示让Glide根据图片资源智能地选择使用哪一种缓存策略（默认选项）。

其中，DiskCacheStrategy.DATA对应Glide 3中的DiskCacheStrategy.SOURCE，DiskCacheStrategy.RESOURCE对应Glide 3中的DiskCacheStrategy.RESULT。而DiskCacheStrategy.AUTOMATIC是Glide 4中新增的一种缓存策略，并且在不指定diskCacheStrategy的情况下默认使用就是的这种缓存策略。

上面五种参数的解释本身并没有什么难理解的地方，但是关于转换过后的图片这个概念大家可能需要了解一下。就是当我们使用Glide去加载一张图片的时候，Glide默认并不会将原始图片展示出来，而是会对图片进行压缩和转换（我们会在稍后学习这方面的内容）。总之就是经过种种一系列操作之后得到的图片，就叫转换过后的图片。

好的，关于Glide 4硬盘缓存的内容就讲到这里。想要了解更多Glide缓存方面的知识，可以参考 [Android图片加载框架最全解析（三），深入探究Glide的缓存机制](http://blog.csdn.net/guolin_blog/article/details/54895665) 这篇文章。

# 指定加载格式

我们都知道，Glide其中一个非常亮眼的功能就是可以加载GIF图片，而同样作为非常出色的图片加载框架的Picasso是不支持这个功能的。

而且使用Glide加载GIF图并不需要编写什么额外的代码，Glide内部会自动判断图片格式。比如我们将加载图片的URL地址改成一张GIF图，如下所示：

```java
Glide.with(this)
     .load("http://guolin.tech/test.gif")
     .into(imageView);
```

- 1
- 2
- 3

现在重新运行一下代码，效果如下图所示：



![img](https://img-blog.csdn.net/20171216181633717)



也就是说，不管我们传入的是一张普通图片，还是一张GIF图片，Glide都会自动进行判断，并且可以正确地把它解析并展示出来。

但是如果我想指定加载格式该怎么办呢？就比如说，我希望加载的这张图必须是一张静态图片，我不需要Glide自动帮我判断它到底是静图还是GIF图。

想实现这个功能仍然非常简单，我们只需要再串接一个新的方法就可以了，如下所示：

```java
Glide.with(this)
     .asBitmap()
     .load("http://guolin.tech/test.gif")
     .into(imageView);
```

- 1
- 2
- 3
- 4

可以看到，这里在with()方法的后面加入了一个asBitmap()方法，这个方法的意思就是说这里只允许加载静态图片，不需要Glide去帮我们自动进行图片格式的判断了。如果你传入的还是一张GIF图的话，Glide会展示这张GIF图的第一帧，而不会去播放它。

熟悉Glide 3的朋友对asBitmap()方法肯定不会陌生对吧？但是千万不要觉得这里就没有陷阱了，在Glide 3中的语法是先load()再asBitmap()的，而在Glide 4中是先asBitmap()再load()的。乍一看可能分辨不出来有什么区别，但如果你写错了顺序就肯定会报错了。

那么类似地，既然我们能强制指定加载静态图片，就也能强制指定加载动态图片，对应的方法是asGif()。而Glide 4中又新增了asFile()方法和asDrawable()方法，分别用于强制指定文件格式的加载和Drawable格式的加载，用法都比较简单，就不再进行演示了。

# 回调与监听

回调与监听这部分的内容稍微有点多，我们分成四部分来学习一下。

### 1. into()方法

我们都知道Glide的into()方法中是可以传入ImageView的。那么into()方法还可以传入别的参数吗？我们可以让Glide加载出来的图片不显示到ImageView上吗？答案是肯定的，这就需要用到自定义Target功能。

Glide中的Target功能多样且复杂，下面我就先简单演示一种SimpleTarget的用法吧，代码如下所示：

```java
SimpleTarget<Drawable> simpleTarget = new SimpleTarget<Drawable>() {
    
    @Override
    public void onResourceReady(Drawable resource, Transition<? super Drawable> transition) {
        imageView.setImageDrawable(resource);
    }
};

public void loadImage(View view) {
    Glide.with(this)
         .load("http://guolin.tech/book.png")
         .into(simpleTarget);
}
```

- 1
- 2
- 3
- 4
- 5
- 6
- 7
- 8
- 9
- 10
- 11
- 12

这里我们创建了一个SimpleTarget的实例，并且指定它的泛型是Drawable，然后重写了onResourceReady()方法。在onResourceReady()方法中，我们就可以获取到Glide加载出来的图片对象了，也就是方法参数中传过来的Drawable对象。有了这个对象之后你可以使用它进行任意的逻辑操作，这里我只是简单地把它显示到了ImageView上。

SimpleTarget的实现创建好了，那么只需要在加载图片的时候将它传入到into()方法中就可以了。

这里限于篇幅原因我只演示了自定义Target的简单用法，想学习更多相关的内容可以去阅读 [Android图片加载框架最全解析（四），玩转Glide的回调与监听](http://blog.csdn.net/guolin_blog/article/details/70215985) 。

### 2. preload()方法

Glide加载图片虽说非常智能，它会自动判断该图片是否已经有缓存了，如果有的话就直接从缓存中读取，没有的话再从网络去下载。但是如果我希望提前对图片进行一个预加载，等真正需要加载图片的时候就直接从缓存中读取，不想再等待慢长的网络加载时间了，这该怎么办呢？

不用担心，Glide专门给我们提供了预加载的接口，也就是preload()方法，我们只需要直接使用就可以了。

preload()方法有两个方法重载，一个不带参数，表示将会加载图片的原始尺寸，另一个可以通过参数指定加载图片的宽和高。

preload()方法的用法也非常简单，直接使用它来替换into()方法即可，如下所示：

```java
Glide.with(this)
     .load("http://guolin.tech/book.png")
     .preload();
```

- 1
- 2
- 3

调用了预加载之后，我们以后想再去加载这张图片就会非常快了，因为Glide会直接从缓存当中去读取图片并显示出来，代码如下所示：

```java
Glide.with(this)
     .load("http://guolin.tech/book.png")
     .into(imageView);
```

- 1
- 2
- 3

### 3. submit()方法

一直以来，我们使用Glide都是为了将图片显示到界面上。虽然我们知道Glide会在图片的加载过程中对图片进行缓存，但是缓存文件到底是存在哪里的，以及如何去直接访问这些缓存文件？我们都还不知道。

其实Glide将图片加载接口设计成这样也是希望我们使用起来更加的方便，不用过多去考虑底层的实现细节。但如果我现在就是想要去访问图片的缓存文件该怎么办呢？这就需要用到submit()方法了。

submit()方法其实就是对应的Glide 3中的downloadOnly()方法，和preload()方法类似，submit()方法也是可以替换into()方法的，不过submit()方法的用法明显要比preload()方法复杂不少。这个方法只会下载图片，而不会对图片进行加载。当图片下载完成之后，我们可以得到图片的存储路径，以便后续进行操作。

那么首先我们还是先来看下基本用法。submit()方法有两个方法重载：

- submit()
- submit(int width, int height)

其中submit()方法是用于下载原始尺寸的图片，而submit(int width, int height)则可以指定下载图片的尺寸。

这里就以submit()方法来举例。当调用了submit()方法后会立即返回一个FutureTarget对象，然后Glide会在后台开始下载图片文件。接下来我们调用FutureTarget的get()方法就可以去获取下载好的图片文件了，如果此时图片还没有下载完，那么get()方法就会阻塞住，一直等到图片下载完成才会有值返回。

下面我们通过一个例子来演示一下吧，代码如下所示：

```java
public void downloadImage() {

    new Thread(new Runnable() {
        
        @Override
        public void run() {
            try {

                String url = "http://www.guolin.tech/book.png";

                final Context context = getApplicationContext();
                
                FutureTarget<File> target = Glide.with(context)
                        .asFile()
                        .load(url)
                        .submit();
                
                final File imageFile = target.get();
                
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        Toast.makeText(context, imageFile.getPath(), Toast.LENGTH_LONG).show();
                    }
                });
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }).start();
}
```

- 1
- 2
- 3
- 4
- 5
- 6
- 7
- 8
- 9
- 10
- 11
- 12
- 13
- 14
- 15
- 16
- 17
- 18
- 19
- 20
- 21
- 22
- 23
- 24

这段代码稍微有一点点长，我带着大家解读一下。首先，submit()方法必须要用在子线程当中，因为刚才说了FutureTarget的get()方法是会阻塞线程的，因此这里的第一步就是new了一个Thread。在子线程当中，我们先获取了一个Application Context，这个时候不能再用Activity作为Context了，因为会有Activity销毁了但子线程还没执行完这种可能出现。

接下来就是Glide的基本用法，只不过将into()方法替换成了submit()方法，并且还使用了一个asFile()方法来指定加载格式。submit()方法会返回一个FutureTarget对象，这个时候其实Glide已经开始在后台下载图片了，我们随时都可以调用FutureTarget的get()方法来获取下载的图片文件，只不过如果图片还没下载好线程会暂时阻塞住，等下载完成了才会把图片的File对象返回。

最后，我们使用runOnUiThread()切回到主线程，然后使用Toast将下载好的图片文件路径显示出来。

现在重新运行一下代码，效果如下图所示。



![img](https://img-blog.csdn.net/20171216185602206)



这样我们就能清晰地看出来图片完整的缓存路径是什么了。

### 4. listener()方法

其实listener()方法的作用非常普遍，它可以用来监听Glide加载图片的状态。举个例子，比如说我们刚才使用了preload()方法来对图片进行预加载，但是我怎样确定预加载有没有完成呢？还有如果Glide加载图片失败了，我该怎样调试错误的原因呢？答案都在listener()方法当中。

下面来看下listener()方法的基本用法吧，不同于刚才几个方法都是要替换into()方法的，listener()是结合into()方法一起使用的，当然也可以结合preload()方法一起使用。最基本的用法如下所示：

```java
Glide.with(this)
     .load("http://www.guolin.tech/book.png")
     .listener(new RequestListener<Drawable>() {
         @Override
         public boolean onLoadFailed(@Nullable GlideException e, Object model, Target<Drawable> target, boolean isFirstResource) {
             return false;
         }
         @Override
         public boolean onResourceReady(Drawable resource, Object model, Target<Drawable> target, DataSource dataSource, boolean isFirstResource) {
             return false;
         }
     })
     .into(imageView);
```

- 1
- 2
- 3
- 4
- 5
- 6
- 7
- 8
- 9
- 10
- 11
- 12
- 13
- 14

这里我们在into()方法之前串接了一个listener()方法，然后实现了一个RequestListener的实例。其中RequestListener需要实现两个方法，一个onResourceReady()方法，一个onLoadFailed()方法。从方法名上就可以看出来了，当图片加载完成的时候就会回调onResourceReady()方法，而当图片加载失败的时候就会回调onLoadFailed()方法，onLoadFailed()方法中会将失败的GlideException参数传进来，这样我们就可以定位具体失败的原因了。

没错，listener()方法就是这么简单。不过还有一点需要处理，onResourceReady()方法和onLoadFailed()方法都有一个布尔值的返回值，返回false就表示这个事件没有被处理，还会继续向下传递，返回true就表示这个事件已经被处理掉了，从而不会再继续向下传递。举个简单点的例子，如果我们在RequestListener的onResourceReady()方法中返回了true，那么就不会再回调Target的onResourceReady()方法了。

关于回调与监听的内容就讲这么多吧，如果想要学习更多深入的内容以及源码解析，还是请参考这篇文章 [Android图片加载框架最全解析（四），玩转Glide的回调与监听](http://blog.csdn.net/guolin_blog/article/details/70215985) 。

# 图片变换

图片变换的意思就是说，Glide从加载了原始图片到最终展示给用户之前，又进行了一些变换处理，从而能够实现一些更加丰富的图片效果，如图片圆角化、圆形化、模糊化等等。

添加图片变换的用法非常简单，我们只需要在RequestOptions中串接transforms()方法，并将想要执行的图片变换操作作为参数传入transforms()方法即可，如下所示：

```java
RequestOptions options = new RequestOptions()
        .transforms(...);

Glide.with(this)
     .load(url)
     .apply(options)
     .into(imageView);
```

- 1
- 2
- 3
- 4
- 5
- 6

至于具体要进行什么样的图片变换操作，这个通常都是需要我们自己来写的。不过Glide已经内置了几种图片变换操作，我们可以直接拿来使用，比如CenterCrop、FitCenter、CircleCrop等。

但所有的内置图片变换操作其实都不需要使用transform()方法，Glide为了方便我们使用直接提供了现成的API：

```java
RequestOptions options = new RequestOptions()
        .centerCrop();

RequestOptions options = new RequestOptions()
        .fitCenter();

RequestOptions options = new RequestOptions()
        .circleCrop();
```

- 1
- 2
- 3
- 4
- 5
- 6
- 7
- 8

当然，这些内置的图片变换API其实也只是对transform()方法进行了一层封装而已，它们背后的源码仍然还是借助transform()方法来实现的。

这里我们就选择其中一种内置的图片变换操作来演示一下吧，circleCrop()方法是用来对图片进行圆形化裁剪的，我们动手试一下，代码如下所示：

```java
String url = "http://guolin.tech/book.png";

RequestOptions options = new RequestOptions()
        .circleCrop();

Glide.with(this)
     .load(url)
     .apply(options)
     .into(imageView);
```

- 1
- 2
- 3
- 4
- 5
- 6
- 7

重新运行一下程序并点击加载图片按钮，效果如下图所示。



![img](https://img-blog.csdn.net/20171216133453984)



可以看到，现在展示的图片是对原图进行圆形化裁剪后得到的图片。

当然，除了使用内置的图片变换操作之外，我们完全可以自定义自己的图片变换操作。理论上，在对图片进行变换这个步骤中我们可以进行任何的操作，你想对图片怎么样都可以。包括圆角化、圆形化、黑白化、模糊化等等，甚至你将原图片完全替换成另外一张图都是可以的。

不过由于这部分内容相对于Glide 3没有任何的变化，因此就不再重复进行讲解了。想学习自定义图片变换操作的朋友们可以参考这篇文章 [Android图片加载框架最全解析（五），Glide强大的图片变换功能](http://blog.csdn.net/guolin_blog/article/details/71524668) 。

关于图片变换，最后我们再来看一个非常优秀的开源库，glide-transformations。它实现了很多通用的图片变换效果，如裁剪变换、颜色变换、模糊变换等等，使得我们可以非常轻松地进行各种各样的图片变换。

glide-transformations的项目主页地址是 <https://github.com/wasabeef/glide-transformations> 。

下面我们就来体验一下这个库的强大功能吧。首先需要将这个库引入到我们的项目当中，在app/build.gradle文件当中添加如下依赖：

```groovy
dependencies {
    implementation 'jp.wasabeef:glide-transformations:3.0.1'
}
```

- 1
- 2
- 3

我们可以对图片进行单个变换处理，也可以将多种图片变换叠加在一起使用。比如我想同时对图片进行模糊化和黑白化处理，就可以这么写：

```java
String url = "http://guolin.tech/book.png";

RequestOptions options = new RequestOptions()
        .transforms(new BlurTransformation(), new GrayscaleTransformation());

Glide.with(this)
     .load(url)
     .apply(options)
     .into(imageView);
```

- 1
- 2
- 3
- 4
- 5
- 6
- 7

可以看到，同时执行多种图片变换的时候，只需要将它们都传入到transforms()方法中即可。现在重新运行一下程序，效果如下图所示。



![img](https://img-blog.csdn.net/20171216140213292)



当然，这只是glide-transformations库的一小部分功能而已，更多的图片变换效果你可以到它的GitHub项目主页去学习。

# 自定义模块

自定义模块属于Glide中的高级功能，同时也是难度比较高的一部分内容。

这里我不可能在这一篇文章中将自定义模块的内容全讲一遍，限于篇幅的限制我只能讲一讲Glide 4中变化的这部分内容。关于Glide自定义模块的全部内容，请大家去参考 [Android图片加载框架最全解析（六），探究Glide的自定义模块功能](http://blog.csdn.net/guolin_blog/article/details/78179422) 这篇文章。

自定义模块功能可以将更改Glide配置，替换Glide组件等操作独立出来，使得我们能轻松地对Glide的各种配置进行自定义，并且又和Glide的图片加载逻辑没有任何交集，这也是一种低耦合编程方式的体现。下面我们就来学习一下自定义模块要如何实现。

首先定义一个我们自己的模块类，并让它继承自AppGlideModule，如下所示：

```java
@GlideModule
public class MyAppGlideModule extends AppGlideModule {

    @Override
    public void applyOptions(Context context, GlideBuilder builder) {
        
    }

    @Override
    public void registerComponents(Context context, Glide glide, Registry registry) {

    }

}
```

- 1
- 2
- 3
- 4
- 5
- 6
- 7
- 8
- 9
- 10
- 11
- 12
- 13
- 14

可以看到，在MyAppGlideModule类当中，我们重写了applyOptions()和registerComponents()方法，这两个方法分别就是用来更改Glide配置以及替换Glide组件的。

注意在MyAppGlideModule类在上面，我们加入了一个@GlideModule的注解，这是Gilde 4和Glide 3最大的一个不同之处。在Glide 3中，我们定义了自定义模块之后，还必须在AndroidManifest.xml文件中去注册它才能生效，而在Glide 4中是不需要的，因为@GlideModule这个注解已经能够让Glide识别到这个自定义模块了。

这样的话，我们就将Glide自定义模块的功能完成了。后面只需要在applyOptions()和registerComponents()这两个方法中加入具体的逻辑，就能实现更改Glide配置或者替换Glide组件的功能了。详情还是请参考 [Android图片加载框架最全解析（六），探究Glide的自定义模块功能](http://blog.csdn.net/guolin_blog/article/details/78179422) 这篇文章，这里就不再展开讨论了。

# 使用Generated API

Generated API是Glide 4中全新引入的一个功能，它的工作原理是使用注解处理器 (Annotation Processor) 来生成出一个API，在Application模块中可使用该流式API一次性调用到RequestBuilder，RequestOptions和集成库中所有的选项。

这么解释有点拗口，简单点说，就是Glide 4仍然给我们提供了一套和Glide 3一模一样的流式API接口。毕竟有些人还是觉得Glide 3的API更好用一些，比如说我。

Generated API对于熟悉Glide 3的朋友来说那是再简单不过了，基本上就是和Glide 3一模一样的用法，只不过需要把Glide关键字替换成GlideApp关键字，如下所示：

```java
GlideApp.with(this)
        .load(url)
        .placeholder(R.drawable.loading)
        .error(R.drawable.error)
        .skipMemoryCache(true)
        .diskCacheStrategy(DiskCacheStrategy.NONE)
        .override(Target.SIZE_ORIGINAL)
        .circleCrop()
        .into(imageView);
```

- 1
- 2
- 3
- 4
- 5
- 6
- 7
- 8
- 9

不过，有可能你的IDE中会提供找不到GlideApp这个类。这个类是通过编译时注解自动生成的，首先确保你的代码中有一个自定义的模块，并且给它加上了@GlideModule注解，也就是我们在上一节所讲的内容。然后在Android Studio中点击菜单栏Build -> Rebuild Project，GlideApp这个类就会自动生成了。

当然，Generated API所能做到的并不只是这些而已，它还可以对现有的API进行扩展，定制出任何属于你自己的API。

下面我来具体举个例子，比如说我们要求项目中所有图片的缓存策略全部都要缓存原始图片，那么每次在使用Glide加载图片的时候，都去指定diskCacheStrategy(DiskCacheStrategy.DATA)这么长长的一串代码，确实是让人比较心烦。这种情况我们就可以去定制一个自己的API了。

定制自己的API需要借助@GlideExtension和@GlideOption这两个注解。创建一个我们自定义的扩展类，代码如下所示：

```java
@GlideExtension
public class MyGlideExtension {

    private MyGlideExtension() {
        
    }

    @GlideOption
    public static void cacheSource(RequestOptions options) {
        options.diskCacheStrategy(DiskCacheStrategy.DATA);
    }

}
```

- 1
- 2
- 3
- 4
- 5
- 6
- 7
- 8
- 9
- 10
- 11
- 12
- 13

这里我们定义了一个MyGlideExtension类，并且给加上了一个@GlideExtension注解，然后要将这个类的构造函数声明成private，这都是必须要求的写法。

接下来就可以开始自定义API了，这里我们定义了一个cacheSource()方法，表示只缓存原始图片，并给这个方法加上了@GlideOption注解。注意自定义API的方法都必须是静态方法，而且第一个参数必须是RequestOptions，后面你可以加入任意多个你想自定义的参数。

在cacheSource()方法中，我们仍然还是调用的diskCacheStrategy(DiskCacheStrategy.DATA)方法，所以说cacheSource()就是一层简化API的封装而已。

然后在Android Studio中点击菜单栏Build -> Rebuild Project，神奇的事情就会发生了，你会发现你已经可以使用这样的语句来加载图片了：

```java
GlideApp.with(this)
        .load(url)
        .cacheSource()
        .into(imageView);
```

- 1
- 2
- 3
- 4

有了这个强大的功能之后，我们使用Glide就能变得更加灵活了。

# 结束语

这样我们基本上就将Glide 4的所有重要内容都介绍完了，如果你以前非常熟悉Glide 3的话，看完这篇文章之后相信你已经能够熟练使用Glide 4了。而如果你以前并未接触过Glide，仅仅只看这一篇文章可能了解得还不够深入，建议最好还是把前面的七篇文章也去通读一下，这样你才能成为一名Glide好手。

原文：http://blog.csdn.net/guolin_blog/article/details/78582548