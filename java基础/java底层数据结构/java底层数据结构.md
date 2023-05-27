# 底层数据结构

![Java 集合框架概览](https://oss.javaguide.cn/github/javaguide/java/collection/java-collection-hierarchy.png)

来源：javaguide

## List

常用方法

```java
//返回大小
int size();
//判断是否为空
boolean isEmpty();
//是否包含某个对象
boolean contains(Object o);
//转换为数组
Object[] toArray();
//添加对象
boolean add(E e);
//返回指定位置下的对象
E get(int index);
//修改指定位置下的对象
E set(int index, E element);
//移除对象
boolean remove(Object o);
//排序
default void sort(Comparator<? super E> c) {...};
```

### ArrayList()

底层通过数组实现,初始化容量为10,只能存储对象类型的数据

```java
//transient 用来表示一个成员变量不是该对象序列化的一部分。
transient object[] elementData 
```

扩容机制，先生成一个大的数组，再将元素复制到大数组里面，新数组大小为原数组的1.5倍。

```java
 int newCapacity = oldCapacity + (oldCapacity >> 1);
 elementData = Arrays.copyOf(elementData, newCapacity);
```

ArrayList是线程不安全的，没有使用关键字去加锁

### LinkedList()

底层通过双向链表实现

```
    private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```

### Vector()

vector()所有方法都使用synchronized关键字实现线程同步，是线程安全的，但同步需要一定的开销所以比ArrayList()效率低，默认扩容是1倍，可以指定扩容增量

```
    public synchronized void trimToSize() {
        modCount++;
        int oldCapacity = elementData.length;
        if (elementCount < oldCapacity) {
            elementData = Arrays.copyOf(elementData, elementCount);
        }
    }
```

三者的区别与使用场景

ArrayList: 在需要频繁读取集合中的元素时，更推荐使用 ArrayList

LinkedList: 增加和删除元素的效率较高，因为不需要移动其他元素，但访问元素的效率较低，因为需要访问整个链表来查找目标元素。

Vector: 线程安全

#### Stack<> 

Stack是一种栈结构，只在栈顶进行插入和删除操作的线性表，Stack<>继承于Vector(),所以是线程安全的，但也效率比较低，优点是在栈顶插入和删除元素的时间复杂度都为 O(1)

方法

```java
//入栈
public E push(E item) {
   addElement(item);
   return item;
}
//出栈
    public synchronized E pop() {
        E       obj;
        int     len = size();

        obj = peek();
        removeElementAt(len - 1);

        return obj;
    }
```

## Queue 队列

List和Queue的区别
List是一种线性结构，可以随机访问其中的元素  俗称列表
Queue是一种先进先出（FIFO）的数据结构，只允许在队列的一端插入元素，在另一端删除元素。 俗称队列

方法

```java
//插入元素，想在一个满的队列中加入一个新项，调用 add() 方法就会抛出一个 unchecked 异常，而调用 offer() 方法会返回 false。
boolean add(E e);
boolean offer(E e);

//返回第一个元素，在队列为空时， element() 抛出一个异常，而 peek() 返回 null。
E element();
E peek();

//删除第一个元素
E remove();
E poll();
```



### Deque 双端队列

Deque 是双端队列，继承Queue,Deque支持从队列两端插入和删除元素，即支持队列和栈两种操作方式。

方法

```java
//在头增加元素
addFirst(e);offerFirst(e)
//在尾增加元素
addLast(e);offerLast(e)    
```

#### ArrayDeque 

ArrayDeque继承于Deque，双端队列的一种实现方法，基于可调整大小的数组实现的。在用作双端队列时，添加和删除操作较多时使用LinkedList,访问较多时使用ArrayDeque

### PriorityQueue 优先队列

PriorityQueue基于优先级堆的无界队列，内部维护了维护了一个优先级堆（完全二叉树），通过Comparator（比较器）指定的顺序对元素进行排序。

```
 private void siftDownUsingComparator(int k, E x) {
        int half = size >>> 1;
        while (k < half) {
            int child = (k << 1) + 1;
            Object c = queue[child];
            int right = child + 1;
            if (right < size &&
                comparator.compare((E) c, (E) queue[right]) > 0)
                c = queue[child = right];
            if (comparator.compare(x, (E) c) <= 0)
                break;
            queue[k] = c;
            k = child;
        }
        queue[k] = x;
    }
```

## Set 

set用于存储不重复元素的集合

方法

```
同List
```

### HashSet

默认容量为16，加载因子0.75，超过16*0.75则扩容，

底层通过hashmap实现, 应为hashmap的key唯一 ，所以HashSet存储的值唯一

```java
public HashSet() {
   map = new HashMap<>();
}

//增加元素
public boolean add(E e) {
        return map.put(e, PRESENT)==null;
}
//扩容
```

### LinkedHashSet

继承于HashSet, 具有HashSet的无序性和LinkedList的有序性，底层通过LinkedHashMap实现，维护元素的插入顺序，可以通过迭代器以插入的顺序遍历集合元素

```java
    public LinkedHashSet() {
        super(16, .75f, true);
    }
// 底层实现
    HashSet(int initialCapacity, float loadFactor, boolean dummy) {
        map = new LinkedHashMap<>(initialCapacity, loadFactor);
    }
```

### TreeSet

有序Set集合类，底层使用红黑树数据结构进行存储和操作，TreeSet保证元素的有序性，通过Comparator对象比较元素之间的大小关系。

红黑树：

## Map 键值对

存储键值对数据

常用方法

```java
int size();
boolean containsKey(Object key);
boolean containsValue(Object value);
V get(Object key);
V put(K key, V value);
V remove(Object key);
Set<K> keySet();
Collection<V> values();
Set<Map.Entry<K, V>> entrySet();
```

### HashMap

继承于AbstractMap<K,V>，实现Map<K,V>，

**数据结构**：HashMap的数据存在Node类型的数组中，key 和 value 分别表示键和值，next 表示链表中的下一个节点，hash 表示键的哈希值。

```java
Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
}
//桶数组
transient Node<K,V>[] table;
```

put方法

```java
putVal(hash(key), key, value, false, true);
//首先计算键的哈希值，然后根据哈希值计算在数组中的位置，找到对应的桶,如果该桶为空，则将键值对插入到该桶中。
tab[i] = newNode(hash, key, value, null);
//如果该桶不为空，则遍历该桶的链表或红黑树，查找是否存在相同的键。如果找到了相同的键，则更新该键对应的值；
e.value = value;
//否则，在该桶的链表或树中插入新节点。
p.next = newNode(hash, key, value, null);
//或则
e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value); 
```

**扩容方法**：HashMap的大小超过了负载因子（load factor）与当前容量的乘积，就会自动扩容，以减少散列冲突的可能性。

```java
//创建一个新数组，长度为原数组两倍；
newThr = oldThr << 1; // double threshold
//重新计算哈希值，并插入到新数组的相应位置上。

//释放原数组的内存空间，将HashMap的table属性指向新数组。
 return newTab;
```

链表转红黑树：

```java
//链表的长度大于 8 的时候，就执行 treeifyBin （转换红黑树）的逻辑
if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break
                            
//将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树。
if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
            resize();
```

线程不安全：多个线程对 `HashMap` 的 `put` 操作会导致线程不安全，具体来说会有数据覆盖的风险。

线程安全的map集合：

### HashTable

线程安全,内部的方法基本都经过 synchronized 修饰，只有一个线程可以进入方法，效率比ConcurrentHashMap低。

### ConcurrentHashMap

线程安全的哈希表实现

JDK1.7 的 `ConcurrentHashMap` 底层采用 **分段的数组Segment<K,V> +链表** 实现,Segment<K,V> 大小默认是16,默认可以同时支持 16 个线程并发写。

```java
static class Segment<K,V> extends ReentrantLock implements Serializable {
}

```

![Java7 ConcurrentHashMap 存储结构](https://oss.javaguide.cn/github/javaguide/java/collection/java7_concurrenthashmap.png)

JDK1.8 采用的数据结构跟 `HashMap1.8` 的结构一样，数组+链表/红黑二叉树。

![Java8 ConcurrentHashMap 存储结构](https://oss.javaguide.cn/github/javaguide/java/collection/java8_concurrenthashmap.png)

只锁node节点

![image-20230527234623623](C:\Users\luoaixiong\AppData\Roaming\Typora\typora-user-images\image-20230527234623623.png)

锁粒度更细，`synchronized` 只锁定当前链表或红黑二叉树的首节点，这样只要 hash 不冲突，就不会产生并发，就不会影响其他 Node 的读写，效率大幅提升

### TreeMap

继承于SortedMap，TreeMap是一个基于key有序的key value散列表, map根据其键的自然顺序排序，或根据map创建时提供的Comparator排序,底层为红黑树

### **红黑树**





