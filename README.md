# Java常用容器详解
## 基本概念
#### 线性表
* 线性表是由n（n≥0）个数据元素a[0]，a[1]，a[2]，…，a[n-1]组成的有限序列。存在一个唯一的没有前驱的（头）数据元素；存在一个唯一的没有后继的（尾）数据元素；此外，每一个数据元素均有一个直接前驱和一个直接后继数据元素。
#### 非线性
* 非线性结构中,各数据元素之间的前驱后继关系要比线性结构复杂。如图，树等。
#### List
* list 是一个元素有序的集合，每个元素可以通过索引访问，并且允许重复的元素。
#### Set
* set 是一个不允许元素重复的集合，其有序性与具体的实现类有关。
#### Map
* map 是将键映射到值的结构。一个映射不能包含重复的键；每个键最多只能映射到一个值。
#### Queue
* queue 是 FIFO 的序列。
* deque 是可以在两端进行操作的序列。
#### Stack
* stack 是 LIFO 的序列。
#### Heap
n 个记录的序列 L[1…n] 称为堆，当且仅当该序列满足下面两个条件任意一个。($1 \le i \le \left\lfloor\frac{n}{2}\right \rfloor$)

* L[i] ≤ L[2i] && L[i] ≤ L[2i+1]  (小堆)
* L[i] ≥ L[2i] && L[i] ≥ L[2i+1]  (大堆)


