# HashMap原理与性能优化

HashMap下标相同会发生hash冲突，添加加载因子改变数组大小会减少hash冲突，什么时候扩容，填充度到达加载因子大小就会扩容，扩容后需重新计算hash进行排序



**当两个对象的hashcode相同，如何获取对象？**

通过下标找到对应的数组，然后对链表进行轮循查找



**为什么String，Integer这样的wrapper类适合作为键？**

因为这些类重写了hashcode



**可以用自定义的对象键作为键吗？**

只需要重写hashcode和equals



JDK1.8后用红黑树代替了链表

# 从源码分析的角度分析HashMap和SparseArray性能

SparseArray 查找get使用二分法，删除remove直接将节点设置成object，等待需要赋值的时候直接修改key值和对object重新设置为value，避免arraycopy

SparseArray 的key只能是int，HashMap 的key是泛型