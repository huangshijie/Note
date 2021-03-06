# HashMap

参考链接：

https://www.cnblogs.com/chengxiao/p/6059914.html

https://www.jianshu.com/p/03d0e77f182c

http://yikun.github.io/2015/04/01/Java-HashMap%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86%E5%8F%8A%E5%AE%9E%E7%8E%B0/

https://www.cnblogs.com/killbug/p/7679043.html

http://www.importnew.com/31278.html

作为一个存储结构来说，查找和插入可以看作是硬币的两面，查找快，插入势必会慢；插入快，查找势必会慢

举例最常见的就是：数组和链表这两种结构

## WHAT

下面就是HashMap的主体，一个Node<K, V>的数组

```java
transient Node<K,V>[] table;
```

Node<K, V>作为存储里的主体，链表中的一个元素

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;
    ...
}
```

举个例子：

```java
HashMap<String, Integer> h = new HashMap<String, Integer>();

h.put("key1", 1);
h.put("kkk2", 2);
h.put("eee3", 3);
h.put("yyy4", 4);
h.put("kkk5", 5);
h.put("eee6", 6);
h.put("yyy7", 7);

for(Entry<String, Integer> entry : h.entrySet()) {
  System.out.println(entry.toString());
}
```

![Debug HashMap Instance](https://github.com/huangshijie/ImgRep/blob/master/debug%20hashmap%20instance.png)

从上面的源码，以及debug调试截图可以看出，首先数据存入的是HashMap h的实例变量table中。*table*是一个数组，但是数据存入的时候，却没有连续存入。
这是因为在数据存入的时候，会计算在数组中存入时候的下标。

比如上面源码中，存入key为"eee3"调用Object.hashCode()计算得到3109153，二进制为1011110111000100100001。HashMap.hash(K key)将该二进制与自己无符号右移16位的二进制数，进行位异或运算，即1011110111000100100001 ^ 0000000000000000101111 = 0000000000000000100001。将得到的值与数组长度进行与运算，即0000000000000000100001 & 0000000000000000001111 = 1，即转换成十进制1，即存在数组下标为1的位置。

---

### 普通链表

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;
    ...
}
```

---

### 红黑树(R-B Tree)

一旦因为hash冲突，导致链表过长，则会将该链表树化()，将链表转为红黑树。

#### 特性

- 每个节点要么是红的，要么是黑的。
- 根节点是黑的。
- 每个叶节点（叶节点即指树尾端NIL指针或NULL节点）是黑的。
- 如果一个节点是红的，那么它的两个儿子都是黑的。
- 对于任一节点而言，其到叶节点树尾端NIL指针的每一条路径都包含相同数目的黑节点。

#### 变色

#### 左旋

#### 右旋

```java
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
    TreeNode<K,V> parent;  // red-black tree links
    TreeNode<K,V> left;
    TreeNode<K,V> right;
    TreeNode<K,V> prev;    // needed to unlink next upon deletion
    boolean red;
    ...
}
```

---

## HOW

### 构造函数

```java
public HashMap(int initialCapacity, float loadFactor) 

public HashMap(int initialCapacity)

public HashMap()

public HashMap(Map<? extends K, ? extends V> m)
```

一共有四个构造函数，前三种是使用自定义或者默认初试容量(initialCapacity)和加载因子(loadFactor)来创建一个空的HashMap；第四个是使用一个相同映射的特殊Map，来创建一个新的HashMap，这里的初试容量和加载因子都是取的默认值。

注意这里前三种，在初始化的时候，并没有给table开辟内存空间，第四种会根据**Map<? extends K, ? extends V> m**的大小来设置table的最大容量，加载因子还是使用默认的

### 增

```java
static final int hash(Object key)
```