## 相关类图的层级关系
### 集合相关
下图给出了常用的集合容器的层级关系，有些依赖关系未标出。
![Java](http://oo2tkpanb.bkt.clouddn.com/java_collection.png)

**Iterable:** 迭代器接口，只含一个方法 iterator()，返回 Iterator 对象。所有的集合容器都使用迭代器对象来访问容器中的元素。Iterator 迭代器接口只含有三个方法如下：
```Java
public interface Iterator<E> {
    E next();
    boolean hashNext();
    void remove();
}
```
Java1.5开始可以使用 **for each** 更加简化的遍历方式。迭代器访问元素的顺序取决于集合的类型，如对 HashSet 进行遍历，元素会随机不重复的出现，无法预知元素被访问的顺序。  

**Collection:** 所有集合容器的父接口，它定义了一系列集合操作的规范。 

**List:** 有序，可重复的集合接口。继承自 **Collection** 接口，其元素的索引是按照插入的顺序构建。定义了更加具体的一系列有序集合的操作规范。 

**ArrayList:** 利用数组实现的有序集合类，除了实现 **List**接口外，还提供一些方法来操作内部用来存储列表的数组的大小。允许 Null 元素。默认初始容量为10，==若已知所需的容量，需初始化时指定容量，以减少后续动态扩容的次数==。
```Java
   /**
     * @param minCapacity the desired minimum capacity
     */
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```
上述代码可知，ArrayList 默认的扩容方式以原容量的0.5倍增加。  

**LinkedList:** 用链表来实现 **List** 接口的有序集合类，同时实现了 **deque** 接口，允许 null 元素。LinkedList 类为链表头及尾结点设置 get、remove 和 insert 等统一的命名方法。这些操作允许将链表列表用作堆栈、队列或双端队列。

**Vector:** 用法和 ArrayList 类似，是一个旧类。同样，==若已知所需的容量，需初始化时指定容量，以减少后续动态扩容的次数==。  
```
Vector 与 ArrayList, LinkedList 之间的区别如下：  
1. Vector 可指定增加容量。ArrayList 不可，若不指定，需扩容时，Vector 扩容一倍，ArrayList 则扩容0.5倍。
2. Vector 是同步的，ArrayList 和 LinkedList 不是。
3. LinkedList 增删效率高，ArrayList 和 Vector 支持随机访问。  
```
**Stack:** Vector 的子类，模拟栈的容器。  

**Set:** 继承自 **Collection** 接口，限定了不可含有重复元素的集合。其添加对象时，是利用对象的 equals 方法来判断两对象是否相等。需要对象的 equals 方法有效，否则并不能实现真正无重复的集合。  

**HashSet:** 实现 **Set** 接口，由哈希表（实际上是一个 HashMap 实例）实现。它不保证 迭代顺序。允许使用 null 元素。其通过元素的 hashCode 值来决定该元素在 HashSet 中的存储位置。  

**LinkedHashSet:** 具有可预知迭代顺序的 **Set** 接口的哈希表和链表实现。实际是利用 LinkedHashMap 实现。

**SortedSet:** 继承自  **Set**,定义了关于元素排序方法的规约。元素使用自然顺序或者根据通常在创建有序 Set 时提供的 Comparator 进行排序。  

**NavigableSet:** 扩展了 **SortedSet** 接口，具有为给定搜索目标报告最接近匹配项的导航方法。方法 lower、floor、ceiling 和 higher 分别返回小于、小于等于、大于等于、大于给定元素的元素。

**TreeSet:** 基于 TreeMap 的 NavigableSet 实现。  

*所有具有排序性质的容器，通常需提供两种方式来进行排序比较，默认的是元素得实现 Comparable 接口，或者提供一个外部比较器 Comparator。否则将会运行中抛出 ClassCastException exception。* Comparable 需实现 compareTo 方法，Comparator 接口需要实现compare 方法，equals 方法可选实现。两者代码如下：
```Java
public interface Comparable<T> {
    int compareTo(T o);
}

//1.8之前的Comparator接口只有两个方法
public interface Comparator<T> {
    int compare(T o1, T o2);
    boolean equals(Object object);
}
```
对于Set集合的实现，其容器中元素的 equals 方法至关重要。重写 equals 方法需遵守以下几点： 
+ **自反性**：对于任何非空的 x, x.equals(x)都应该返回 true
+ **对称性**：对于任何引用 x 和 y，当且仅当 x.equals(y) 返回true时，y.equals(x) 也应该返回 true
+ **传递性**：对于任何引用 x,y,z，如果 x.equals(y)返回true，y.equals(z) 返回true，那么 x.equals(z) 也应该返回 true
+ **一致性**：如果 x 和 y 的引用没有发生变化，那么反复调用 x.equals(y) 的结果应该相同
+ 对于任何非空的引用 x,x.equals(null) 应该返回 false  

==注意==：当此方法被重写时，通常有必要重写 hashCode 方法，以维护 hashCode 方法的常规协定，该协定声明相等对象必须具有相等的哈希码。  
以下是一个重写equals 方法举例。
```Java
public class A {
    int id;
    B b;

    @Override
    public boolean equals(Object obj) {
        // 同一个对象
        if (this == obj) return true;
        // 对象为null
        if (obj == null) return false;
        // 两者不同类
        if (obj.getClass() != getClass()) return false;
        // 同类，需判定其内容是否分别相等
        A other = (A) obj;
        return (other.id == this.id) && (this.b.equals(other.b));
    }
}
```

**Queue:** 定义了队列操作的规范，从 **Collection** 继承了一系列方法。从以下代码中可知队列一些常用的方法之间的区别。
```Java
public interface Queue<E> extends Collection<E> {
    /**
     * 将元素插入此队列（如果立即可行且不会违反容量限制），在成功时返回
     * true，如果当前没有可用的空间，则抛出 IllegalStateException。
     */
    boolean add(E e);
    /**
     * 将元素插入此队列（如果立即可行且不会违反容量限制），当使用有容量限制的
     * 队列时，此方法通常要优于add(E)，后者可能无法插入元素，而只是抛出一个异常。
     */
    boolean offer(E e);
    // 返回队列头，若队列空则 throws NoSuchElementException
    E remove();
    // 返回队列头，若队列空，则返回 null
    E poll();
    // 返回队列头，若队列为空，则 throws NoSuchElementException
    E element();
    // return the head of this queue, or null if this queue is empty
    E peek();
}
```

**Deque:** 支持在两端插入和移除元素的线性结构。此接口定义在双端队列两端访问元素的方法。提供插入、移除和检查元素的方法。每种方法都存在两种形式：一种形式在操作失败时抛出异常，另一种形式返回一个特殊值(false or null)。如下表总结所示：  

<table >
   <tr>
        <th ></th>
        <th colspan="2">第一个元素</th>
        <th colspan="2">最后一个元素</th>
  </tr>
  <tr>
        <th ></th>
        <th >抛出异常</th>
        <th >特殊值</th>
        <th >抛出异常</th>
        <th >特殊值</th>
  </tr>
  <tr>
    <td>insert</td>
     <td>addFirst(e)</td>
    <td>offerFirst(e)</td>
    <td>addLast(e)</td>
    <td>offerLast(e)</td>
  </tr>
  <tr>
    <td>remove</td>
     <td>removeFirst()</td>
    <td>pollFirst()</td>
    <td>removeLast()</td>
    <td>pollLast()</td>
  </tr>
  <tr>
    <td>check</td>
     <td>getFirst()</td>
    <td>peekFirst()</td>
    <td>getLast()</td>
    <td>peekLast()</td>
  </tr>
</table>
实现**Deque**接口的结构，可以当做队列和栈使用，其内部，提供了很多同功能异名的方法，例如 pop 方法和 removeFirst 方法等。  

**BlockingQueue:** 支持两个附加操作的 Queue，这两个操作是：获取元素时**等待**双端队列变为非空；存储元素时**等待**双端队列中的空间变得可用。  

**PriorityQueue:** 一个基于优先级**堆**的无界优先级队列。优先级队列的元素按照其自然顺序进行排序，或者根据构造队列时提供的 Comparator 进行排序，具体取决于所使用的构造方法。优先级队列不允许使用 null 元素。不可插入不可比较对象，否则抛出异常。  
构造方法只分析通过其他集合类创建的方法：
```Java
    public PriorityQueue(Collection<? extends E> c) {
        // 若是排序类，只需要直接转换成数组即可,不能含有null
        if (c instanceof SortedSet<?>) {
            SortedSet<? extends E> ss = (SortedSet<? extends E>) c;
            this.comparator = (Comparator<? super E>) ss.comparator();
            initElementsFromCollection(ss);
        }
        // 若是优先级队列，直接赋值
        else if (c instanceof PriorityQueue<?>) {
            PriorityQueue<? extends E> pq = (PriorityQueue<? extends E>) c;
            this.comparator = (Comparator<? super E>) pq.comparator();
            initFromPriorityQueue(pq);
        }
        else {
            // 需要转换成数组，且需要通过比较器实现堆化
            this.comparator = null;
            initFromCollection(c);
        }
    }
```
堆化就是利用比较器将数组构造成一个堆，形式上可以想象成完全二叉树。父节点比子节点都大或者小。调整堆的源码如下：
```Java
   /**
     * 从数组中段开始，遍历堆化到数组头
     */
    private void heapify() {
        for (int i = (size >>> 1) - 1; i >= 0; i--)
            siftDown(i, (E) queue[i]);
    }
    // 两种方式的比较器进行堆调整。
    private void siftDown(int k, E x) {
        if (comparator != null)
            siftDownUsingComparator(k, x);
        else
            siftDownComparable(k, x);
    }
   
    private void siftDownUsingComparator(int k, E x) {
        int half = size >>> 1;
        while (k < half) {
            int child = (k << 1) + 1;   //左子节点索引
            Object c = queue[child];
            int right = child + 1;  //右子节点索引
            if (right < size &&
                comparator.compare((E) c, (E) queue[right]) > 0)
                c = queue[child = right];   //获取左右子节点中大的一个
            if (comparator.compare(x, (E) c) <= 0)
                break;   //只要符合大小堆，即可结束。
            queue[k] = c;  //将子节点中大的元素转到k索引的位置
            k = child;  //将子节点中大的元素的索引赋予k，继续循环处理
        }
        queue[k] = x;  //调整完毕，最终将x元素放置到k索引处
    }
```

**DelayQueue:** Delayed元素的一个无界阻塞队列，只有在延迟期满时才能从中提取元素。其内部是由 PriorityQueue 来存储元素的。  
这里主要介绍一个 poll 方法，此方法获取并移除此队列的头部，在可从此队列获得到期延迟的元素，或者到达指定的等待时间之前一直等待（如有必要）。代码如下：
```Java
    public E poll(long timeout, TimeUnit unit) throws InterruptedException {
        long nanos = unit.toNanos(timeout);
        final ReentrantLock lock = this.lock; 
        lock.lockInterruptibly();
        try {
            for (;;) {
                E first = q.peek();
                if (first == null) {
                    if (nanos <= 0)
                        return null;
                    else
                        nanos = available.awaitNanos(nanos); // Block until signalled, interrupted, or timed out.
                } else {
                    long delay = first.getDelay(NANOSECONDS);
                    if (delay <= 0)  // 延迟期已经满足，则立即返回此元素
                        return q.poll();
                    if (nanos <= 0)  // 等待时间已到，则返回null
                        return null;
                    first = null; // don't retain ref while waiting
                    if (nanos < delay || leader != null)
                        nanos = available.awaitNanos(nanos); // 等待
                    else { // 等待时间大于delay
                        Thread thisThread = Thread.currentThread();
                        leader = thisThread;// 主线程等待下一个头元素
                        try {
                            long timeLeft = available.awaitNanos(delay);
                            nanos -= delay - timeLeft;
                        } finally {
                            if (leader == thisThread)
                                leader = null;
                        }
                    }
                }
            }
        } finally {
            if (leader == null && q.peek() != null)
                available.signal();// 给其他线程的信号
            lock.unlock();
        }
    }
```

### 映射表相关
下面是Map相关的类关系图，列出了一些常用的 Map 容器类。
![Java](http://oo2tkpanb.bkt.clouddn.com/java_map.png)

**Map:** 定义了一系列映射表相关的规约。可描述为：将键映射到值的结构，一个映射不能包含重复的键；每个键最多只能映射到一个值。提供三种 **Collection** 视图，允许以键集、值集或键-值映射关系集的形式查看某个映射的内容。映射的顺序依据具体的实现类的属性，如TreeMap可保证映射的顺序。元素的非空属性也有具体实现类决定。

**HashMap:** 
这里分析的是1.7版本的HashMap处理细节，1.8版本的HashMap相关分析及与1.7版本的差别[点这里](http://blog.wangchangzhi.me/2017/05/21/Map-HashMap/)  
有两个参数影响其性能：**初始容量**和**加载因子**。容量 是哈希表中桶的数量，初始容量只是哈希表在创建时的容量。加载因子 是哈希表在其容量自动增加之前可以达到多满的一种尺度。  
对于构造函数，提供了多种方式，可使用默认的参数，默认的初始容量是16，加载因子为0.75。也可自行设定初始容量和加载因子，加载因子最好不要更改，太小会浪费空间，太大会增加碰撞的几率。  

下面分析几个主要的方法, 如索引位置的确定，put，resize方法。
1.7版本HashMap的put方法整体流程图如下：  

![图](http://oo2tkpanb.bkt.clouddn.com/HashMap-put.png)  
put方法的源码解析如下：
```Java
    // Returns index for hash code h.
    static int indexFor(int h, int length) {
        // assert Integer.bitCount(length) == 1 : "length must be a non-zero power of 2";
        return h & (length-1);
    }
    // 返回原结点的值，无，则返回null
    public V put(K key, V value) {
        // map是空时初始化table
        if (table == EMPTY_TABLE) {
            inflateTable(threshold);
        }
        // key是null时将数据放入table[0]下的链表中
        if (key == null)
            return putForNullKey(value);
        // 计算key的hash值，找到这个key的位置索引
        int hash = hash(key);
        int i = indexFor(hash, table.length);
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            // 判断key是否存在，如果存在更新value
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }
        // 总数自增
        modCount++;
        // 不存的key,则会创建一个entry实例
        addEntry(hash, key, value, i);
        return null;
    }

    void addEntry(int hash, K key, V value, int bucketIndex) {
        if ((size >= threshold) && (null != table[bucketIndex])) {
            resize(2 * table.length);
            hash = (null != key) ? hash(key) : 0;
            // 获取这个key所在的链表地址
            bucketIndex = indexFor(hash, table.length);
        }
    // 创建一个新的entry
        createEntry(hash, key, value, bucketIndex);
    }

    void createEntry(int hash, K key, V value, int bucketIndex) {
        Entry<K,V> e = table[bucketIndex];
        // 创建一个新的entry实例，将它置于链表数组
        table[bucketIndex] = new Entry<>(hash, key, value, e);
        size++;
    }
```
resize方法源代码及解析如下：
```Java
    void resize(int newCapacity) {
        Entry[] oldTable = table;
        int oldCapacity = oldTable.length;
        if (oldCapacity == MAXIMUM_CAPACITY) {// 达到最大容量，则直接返回
            threshold = Integer.MAX_VALUE;
            return;
        }

        Entry[] newTable = new Entry[newCapacity];// 创建新的散列表
        transfer(newTable, initHashSeedAsNeeded(newCapacity));
        table = newTable;// 将新散列表覆盖原始的
        threshold = (int)Math.min(newCapacity * loadFactor, MAXIMUM_CAPACITY + 1);// 更新阈值
    }

    /**
     * 旧表向新表转移的时候，采用的是头插法。所以原始的碰撞链表可能
     * 会拆分成两个链表，这些链表中的元素的相对位置变成倒序。
     */
    void transfer(Entry[] newTable, boolean rehash) {
        int newCapacity = newTable.length;
        for (Entry<K,V> e : table) {
            while(null != e) {
                Entry<K,V> next = e.next;
                if (rehash) {// 是否重新计算hash
                    e.hash = null == e.key ? 0 : hash(e.key);
                }
                int i = indexFor(e.hash, newCapacity);//重新计算索引位置
                e.next = newTable[i];
                newTable[i] = e;// 放置在链表头
                e = next;// 指向后一个元素
            }
        }
    }
```
**LinkedHashMap:**  Map 接口的散列表和链表实现，具有可预知的迭代顺序。此实现与 HashMap 的不同之处在于，它维护着一个记录插入顺序的双向链表。

**SortMap:** 提供关于键的总体排序 的 Map。该映射是根据其键的自然顺序进行排序的，或者根据通常在创建有序映射时提供的 Comparator 进行排序。对有序映射的 collection 视图（由 entrySet、keySet 和 values 方法返回）进行迭代时，此顺序就会反映出来。

**NavigableMap:** 扩展的 SortedMap，具有了针对给定搜索目标返回最接近匹配项的导航方法。方法 lowerEntry、floorEntry、ceilingEntry 和 higherEntry 分别返回与小于、小于等于、大于等于、大于给定键的键关联的 Map.Entry 对象，如果不存在这样的键，则返回 null。

**TreeMap:** 基于红黑树的 NavigableMap 实现。该映射根据其键的自然顺序进行排序，或者根据创建映射时提供的 Comparator 进行排序，具体取决于使用的构造方法。key 必须实现Comparable 接口或者在构造TreeMap传入自定义的Comparator，否则会在运行时抛出ClassCastException 类型的异常。  
TreeMap 为 containsKey、get、put 和 remove 操作提供受保证的 log(n) 时间开销。  
下面对TreeMap的put 方法进行源码分析，如下：
```Java
    // 返回原结点的值，无，则返回null
    public V put(K key, V value) {
        Entry<K,V> t = root;
        if (t == null) { // 树为null的情况
            compare(key, key); // null检测

            root = new Entry<>(key, value, null);// 添加到根结点
            size = 1;
            modCount++;
            return null; 
        }
        int cmp;
        Entry<K,V> parent;
        // split comparator and comparable paths
        Comparator<? super K> cpr = comparator;
        if (cpr != null) {
            do {
                parent = t;// 记住父节点
                cmp = cpr.compare(key, t.key);
                if (cmp < 0)
                    t = t.left;
                else if (cmp > 0)
                    t = t.right;
                else// 相同key，则直接覆盖，返回
                    return t.setValue(value);
            } while (t != null);
        }
        else {// 使用key内部的比较器，
            if (key == null)
                throw new NullPointerException();
            Comparable<? super K> k = (Comparable<? super K>) key;
            do {
                parent = t;// 记住父节点
                cmp = k.compareTo(t.key);
                if (cmp < 0)
                    t = t.left;
                else if (cmp > 0)
                    t = t.right;
                else// 相同key，则直接覆盖，返回
                    return t.setValue(value);
            } while (t != null);
        }
        // 没有相同的key情况
        Entry<K,V> e = new Entry<>(key, value, parent);
        if (cmp < 0) // 直接添加结点
            parent.left = e;
        else
            parent.right = e;
        fixAfterInsertion(e);// 进行着色和旋转的操作
        size++;
        modCount++;
        return null;
    }
```

**~~Dictionary~~:** Dictionary 类是任何可将键映射到相应值的类的抽象父类。任何非 null 对象都可以用作键或值。==此类已过时。新的实现应该实现 Map 接口，而不是扩展此类。==

**HashTable:** 此类实现一个散列表，该散列表将键映射到相应的值。任何非 null 对象都可以用作键或值。除了同步和不允许null 元素和HashMap大致相同。   
其并发性不如ConcurrentHashMap，因为ConcurrentHashMap引入了分段锁。Hashtable 不建议使用，没有同步的需求场合可以用HashMap替换，需要同步安全的场合可以用ConcurrentHashMap替换。
```
下面总结了 HashTable 和 HashMap 的异同点。 
1. 来源。都实现了 Map 接口，HashMap 继承自AbstractMap,而 HashTable 继承自 Dictionary。  
2. 同步，HashTable 是同步的，HashMap 不是。  
3. 遍历。HashMap 和 HashTable 都可使用 Iterator 迭代器，而 HashTable 还保留使用了 Enumeration。  
4. 扩容方式，HashMap 初始容量16，扩容每次增加一倍容量，而 HashTable 初始容量11，扩容结果为 old*2+1
6. null 元素，HashMap允许键值为 null，HashTbale 不允许。
7. hash值，HashMap 需重新计算，而 HashTable 直接使用对象的 hashCode 进行操作。
```
**ConcurrentMap:** 额外提供 putIfAbsent、remove、replace 方法原子操作的 Map。  
内存一致性效果：当存在其他并发 collection 时，将对象放入 ConcurrentMap 之前的线程中的操作 happen-before 随后通过另一线程从 ConcurrentMap 中访问或移除该元素的操作。

**ConcurrentHashMap:** 支持获取的完全并发和更新的所期望可调整并发的哈希表。  
+ JDK6,7 中的 ConcurrentHashmap 主要使用 Segment 来实现减小锁粒度，把 HashMap 分割成若干个Segment，在 put 的时候需要锁住Segment，get 时候不加锁。  
+ JDK8 中对 ConcurrentHashMap 进行了完全重写。

### 工具类
**Collections:** 此在 collection 上进行操作或返回 collection 的静态方法组成，其中提供了一系列静态方法，用于对集合中元素进行排序、搜索以及线程安全等各种操作。主要提供如下方法等： 
+ 排序，提供两种，一种自然排序，一共提供外部比较器 Comparator。
+ 查找集合元素，binarySearch，同样提供两种方式，自然排序，或者提供外部比较器。
+ 复制集合，将一个集合的所有元素复制到另一个中。
+ 返回集合中的最大或者最小的元素，和上述一样，也是提供两种比较方式。
+ 翻转集合，或者固定偏移集合中的元素。
+ 提供同步包装器，如 synchronizedMap 方法。
 
**Arrays:** 此类包含用来操作数组（比如排序和搜索）的各种方法。此类还包含一个允许将数组作为列表来查看的静态工厂。主要提供以下方法。    
* 给数组赋值：通过 fill 方法。
+ 对数组排序：通过 sort 方法,按升序。
+ 比较数组：通过 equals 方法比较数组中元素值是否相等。
+ 查找数组元素：通过 binarySearch 方法能对排序好的数组进行二分查找法操作。
## 总结
针对上述几张图中的一些常用容器作简要的总结，插入和读取的时间复杂度仅给出**最坏情况**下的。具体如下：  

|    容器名   | 描述| 内部结构| 扩容策略|插入|读取|是否同步|
| ---------- | --- | -----|------|-------|-----|-----|
| ArrayList |  可伸缩的索引序列 |数组|增长0.5倍|O(N)|O(1)|否|
| Vector |  可伸缩的索引序列 |数组|增长(1倍 or 固定大小)|O(N)|O(1)|是|
| LinkedList |  可高效增删操作的序列 |链表|无界|O(1)|O(N)|否|
| ArrayQueue |  循环数组实现的双端队列|循环数组|手动扩容|O(1)|O(1)|否|
| HashSet    |  无重复的无序集合|HashMap(散列表)|增长1倍|O(N)|O(N)|否|
| TreeSet    |  有序集合|TreeMap(红黑树)|无界|O(logN)|O(logN)|否|
| LinkedHashSet| 可保存插入顺序的集合|LinkedHashMap(散列表+双向链表)|增长1倍|O(N)|O(N)|否|
| PriorityQueue | 可高效删除符合规则元素的集合|堆|增长1 or 0.5倍|O(logN)|O(1)|否|
| DelayQueue | Delayed元素的一个阻塞队列|PriorityQueue(数组)|增长1 or 0.5倍| -|-|是|
| HashMap | 存储键值对的结构|散列表(1.8中红黑树改善性能)|增长1倍|O(N)|O(N)|否|
| TreeMap| 存储键值有序的结构|红黑树|无界|O(logN)|O(logN)|否|
| LinkedHashMap| 可保存插入顺序的映射表|散列表+双向链表|增长1倍|O(N)|O(N)|否|
| WeakHashMap | 可将无用的键值对回收的映射表|数组+ReferenceQueue(队列)| 增加1倍|O(N)|O(N)|否|
|HashTable| 支持并发的存储键值对的映射表|散列表|增长1倍 + 1|O(N)|O(N)|是|
|ConcurrentHashMap|支持高效并发的存储键值对的映射表|散列表(分段加锁)|增长1倍|O(N)|O(N)|是|

**附：上表所有的Hash相关的容器，查找和插入时间复杂度，N代表发生碰撞的那条链表长度。**
