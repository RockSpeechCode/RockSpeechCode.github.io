---
title: JAVA集合
date: 2020-06-29 22:11:06
tags: JAVA
categories: 编程
---

# JAVA集合

{% asset_img image-1.png %}

## 数组

- 优点：是一种简单的线性序列，可以快速地访问数组元素，效率高。如果从效率和类型检查的角度讲，数组是最好的。

- 缺点：不灵活。容量需要事先定义好，不能随着需求变化扩容。



## 泛型

数据类型的参数化，数据类型的一个占位符（形式参数）。



## List

有序，可重复。

有三个实现类。ArrayList、LinkedList和Vector。

- 在末尾添加一个元素：`boolean add(E e)`
- 在指定索引添加一个元素：`boolean add(int index, E e)`
- 是否为空：`boolean isEmpty()`
- 删除某个元素：`boolean remove(Object e)`
- 清空：`void clear()`
- 获取指定索引的元素：`E get(int index)`
- 获取链表大小（包含元素的个数）：`int size()`
- 添加一个list：`boolean addAll(Collection c)`
- 删除与另一个list相同元素：`boolean removeAll(Collection c)`
- 取交集：`boolean retainAll(Collection c)`
- 是否包含另一个list：`boolean containsAll(Collection c)`



### Arraylist

用数组实现的存储。查询效率高，增删效率低，线程不安全。

通过数组扩容的方式实现不限制大小。

- 指定索引添加或替换：`boolean set(int index, E e)`

- 删除指定索引的元素：`E remove(int index)`

- 是否存在某个元素，返回第一次出现的索引：`int indexOf(E e)`
- 是否存在某个元素，返回最后一次出现的索引：`int indexOf(E e)`



### LinkedList

用双向链表实现的存储。查询效率低，增删效率高，线程不安全。



### Vector

用数组实现的存储，效率低，线程安全。相关方法做了同步检查synchronized。



## Map

存储键值对。键不可重复。

实现类HashMap、TreeMap、HashTable、Properties



### HashMap

线程不安全，效率高，允许key或者value为null，

- 添加一个键值对：`Object put(Object key,Object value)`
- 通过键查找值：`Object get(Object key)`
- 是否为空：`boolean isEmpty()`
- 删除指定键的键值对：`Object remove(Object key)`
- 是否包含键对象：`boolean containsKey(Object key)`
- 是否包含值对象：`boolean containsValue(Object value)`
- 清空：`void clear()`
- 获取Map大小（包含键值对的个数）：`int size()`
- 添加一个Map中的所有对象：`void putAll(Map m)`



### TreeMap

红黑二叉树的典型实现，排序key。



### HashTable

线程安全，效率低，不允许key或者value为null。



## Set

无序，不可重复。

- 将元素添加进`Set<E>`：`boolean add(E e)`
- 将元素从`Set<E>`删除：`boolean remove(Object e)`
- 判断是否包含元素：`boolean contains(Object e)`



### HashSet

无序的，实现了`Set`接口，并没有实现`SortedSet`接口。



### TreeSet

有序的，实现了`SortedSet`接口。



## 迭代器Iterator

- 迭代器遍历List

```java
List<String> list = new ArrayList<>();
for (Iterator<String> it = list.iterator(); it.hasNext(); ) {
     String s = it.next();
     System.out.println(s);
}
```



- 迭代器遍历Set

```java
Set<String> set = new HashSet<>();
for (Iterator<String> it = set.iterator(); it.hasNext(); ) {
     String s = it.next();
     System.out.println(s);
}
```



- 迭代器遍历Map

```java
Map<Integer,String> map = new HashMap<>();
Set <Entry<String,String>> s = map.entrySet();
for (Iterator<String> it = s.iterator(); it.hasNext(); ) {
     Entry e = it.next();
     System.out.println(e.getKey() + e.getValue());
}
```

```java
Map<Integer,String> map = new HashMap<>();
Set<String> s = map.keySet();
for(Iterator<String> it = s.iterator(); it.hasNext(); ){
    String key = it.next();
    System.out.println(key + map.get(key));
}
```



## Collections工具类

- `void sort(List)`：对List元素升序排列

- `void shuffle(List)`：对List容器内元素随机排列

- `void reverse(List)`：对List元素逆转排列

- `void fill(List,Object)`：用一个特定的对象重写List容器

- `int binarySearch(List,Object)`：顺序折半查找