hash()方法主要是调用Object.hashCode()方法，hashCode是

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        //声明了一个局部变量 tab,局部变量 Node 类型的数据 p,int 类型 n,i
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        //首先将当前 hashmap 中的 table(哈希表)赋值给当前的局部变量 tab,然后判断tab 是不是空或者长度是不是 0,实际上就是判断当前 hashmap 中的哈希表是不是空或者长度等于 0
        if ((tab = table) == null || (n = tab.length) == 0)
        //如果是空的或者长度等于0,代表现在还没哈希表,所以需要创建新的哈希表,默认就是创建了一个长度为 16 的哈希表
            n = (tab = resize()).length;
        //将当前哈希表中与要插入的数据位置对应的数据取出来,(n - 1) & hash])就是找当前要插入的数据应该在哈希表中的位置,如果没找到,代表哈希表中当前的位置是空的,否则就代表找到数据了, 并赋值给变量 p
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);//创建一个新的数据,这个数据没有下一条,并将数据放到当前这个位置
        else {
          //代表要插入的数据所在的位置是有内容的
          //声明了一个节点 e, 一个 key k
            Node<K,V> e; K k;
            if (p.hash == hash &&
            //如果当前位置上的那个数据的 hash 和我们要插入的 hash 是一样,代表没有放错位置
            //如果当前这个数据的 key 和我们要放的 key 是一样的,实际操作应该是就替换值
                ((k = p.key) == key || (key != null && key.equals(k))))
                //将当前的节点赋值给局部变量 e
                e = p;
            else if (p instanceof TreeNode)//如果当前节点的 key 和要插入的 key 不一样,然后要判断当前节点是不是一个红黑色类型的节点
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);//如果是就创建一个新的树节点,并把数据放进去
            else {
                //如果不是树节点,代表当前是一个链表,那么就遍历链表
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {//如果当前节点的下一个是空的,就代表没有后面的数据了
                        p.next = newNode(hash, key, value, null);//创建一个新的节点数据并放到当前遍历的节点的后面
                        if (binCount >= TREEIFY_THRESHOLD - 1) // 重新计算当前链表的长度是不是超出了限制
                            treeifyBin(tab, hash);//超出了之后就将当前链表转换为树,注意转换树的时候,如果当前数组的长度小于MIN_TREEIFY_CAPACITY(默认 64),会触发扩容,我个人感觉可能是因为觉得一个节点下面的数据都超过8 了,说明 hash寻址重复的厉害(比如数组长度为 16 ,hash 值刚好是 0或者 16 的倍数,导致都去同一个位置),需要重新扩容重新 hash
                        break;
                    }
                    //如果当前遍历到的数据和要插入的数据的 key 是一样,和上面之前的一样,赋值给变量 e,下面替换内容
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { //如果当前的节点不等于空,
                V oldValue = e.value;//将当前节点的值赋值给 oldvalue
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value; //将当前要插入的 value 替换当前的节点里面值
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;//增加长度
        if (++size > threshold)
            resize();//如果当前的 hash表的长度已经超过了当前 hash 需要扩容的长度, 重新扩容,条件是 haspmap 中存放的数据超过了临界值(经过测试),而不是数组中被使用的下标
        afterNodeInsertion(evict);
        return null;
    }

```

- 判断Node数组是否为空，是的话，则调用resize方法，相当于初始化这个Node的table。
- 根据(n - 1) & hash，得到该元素在数组中的下标。
- 若该位置为空，则直接new一个Node，赋值给该数组
- 若该位置不为空，说明hash方法返回的值相同，则要么发生了hash碰撞，要么key已经存在该数组中了。
- 若发生了hash碰撞，则以链表或是treeNode的方式插入数据，如果链表长度过长，大于threshold，则将该链表树化(treeifyBin)。
- 若key存在该数组中，则覆盖。

```java
final TreeNode<K,V> putTreeVal(HashMap<K,V> map, Node<K,V>[] tab,
                                       int h, K k, V v)
```

树版putVal

```java
final void treeifyBin(Node<K,V>[] tab, int hash)
```

```java
final Node<K,V>[] resize()
```

```java
// 在LinkedHashMap中有个排序的规则，是否按访问排序还是按插入排序，所以在HashMap中，就放了这三个空函数
// Callbacks to allow LinkedHashMap post-actions
void afterNodeAccess(Node<K,V> p) { }
void afterNodeInsertion(boolean evict) { }
void afterNodeRemoval(Node<K,V> p) { }
```

### 删

### 改

### 查

get和containsKey都会调用getNode，是get方法的具体实现

正如HashMap可以被看作是数组与链表的结合体，令**i = (n - 1) & hash**，其中n为table的长度，hash为key的hash值，对象会存储在数组中坐标为 *i* 所在的位置。

在getNode中有两个参数，一个hash是hash(key)，即key的哈希值，另一个key就是key对象。
所以在根据这个key进行查找的时候，首先根据key的hash值获得数组中对应位置的**Node = table[(n - 1) & hash]**。

然后以这个Node为链表的第一个节点，进行遍历，中间也要考虑这个Node是否为TreeNode，是的话，用treeNode的方式getTreeNode进行遍历。不是的话，就说明这个链表是一个普通的链表，直接do...while...遍历

```java
final Node<K,V> getNode(int hash, Object key)
```

### 其他

```java

public class ConstantObject {
    private int value;

    public ConstantObject(int value) {
        this.value = value;
    }

//    @Override
//    public boolean equals(Object o) {
//        return true;
//    }

    @Override
    public int hashCode() {
        return 1;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        ConstantObject that = (ConstantObject) o;
        return value == that.value;
    }

//    @Override
//    public int hashCode() {
//        return Objects.hash(value);
//    }

    public static void main(String[] args) {
        ConstantObject o1 = new ConstantObject(1);
        ConstantObject o2 = new ConstantObject(2);
        ConstantObject o3 = new ConstantObject(3);
        ConstantObject o4 = new ConstantObject(4);

        HashMap<ConstantObject, Integer> arr = new HashMap<>();
        arr.put(o1, 1);
        arr.put(o2, 2);
        arr.put(o3, 3);
        arr.put(o4, 4);

        System.out.println(arr.get(o2));
    }
}
```

如果存储的对象里，hashcode永远返回一个固定值，那么就会往hashmap里table里的一个位置元素后面固定增加节点。
如果equals永远返回true，存储还是存在不同的位置上，get返回的还是正确的值。
如果equals永远返回true并且hashcode返回固定值，hashmap只会有一个值

## WHY

- 允许value和key为null
- 不同步
- HashMap与HashTable大致相同，除了HashMap是不同步的，并且HashTable不接受为null的key和value
- 可以把HashMap看成数组加链表的实现，如果元素太多了的话，会自动转成树就是(treefied，树化)来提高查找速度
- HashMap由数组+链表组成，数组是HashMap的主体，链表主要是为了解决hash冲突
- 一个HashMap实例有两个参数影响它的性能：初始容量和装载因子，容量是散列表中的桶数，初始容量只是创建散列表时的容量。装载因子是衡量散列表完整性的度量标准，允许在容量自动增加之前获得。当散列表中的Entry数超过了装载因子和当前容量的乘积，hash表就rehashed（即内部数据结构重建），使散列表大约有两倍buckets数。

```word
例如，当散列表的Entry数超过0.75*16时，就rehashed。因为碰撞，此时数组中可能还没有12个桶被占用。
```

> 而为什么默认的装载因子是0.75，源代码如下：

```java
static final float DEFAULT_LOAD_FACTOR = 0.75f;
```

> 作为一般规则，默认装载因子（.75）在时间和空间成本之间提供了良好的权衡。较高的值会减少空间开销，但会增加查找成本（反映在HashMap类的大多数操作中，包括get和put）。在设置其初始容量时，应考虑映射中的预期条目数及其装载因子，以便最小化重新散列操作的数量。如果初始容量大于最大条目数除以装载因子，则不会发生重新装载操作。

- 如果说很多key有相同的hashcode，会降低hash table的表现。所以需要通过compareable，如果key支持java.lang.comparable，再进行一次排序插入进链表中，来降低影响

- hash(key)核心hash算法，在长度为2的幂前提下，hash值高位下移16位，参与取模，降低碰撞，另外取模部分利用2的幂减1来做&操作提高了性能。

- 继承Serializable接口，可是字段使用transient修饰，比如table,entrySet。原因是hashcode操作依赖jvm所处的环境因素,不同环境可能有不同的hash值，做一现成存储的内容既是序列化也无法通用.所以hashmap自己实现了writeObject和readObject，这里就需要知道java在序列化和反序列化一个类时是先调用writeObject和readObject,如果没有默认调用的是ObjectOutputStream的defaultWriteObject以及ObjectInputStream的defaultReadObject方法。

TREEIFY_THRESHOLD

HashMap扩容机制，即什么时候Rehash

## LinkedHashMap

### WHAT

有序map，且默认为插入顺序，不是线程安全的

双端链表

继承了HashMap，其中的节点Entry类也继承了HashMap中的Node类，并在基础上增加了before和after两个节点

```word
为什么说LinkedHashMap不是线程安全的，我觉得在很多操作里类似replaceAll，都是通过判断

int mc = modCount;
if (modCount != mc)
  throw new ConcurrentModificationException();

但是这个操作并不是原子操作

```

```java
LinkedHashMap<String, Integer> map = new LinkedHashMap<String, Integer>();
map.put("key1", 1);
map.put("kkk2", 2);
map.put("eee3", 3);
map.put("yyy4", 4);
map.put("kkk5", 5);
map.put("eee6", 6);
map.put("yyy7", 7);
map.put("zzz7", 8);
map.put("eee1", 9);
```

![LinkedHashMap Code Example](https://github.com/huangshijie/ImgRep/blob/master/LinkedHashMap%20Code%20Example.PNG)
同样的通过上面的debug截图可以看出来，LinkedHashMap是HashMap的一个子类，他里面同样有一个table，但是他使用的元素是自己的静态内部类Entry，这个静态内部类是HashMap.Node的子类。在HashMap.Node的基础上增加了两个Entry类型的属性before和after，这样就将所有的元素构成了一个链表。
上面的例子就可以画成如下图的结构。

![LinkedHashMap Example Table Variables](https://github.com/huangshijie/ImgRep/blob/master/LinkedHashMap%20Example%20Table%20Variables.png)

将上面的节点展开就可以得到一个链表如下图所示
![LinkedHashMap Example LinkedList Variables](https://github.com/huangshijie/ImgRep/blob/master/LinkedHashMap%20Example%20LinkedList%20Variables.png)
上图是插入排序，所以链表的顺序和插入的顺序有关，默认accessOrder为false

### HOW

#### 构造函数

![LinkedHashMap](https://github.com/huangshijie/ImgRep/blob/master/LinkedHashMap%20Outline.PNG)

在HashMap的基础上增加了三个变量head，tail和accessOrder

head和tail作为LinkedHashMap的两端，accessOrder控制的是元素存储在链表中的顺序
如果accessOrder为true的话，则会把访问过的元素放在链表后面，放置顺序是访问的顺序 
如果accessOrder为flase的话，则按插入顺序来遍历

## TreeMap

有序map

## ConcurrentHasMap


```java
// map get方法的failback机制，如果map中存在这个key，则返回这个key 对应的值，不存在则返回给的defaultVlue
Map.getOrDefault(Object key, V defaultValue)
```
