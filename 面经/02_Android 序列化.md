# Android 序列化

将一个对象拆解添加标记，传送过后通过标记对对象进行组装

为什么要序列化？

进行Android开发的时候，无法将对象的引用传给Activities或者Fragments，我们需要将这些对象放到一个Intent或者Bundle里面，然后再传递。

- Java 序列化实现 Serializable，通过ObjectOutputStream和ObjectInputStream来实现

优点是无需实现方法，JDK 自动对对象进行高效的序列化操作

缺点是使用了反射，序列化的过程需要大量的 I/O 操作储存在硬盘上，开销很大

- Android 实现序列化实现 Parcelable

优点是效率高，自定义实现方法，便于调试，在内存中读写

缺点是将一个完整的对象进行分解，需实现特定的方法



