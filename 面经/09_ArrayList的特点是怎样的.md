# ArrayList的特点是怎样的

ArrayList删除结点remove()是使用arraycopy()实现的，所以比较耗时

- 优点：尾插效率高，支持随机访问（因为内部是使用数组）
- 缺点：中间插入或者删除效率低（需要对整个数组使用arraycopy耗内存耗时间）
- 应用场景：需要排序不适合，删除增加多的话使用LinkedList，顺序查找速度快

ArrayLis如何顺序t删除结点？

通过迭代器删除，要注意删除后的结点还存在

ArrayLis如何遍历？

用hashNext()方法，for循环遍历

