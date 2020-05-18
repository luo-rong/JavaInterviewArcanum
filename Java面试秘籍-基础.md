[TOC]

# 面试秘籍（基础）
## 1 Java
### 1.1 语言特性
#### 对面向对象的理解
面向过程的范式重点在于设计方法。面向对象的范式将数据和方法耦合在一起构成对象。使用面向对象范式的软件设计重点在对象以及对对象的操作上

#### Java语言的特性，和其他语言的区别
1. 一次编写，处处运行。不管是C语言还是C++，用它们编写的代码编译成的机器码都是只作用某一个特定的运行环境，当换一个环境或换一个CPU的时候原来的代码要有很大的变动才能正常运行。Java语言可以在不改变代码的情况下在不同的运行环境下正常运行，因为java代码编译的时候并不是编译成机器代码，而是字节码这种中间代码，通过JVM把字节码边运行边解释成适合的机器码，保证了正常运行
2. 安全性、多线程、动态等等优秀的特点

#### Java为什么可以跨平台
因为Java程序编译之后的代码不是能被硬件系统直接运行的代码，而是一种“中间码”——字节码。然后不同的硬件平台上安装有不同的Java虚拟机(JVM)，由JVM来把字节码再“翻译”成所对应的硬件平台能够执行的代码。因此对于Java编程者来说，不需要考虑硬件平台是什么，所以Java可以跨平台

#### Java多态有哪几种，如何实现（重写如何定位到对应方法）
1. 多态：对象的多种形态。（严格意义上的多态，只是继承！）
    1. 引用多态：
        1. 父类的引用可以指向本类的对象
        2. 父类的引用可以指向子类的对象
    2. 方法多态：
        1. 创建本类对象时，调用的方法为本类方法
        2. 创建子类对象时，调用的方法为子类重写的方法或者继承的方法
2. 动态绑定
    ```java
    Object o = new GeometricObject();   // 声明类型为Object，实际类型为GeometricObject
    System.out.println(o.toString());
    ```
    1. 声明类型：一个变量必须被声明为某种类型，实例可以使用声明类型或它的子类型的构造方法创建
    2. 实际类型：被变量引用的对象的实际类型
    
    代码中，`o`调用的到底是哪个`toString()`方法是由`o`的实际类型决定的，这称为动态绑定。动态绑定的工作机制：假设对象`o`是类`C1` `C2` ... `C(n)`的实例，其中`C(i-1)`是`C(i)`的子类，`C(n)`是最通用的类、`C1`是最特殊的类。在Java中`C(n)`是`Object`类。如果对象`o`调用一个方法p，那么Java虚拟机会依次在`C1` `C2` ... `C(n)`中查找方法p的实现，直到找到为止。一旦找到这个实现，就停止查找然后调用这个第一次找到的实现
    
    匹配方法的签名和绑定方法的实现是两个独立的事情：
    1. 引用变量的声明类型决定了编译时匹配哪个方法，编译器会在编译时，根据参数类型、参数个数、参数顺序找到匹配的方法（区分重载的方法）
    2. 实际类型决定Java虚拟机在运行时动态绑定方法的实现，一个方法可能在几个子类中都被实现

广义：https://www.runoob.com/java/java-polymorphism.html

### 1.2 关键字相关
#### `final` `finally` `finalize`区别，`final`修饰变量/方法/类
1. **final**：final可以用来修饰类，方法和变量（成员变量或局部变量）
    1. 修饰类：final修饰类的时，表明该类不能被其他类所继承。注意：final类中所有的成员方法都会隐式的定义为final方法
    2. 修饰方法：final修饰方法，表明该方法不能被重写。使用final方法的原因主要有两个：
        1. 锁定方法，以防止继承类对其进行更改
        2. 提升效率，在早期的java版本中，会将final方法转为内嵌调用，但若方法过于庞大，可能在性能上不会有多大提升。因此在最近版本中，不需要final方法进行这些优化了。
        
        注意：若父类中final方法的访问权限为private，将导致子类中不能直接继承该方法，因此，此时可以在子类中定义相同方法名的函数，此时不会矛盾，而是在子类中重新地定义了新方法

    3. 修饰变量：
        
        final修饰基本数据类型，表示该基本数据类型的值一旦在初始化后便不能发生变化；final修饰引用类型，则在对其初始化之后便不能再让其指向其他对象了，但该引用所指向的对象的内容是可以发生变化
        1. final修饰成员变量，表示常量，只能被赋值一次，必须要显示初始化并且赋值后值不可改变。有两种初始化方式：在变量声明的时候初始化；在这个变量所在的类的所有的构造函数中对这个变量赋初值
        2. final修饰函数的参数类型，说明该参数是只读型的。即你可以读取使用该参数，但是无法改变该参数的值
2. **finally**：`finally`作为异常处理的一部分，它只能用在`try...catch`语句中，并且附带一个语句块，表示这段语句最终（一般情况下）不管有没有抛出异常一定会被执行，经常被用在需要释放资源的情况下
    > 几种不会执行finally的情况：
    > 1. 与`finally`对应的`try`语句块没有被执行
    > 2. 在`try`语句块中执行了`System.exit(0)`语句，终止了Java虚拟机的运行
    > 3. 当一个线程在执行`try`语句块或者`catch`语句块时被打断（interrupted）或者被终止（killed）
    > 4. 在线程运行`try`语句块或者`catch`语句块时，突然死机或者断电

3. **finalize**：`finalize()`在`java.lang.Object`中定义，每一个对象都有这个方法。这个方法在GC启动，该对象被回收的时候被调用。其实GC可以回收大部分的对象（凡是`new`出来的对象，GC都能回收），所以一般是不需要程序员去实现`finalize()`。特殊情况下，需要程序员实现`finalize()`，当对象被回收的时候释放一些资源，比如：一个socket链接，在对象初始化时创建，整个生命周期内有效，那么就需要实现`finalize()`，关闭这个链接

#### `try...catch...finally`中`return`的执行结果
因为finally用法特殊，所以会撤销之前的return语句，继续执行最后的finally块中的代码。
```java
/**
 * try代码块已执行
 * try代码块已结束
 * finally代码块已执行
 * finally代码块已结束
 *
 * @return 3
 */
private int noErrorAndFinally() {
    try {
        System.out.println("try代码块已执行");
        System.out.println("try代码块已结束");
        return 1;
    } catch (Exception e) {
        System.out.println("catch代码块已执行");
        System.out.println("catch代码块已结束");
        return 2;
    } finally {
        System.out.println("finally代码块已执行");
        System.out.println("finally代码块已结束");
        return 3;
    }
}

/**
 * try代码块已执行
 * catch代码块已执行
 * catch代码块已结束
 * finally代码块已执行
 * finally代码块已结束
 *
 * @return 3
 */
private int hasErrorAndFinally() {
    try {
        System.out.println("try代码块已执行");
        int i = 1 / 0;
        System.out.println("i = " + i);
        System.out.println("try代码块已结束");
        return 1;
    } catch (Exception e) {
        System.out.println("catch代码块已执行");
        System.out.println("catch代码块已结束");
        return 2;
    } finally {
        System.out.println("finally代码块已执行");
        System.out.println("finally代码块已结束");
        return 3;
    }
}

/**
 * try代码块已执行
 * catch代码块已执行
 * catch代码块已结束
 *
 * @return 2
 */
private int hasErrorWithoutFinally() {
    try {
        System.out.println("try代码块已执行");
        int i = 1 / 0;
        System.out.println("i = " + i);
        System.out.println("try代码块已结束");
        return 1;
    } catch (Exception e) {
        System.out.println("catch代码块已执行");
        System.out.println("catch代码块已结束");
        return 2;
    }
}
```

### 1.3 常用方法
#### `sleep()`和`wait()`的区别
1. `wait()`属于`Object`类，`sleep()`属于`Thread`类
2. `wait()`会释放对象锁而`sleep()`不会
    1. 在调用`sleep()`方法的过程中，线程不会释放对象锁
    2. 当调用`wait()`方法的时候，线程会放弃对象锁，进入等待此对象的等待池，只有针对此对象调用`notify()`或`notifyAll()`方法后本线程才进入对象锁池准备获取对象锁进入运行状态
3. `wait()`需要在同步块中使用，`sleep()`可以在任何地方使用
4. `sleep()`需要捕获异常，`wait()`不需要

#### `==`和`equals()`区别
1. 基本数据类型，也称原始数据类型。`byte` `short` `char` `int` `long` `float` `double` `boolean`他们之间的比较，用`==`，比较的是他们的值
2. 复合数据类型（类）：
    1. `==`：比较的是对象在内存中的存放地址
    2. 基类Object中的基类中定义的`equals()`的方法，默认返回`==`运算的结果；某些类中重写了`equals()`方法：`String` `Integer` `Double` `Date`等，返回值相等

#### `equals()` `hashcode()`区别
在Java中任何一个对象都具备`equals()`和`hashcode()`两个方法，因为他们是在`Object`类中定义的
1. `equals()`方法用来判断两个对象是否相同，如果相同则返回`true`，否则返回`false`
2. `hashcode()`方法返回`int`，在`Object`类中默认实现是：将该对象的内存地址转换成一个整数返回

其中两个重要规范
1. 如果`equals()`返回`true`，即两个对象相同，那么他们的`hashcode`应该相等。因此，若重写`equals()`方法，有必要重写`hashcode()`方法，确保通过`equals()`方法判断结果为`true`的两个对象具备相等的`hashcode()`返回值。注意：这个只是规范，如果你非要写一个类让`equals()`返回`true`而`hashcode()`返回两个不相等的值，编译和运行都是不会报错的。不过这样违反了Java规范，程序也就埋下了BUG
2. 如果`equals()`返回`false`，即两个对象不相同，并不要求对这两个对象调用`hashcode()`方法得到两个不相同的数。说的简单点就是：如果两个对象不相同，他们的hashcode可能相同

根据这两个规范，可以得到如下推论： 
1. 如果两个对象`equals`，Java运行时环境会认为他们的`hashcode`一定相等
2. 如果两个对象不`equals`，他们的`hashcode`有可能相等
3. 如果两个对象`hashcode`相等，他们不一定`equals`
4. 如果两个对象`hashcode`不相等，他们一定不`equals` 

> `hashCode()`在哈希表中起作用，如`HashSet`、`HashMap`等。当我们向哈希表中添加对象`object`时，首先调用`hashCode()`方法计算`object`的哈希码，通过哈希码可以直接定位`object`在哈希表中的位置（一般是哈希码对哈希表大小取余）。如果该位置没有对象，可以直接将`object`插入该位置；如果该位置有对象，则调用`equals()`方法比较这些对象与`object`是否相等，如果相等，则不需要保存`object`；如果不相等，则将该对象加入到对应位置中

### 1.4 常用类
#### `int` `Integer`的区别
1. `Integer`是`int`的包装类，`int`则是Java的一种基本数据类型 
2. `Integer`变量必须实例化后才能使用，而int变量不需要 
3. `Integer`实际是对象的引用，当`new Integer()`时，实际上是生成一个指针指向此对象；而`int`则是直接存储数据值 
4. `Integer`的默认值是`null`，`int`的默认值是0

关于`Integer`和`int`的比较
1. 由于`Integer`变量实际上是对一个`Integer`对象的引用，所以两个通过`new`生成的`Integer`变量永远是不相等的（因为内存地址不同）
    
    ```java
    // result: false
    Integer a = new Integer(100);
    Integer b = new Integer(100);
    ```
    
2. 对于两个非`new`生成的`Integer`对象，进行比较时，如果两个变量的值在区间-128到127之间，则比较结果为`true`，如果两个变量的值不在此区间，则比较结果为`false`
    
    ```java
    // result: true
    Integer a = 100;
    Integer b = 100;
    
    // result: false
    a = 128;
    b = 128;
    ```
    
    java在编译`Integer i = 100;`时，会翻译成为`Integer i = Integer.valueOf(100);`，而Java API中对`Integer.valueOf()`定义如下：对于-128到127之间的数，会进行缓存
    
    ```java
    /**
     * This method will always cache values in the range -128 to 127,
     * inclusive, and may cache other values outside of this range.
     */
    public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
    ```
    
3. 非`new Integer()`变量和`new Integer()`生成的变量比较时，结果为false
    1. 值在-128到127之间，非`new Integer()`变量在缓存中，`new Integer()`生成的变量为对象的引用，故不等
    
    ```java
    // result: false
    Integer a = 100;
    Integer b = new Integer(100);
    ```
    
    2. 值不在-128到127之间，两种方法生成的均为对象的引用，故不等
    
    ```java
    // result: false
    Integer a = 258;
    Integer b = new Integer(258);
    ```
    
4. Integer变量和int变量比较时，只要两个变量的值是向等的，则结果为true（因为包装类Integer和基本数据类型int比较时，java会自动拆包装为int，然后进行比较，实际上就变为两个int变量的比较）
    
    ```java
    // result: true
    Integer a = new Integer(100);
    int c = 100;
    ```

#### 常用集合类
集合 | 底层结构 | 存储类型 | 长度是否有上限 | 扩增/大小 | 线程安全 | 性能 | 备注
---|---|---|---|---|---|---|---
ArrayList | 数组 | 随机 | 有，`int hugeCapacity()` | 自动扩增/1.5倍 | 不安全 | 查询性能好 |
Vector | 数组 | 随机 | 有，`int hugeCapacity()` | 自动扩增/2倍 | 安全 | |
Stack | 数组 | 随机 | 有，`int hugeCapacity()` | 自动扩增/2倍 | 安全 | | 继承自Vector
LinkedList | 双向链表 | 顺序 | 无，直到内存满 | | 不安全 | 增加性能好 |
HashMap | | | 有，`MAXIMUM_CAPACITY = 1<<30` | 自动扩增/2倍 | 不安全 | |

#### `ArrayList`的实现，为什么查找效率高
`ArrayList`采用数组数组实现。查找效率高，因为`ArrayList`是连续存放元素的，找到第一个元素的首地址，再加上每个元素的占据的字节大小就能定位到对应的元素

#### `Array` `ArrayList`区别
`ArrayList`可以算是`Array`（`[]`）的加强版，`ArrayList`想象成一种会自动扩增容量的`Array`
1. 存储内容
    1. `Array`数组可存放基本类型或对象类型；`Array`数组在存放的时候一定是同种类型的元素
    2. `ArrayList`只能包含对象类型；`ArrayList`可存放不同类型的元素，因为`ArrayList`可以存储`Object`
2. 空间大小
    1. `Array`最高效，但是其容量固定且无法动态改变
    2. `ArrayList`动态增长，但牺牲效率。若空间不够，会扩容1.5倍，然后将所有元素复制到新数组中并抛弃旧数组，每次添加新的元素的时候都会检查内部数组的空间是否足够
3. 相互转换
    ```java
    // ArrayList => Array
    ArrayList<Integer> arrayList = new ArrayList(10);
    Integer[] array = arrayList.toArray(new Integer[20]);
    
    // Array => ArrayList
    Integer[] array = new Integer[10];
    ArrayList<Integer> arrayList = new ArrayList(Arrays.asList(array));
    ```

#### `ArrayList` `LinkedList`区别
集合类 | `ArrayList` | `LinkedList`
---|---|---
继承关系 | `Collection<-List<-ArrayList` | `Collection<-List<-LinkedList`
底层数据结构 | 数组 | 双向链表
存取类型 | 随机存取 | 顺序存取
长度限制 | 有上限 `int hugeCapacity()` | 无上限 直到内存满
扩增/大小 | 自动扩增/1.5倍 | 
线程安全 | 不安全 | 不安全
性能 | 查询操作性能好 | 插入操作性能好

#### `CopyOnWriteArrayList`类
`CopyOnWriteArrayList`是`ArrayList`的线程安全版本
1. 写操作：使用了一种叫写时复制的方法，当有新元素添加到`CopyOnWriteArrayList`时，先从原有的数组中拷贝一份出来，然后在新的数组做写操作，写完之后，再将原来的数组引用指向到新数组。整个`add()`操作都是在锁的保护下进行，避免在多线程并发`add()`的时候，复制出多个副本，导致最终的数组数据不是我们期望的
2. 读操作：读操作是可以不用加锁。如果有线程并发的读，则分几种情况： 
    1. 如果写操作未完成，那么直接读取原数组的数据
    2. 如果写操作完成，但是引用还未指向新数组，那么也是读取原数组数据
    3. 如果写操作完成，并且引用已经指向了新的数组，那么直接从新数组中读取数据

#### `Stack`中的主要方法
1. `push()`：Pushes an item onto the top of this stack. This has exactly
    
    ```java
    public E push(E item) {
        addElement(item);
        return item;
    }
    ```
    
2. `pop()`：Removes the object at the top of this stack and returns that object as the value of this function.
    
    ```java
    public synchronized E pop() {
        E obj;
        int len = size();
        obj = peek();
        removeElementAt(len - 1);
        return obj;
    }
    ```
    
3. `peek()`：Looks at the object at the top of this stack without removing it from the stack.
    
    ```java
    public synchronized E peek() {
        int len = size();
        if (len == 0)
            throw new EmptyStackException();
        return elementAt(len - 1);
    }
    ```
    
4. `empty()`：Tests if this stack is empty.
    
    ```java
    public boolean empty() {
        return size() == 0;
    }
    ```
    
5. `search()`：Returns the 1-based position where an object is on this stack. If the object o occurs as an item in this stack, this method returns the distance from the top of the stack of the occurrence nearest the top of the stack; the topmost item on the stack is considered to be at distance 1. The equals method is used to compare o to the items in this stack.
    
    ```java
    public synchronized int search(Object o) {
        int i = lastIndexOf(o);
        if (i >= 0) {
            return size() - i;
        }
        return -1;
    }
    ```

#### `HashMap`基于什么数据结构实现
1. JDK1.8以前
    1. 数据结构：链表散列，数组+链表
    2. 键值对内部类：`Entry`
2. JDK1.8及以后
    1. 数据结构：数组+链表+红黑树（红黑树可提高效率）
    2. 键值对内部类：`Node`&`TreeNode`
        ```java
        static class Node<K,V> implements Map.Entry<K,V>{}
        static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V>{}
        ```

#### `HashMap`如何处理hash冲突
链地址法
1. 计算出hash值，计算位置（因为n为table长度，为2的幂，故可用位运算计算余数）`i = (n - 1) & hash`
2. 若当前发生hash冲突
    1. 找到相同key，则用带插入值覆盖原有值，返回旧值
    2. 未找到相同key，则插入node，返回null
        1. 红黑树：添加节点
        2. 链表：在尾部插入

#### `HashMap` `ConcurrentHashMap` `LinkedHashMap` `HashTable`的区别
接口`java.util.Map`，Map主要用于存储健值对，根据键得到值，因此不允许键重复（重复则覆盖），但允许值重复
1. Hashmap
    
    ```java
    public class HashMap<K,V> extends AbstractMap<K,V> implements Map<K,V>, Cloneable, Serializable {}
    ```
    
    最常用的Map，它根据键的HashCode值存储数据，根据键可以直接获取它的值，具有很快的访问速度，遍历时，取得数据的顺序是完全随机的。HashMap最多只允许一条记录的键为`Null`，允许多条记录的值为`Null`；`HashMap`不支持线程的同步，即任一时刻可以有多个线程同时写HashMap，可能会导致数据的不一致。如果需要同步，可以用`Collections`的`synchronizedMap`方法使`HashMap`具有同步的能力，或者使用`ConcurrentHashMap`
2. `HashTable`
    
    ```java
    public class Hashtable<K,V> extends Dictionary<K,V> implements Map<K,V>, Cloneable, java.io.Serializable {}
    ```
    
    与HashMap类似，它继承自`Dictionary`类，不同的是：它不允许记录的键或者值为`Null`，它支持线程的同步，即任一时刻只有一个线程能写`Hashtable`，因此也导致了`Hashtable`在写入时会比较慢
3. `LinkedHashMap`
    
    ```java
    public class LinkedHashMap<K,V> extends HashMap<K,V> implements Map<K,V> {}
    ```
    
    `HashMap`的一个子类，保存了记录的插入顺序，在用`Iterator`遍历`LinkedHashMap`时，先得到的记录肯定是先插入的．也可以在构造时用带参数，按照应用次数排序。在遍历的时候会比`HashMap`慢，不过有种情况例外，当`HashMap`容量很大，实际数据较少时，遍历起来可能会比`LinkedHashMap`慢，因为`LinkedHashMap`的遍历速度只和实际数据有关，和容量无关，而`HashMap`的遍历速度和容量有关
4. `ConcurrentHashMap`
    
    ```java
    public class ConcurrentHashMap<K,V> extends AbstractMap<K,V> implements ConcurrentMap<K,V>, Serializable {}
    public interface ConcurrentMap<K, V> extends Map<K, V> {}
    ```
    
    不允许key/value为空，`Concurrenthashmap`线程安全
    1. JDK1.7中采用`Segment`+`HashEntry`实现，lock加在`Segment`上
    2. JDK1.8中采用`Node`+`CAS`+`Synchronized`实现

#### 如何让`HashMap`线程安全 | `HashTable`、`ConcurrentHashmap`如何实现线程安全
1. `HashTable`的方式：将容器中数据进行操作的方法用`synchronized`关键字修饰
2. `ConcurrentHashMap`的方式：采用分段锁思路，`segment`+`HashEntry`（JDK1.7）；`Node`+`CAS`+`Synchronized`（JDK1.8）

#### `CurrentHashMap`实现线程安全的关键（JDK1.7 分段锁）
[ConcurrentHashMap实现原理及源码分析](https://www.cnblogs.com/chengxiao/p/6842045.html)

JDK1.7中采用`Segment`+`HashEntry`实现，lock加在`Segment`上。`Segment`继承了`ReentrantLock`，所以它就是一种可重入锁。在`ConcurrentHashMap`，一个`Segment`就是一个子哈希表，`Segment`里维护了一个`HashEntry`数组，并发环境下，对于不同`Segment`的数据进行操作是不用考虑锁竞争的，对于同一个Segment的操作才需考虑线程同步
1. `get()`方法：先定位`Segment`，再定位`HashEntry`。`get()`方法无需加锁，由于其中涉及到的共享变量都使用`volatile`修饰，`volatile`可以保证内存可见性，所以不会读取到过期数据
2. `put()`方法
    1. 定位segment并确保定位的Segment已初始化
    2. 调用Segment的put方法：`tryLock()`不成功时会遍历定位到的`HashEnry`位置的链表（遍历主要是为了使CPU缓存链表），若找不到，则创建`HashEntry`。`tryLock()`一定次数后，则lock。若遍历过程中，由于其他线程的操作导致链表头结点变化，则需要重新遍历

#### `String` `StringBuffer` `StringBuilder`区别，`String`是否线程安全为什么
`String` `StringBuffer` `StringBuilder`都是`final`类，内部都是`char[]`实现，三者在执行速度方面：`StringBuilder` > `StringBuffer` > `String`
1. String，字符串常量，操作少量的数据用，类中定义的`char[]`是`final`的，不可变，因此是线程安全的
2. StringBuffer，字符串变量（线程安全），单线程操作字符串缓冲区下操作大量数据
3. StringBuilder，字符串变量（非线程安全），多线程操作字符串缓冲区下操作大量数据

```
// 补充说明
为什么String要设计成不可变：https://www.cnblogs.com/sessionbest/articles/8688569.html

在java中，String被设计成final类，那为什么平时使用时，String的值可以被改变呢？
字符串常量池是java堆内存中一个特殊的存储区域，当我们建立一个String对象时，假设常量池不存在该字符串，则创建一个，若存在则直接引用已经存在的字符串。当我们对String对象值改变的时候，例如 String a="A"; a="B" 。a是String对象的一个引用（我们这里所说的String对象其实是指字符串常量），当a=“B”执行时，并不是原本String对象("A")发生改变，而是创建一个新的对象("B")，令a引用它。
    
笔试题：https://www.cnblogs.com/AmyZheng/p/9415064.html
```

### 1.5 常用接口
#### `Comparable`和`Comparator`的区别
1. Comparable

    `Comparable`接口中只有一个方法`compareTo()`，就是调用方法的当前对象`this`和`o`进行比较，若返回值大于0则`this > o`，返回值等于0则是`this = o`，返回值小于0则是`this < o`
    ```java
    public interface Comparable<T> {
        public int compareTo(T o);
    }
    ```
2. Comparator
    
    `Comparator`接口中方法很多，但是我们只需要实现一个`compare()`。
    > JDK8以后的新特性：在接口中用`default`修饰的方法可以有方法体，在实现接口的时候可以不用重写

    `compare()`比较`o1`和`o2`，返回值大于0则`o1 > o2`，以此类推。对于`compare()`来说`this`是谁不重要，所比较的两个对象都已经传入到方法中
    ```java
    public interface Comparator<T> {
        int compare(T o1, T o2);
        // default ... ...
    }
    ```
    `Comparator`可实现外部比较器，在我们设计类初时，并不需要它有比较功能，在后期扩展业务时，`Comparator`的存在可以使我们在不修改源代码的情况下来完成需求：只需要新定义一个比较器来实现`Comparator`，重写`compare()`方法并将类对象传进去
    ```java
    // 例子
    static class UserComparator implements Comparator<User> {
        @Override
        public int compare(User o1, User o2) {
            // ... ...
        }
    }
    ```

### 1.6 多线程
#### 线程安全怎么理解
当多个线程访问某个方法时，不管你通过怎样的调用方式或者说这些线程如何交替的执行，我们在主程序中不需要去做任何的同步，这个类的结果行为都是我们设想的正确行为，那么我们就可以说这个类是线程安全的

#### 如何创建一个线程
[https://www.cnblogs.com/songshu120/p/7966314.html](https://www.cnblogs.com/songshu120/p/7966314.html)
1. 继承`Thread`类创建线程类
    1. 定义`Thread`类的子类，并重写该类的`run()`方法，该`run()`方法的方法体就代表了线程要完成的任务，因此把`run()`方法称为执行体
    2. 创建`Thread`子类的实例，即创建了线程对象
    3. 调用线程对象的`start()`方法来启动该线程
2. 通过`Runnable`接口创建线程类：线程的执行流程很简单，当执行代码`start()`时，就会执行对象中重写的`run()`方法，该方法执行完成后，线程就消亡了
    1. 定义`Runnable`接口的实现类，并重写该接口的`run()`方法，该`run()`方法的方法体同样是该线程的线程执行体
    2. 创建`Runnable`实现类的实例，并以此实例作为`Thread`的`target`来创建`Thread`对象，该`Thread`对象才是真正的线程对象
    3. 调用线程对象的`start()`方法来启动该线程
3. 通过`Callable`和`Future`创建线程
    1. 创建`Callable`接口的实现类，并实现`call()`方法，该`call()`方法将作为线程执行体，并且有返回值
    2. 创建`Callable`实现类的实例，使用`FutureTask`类来包装`Callable`对象，该`FutureTask`对象封装了该`Callable`对象的`call()`方法的返回值（`FutureTask`是一个包装器，它通过接受`Callable`来创建，它同时实现了`Future`和`Runnable`接口）
    3. 使用`FutureTask`对象作为`Thread`对象的`target`创建并启动新线程
    4. 调用`FutureTask`对象的`get()`方法来获得子线程执行结束后的返回值

#### ==实现多线程有几种实现方式==
[JAVA多线程实现的四种方式](https://www.cnblogs.com/felixzh/p/6036074.html)

Java多线程实现方式主要有四种：继承Thread类、实现Runnable接口、实现Callable接口通过FutureTask包装器来创建Thread线程、使用ExecutorService、Callable、Future实现有返回结果的多线程。

其中前两种方式线程执行完后都没有返回值，后两种是带返回值的。

1. 继承Thread类创建线程
    
    Thread类本质上是实现了Runnable接口的一个实例，代表一个线程的实例。启动线程的唯一方法就是通过Thread类的start()实例方法。start()方法是一个native方法，它将启动一个新线程，并执行run()方法。这种方式实现多线程很简单，通过自己的类直接extend Thread，并复写run()方法，就可以启动新线程并执行自己定义的run()方法。例如：
    ```java
    public class MyThread extends Thread {  
    　　public void run() {  
    　　 System.out.println("MyThread.run()");  
    　　}  
    }  
     
    MyThread myThread1 = new MyThread();  
    MyThread myThread2 = new MyThread();  
    myThread1.start();  
    myThread2.start();
    ```
2. 实现Runnable接口创建线程
    
    如果自己的类已经extends另一个类，就无法直接extends Thread，此时，可以实现一个Runnable接口，如下：
    ```java
    public class MyThread extends OtherClass implements Runnable {  
    　　public void run() {  
    　　 System.out.println("MyThread.run()");  
    　　}  
    }
    ```
    为了启动MyThread，需要首先实例化一个Thread，并传入自己的MyThread实例：
    ```java
    MyThread myThread = new MyThread();  
    Thread thread = new Thread(myThread);  
    thread.start(); 
    ```
    事实上，当传入一个Runnable target参数给Thread后，Thread的run()方法就会调用target.run()，参考JDK源代码：
    ```java
    public void run() {  
    　　if (target != null) {  
    　　 target.run();  
    　　}  
    }
    ```
3. 实现Callable接口通过FutureTask包装器来创建Thread线程

    Callable接口（也只有一个方法）定义如下：
    ```java
    public interface Callable<V> { 
      V call（） throws Exception;
    }
    ```
    ```java
    public class SomeCallable<V> extends OtherClass implements Callable<V> {
        @Override
        public V call() throws Exception {
            // TODO Auto-generated method stub
            return null;
        }
    }
    ```
    ```java
    Callable<V> oneCallable = new SomeCallable<V>();   
    //由Callable<Integer>创建一个FutureTask<Integer>对象：   
    FutureTask<V> oneTask = new FutureTask<V>(oneCallable);   
    //注释：FutureTask<Integer>是一个包装器，它通过接受Callable<Integer>来创建，它同时实现了Future和Runnable接口
    //由FutureTask<Integer>创建一个Thread对象：
    Thread oneThread = new Thread(oneTask);   
    oneThread.start();   
    //至此，一个线程就创建完成了
    ```
4. 使用ExecutorService、Callable、Future实现有返回结果的线程

    ExecutorService、Callable、Future三个接口实际上都是属于Executor框架。返回结果的线程是在JDK1.5中引入的新特征，有了这种特征就不需要再为了得到返回值而大费周折了。而且自己实现了也可能漏洞百出。
    
    可返回值的任务必须实现Callable接口。类似的，无返回值的任务必须实现Runnable接口。
    
    执行Callable任务后，可以获取一个Future的对象，在该对象上调用get就可以获取到Callable任务返回的Object了。
    
    注意：get方法是阻塞的，即：线程无返回结果，get方法会一直等待。
    
    再结合线程池接口ExecutorService就可以实现传说中有返回结果的多线程了。
    
    下面提供了一个完整的有返回结果的多线程测试例子，在JDK1.5下验证过没问题可以直接使用。代码如下：
    ```java
    import java.util.concurrent.*;
    import java.util.Date;
    import java.util.List;
    import java.util.ArrayList;
    
    /**
     * 有返回值的线程
     */
    @SuppressWarnings("unchecked")
    public class Test {
        public static void main(String[] args) throws ExecutionException,
                InterruptedException {
            System.out.println("----程序开始运行----");
            Date date1 = new Date();
    
            int taskSize = 5;
            // 创建一个线程池  
            ExecutorService pool = Executors.newFixedThreadPool(taskSize);
            // 创建多个有返回值的任务  
            List<Future> list = new ArrayList<Future>();
            for (int i = 0; i < taskSize; i++) {
                Callable c = new MyCallable(i + " ");
                // 执行任务并获取Future对象  
                Future f = pool.submit(c);
                // System.out.println(">>>" + f.get().toString());  
                list.add(f);
            }
            // 关闭线程池  
            pool.shutdown();
    
            // 获取所有并发任务的运行结果  
            for (Future f : list) {
                // 从Future对象上获取任务的返回值，并输出到控制台  
                System.out.println(">>>" + f.get().toString());
            }
    
            Date date2 = new Date();
            System.out.println("----程序结束运行----，程序运行时间【"
                    + (date2.getTime() - date1.getTime()) + "毫秒】");
        }
    }
    
    class MyCallable implements Callable<Object> {
        private String taskNum;
    
        MyCallable(String taskNum) {
            this.taskNum = taskNum;
        }
    
        public Object call() throws Exception {
            System.out.println(">>>" + taskNum + "任务启动");
            Date dateTmp1 = new Date();
            Thread.sleep(1000);
            Date dateTmp2 = new Date();
            long time = dateTmp2.getTime() - dateTmp1.getTime();
            System.out.println(">>>" + taskNum + "任务终止");
            return taskNum + "任务返回运行结果,当前任务时间【" + time + "毫秒】";
        }
    }
    ```

#### 实现Runnable接口要实现什么方法
```java
@Override
public void run() {}
```

#### 【自行整理】线程池`ThreadPoolExecutor`
1. `ThreadPoolExecutor`执行`execute()`方法分4步：
    1. 如果当前运行的线程少于`corePoolSize`，则创建新线程来执行任务（需要获取全局锁）
    2. 如果运行的线程等于或多于`corePoolSize`，则将任务加入`BlockingQueue`
    3. 如果无法将任务加入`BlockingQueue`（队列已满），则创建新的线程来处理任务（需要获取全局锁）
    4. 如果创建新线程将使当前运行的线程超出`maximumPoolSize`，任务将被拒绝，并调用`RejectedExecutionHandler.rejectedExecution()`方法
    
    `ThreadPoolExecutor`采取上述步骤的总体设计思路，是为了在执行`execute()`方法时，尽可能地避免获取全局锁（那将会是一个严重的可伸缩瓶颈）。在`ThreadPoolExecutor`完成预热之后（当前运行的线程数大于等于`corePoolSize`），几乎所有的`execute()`方法调用都是执行步骤2，而步骤2不需要获取全局锁
    
    线程池处理流程图：
    
    ![image](https://note.youdao.com/yws/public/resource/be02344987843483827564c4dc494b13/xmlnote/4A8F300DE6C6478DB5D4798E9206FF16/8337)
2. 线程池中的线程执行任务分2步：
    1. 在`execute()`方法中创建一个线程时，会让这个线程执行当前任务
    2. 这个线程执行完任务后，会反复从`BlockingQueue`获取任务来执行

#### `ThreadPoolExcutor()`创建线程池的主要参数

```java
new ThreadPoolExecutor(corePoolSize, maximumPoolSize, keepAliveTime, milliseconds,runnableTaskQueue, handler);
```

1. `corePoolSize`：线程池的基本大小。当提交一个任务到线程池时，线程池会创建一个线程来执行任务，即使其他空闲的基本线程能够执行新任务也会创建线程，等到需要执行的任务数大于线程池基本大小时就不再创建。如果调用了线程池的`prestartAllCoreThreads()`方法，线程池会提前创建并启动所有基本线程
2. `runnableTaskQueue`：任务队列。用于保存等待执行的任务的阻塞队列。可以选择以下几个阻塞队列
    1. `ArrayBlockingQueue`：基于数组结构的有界阻塞队列，此队列按FIFO（先进先出）原则对元素进行排序
    2. `LinkedBlockingQueue`：基于链表结构的阻塞队列，此队列按FIFO排序元素，吞吐量通常要高于ArrayBlockingQueue。静态工厂方法Executors.newFixedThreadPool()使用了这个队列
    3. `SynchronousQueue`：不存储元素的阻塞队列。每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常要高于Linked-BlockingQueue，静态工厂方法Executors.newCachedThreadPool使用了这个队列
    4. `PriorityBlockingQueue`：具有优先级的无限阻塞队列
3. `maximumPoolSize`：线程池最大数量。线程池允许创建的最大线程数。如果队列满了，并且已创建的线程数小于最大线程数，则线程池会再创建新的线程执行任务。值得注意的是，如果使用了无界的任务队列这个参数就没什么效果
4. `ThreadFactory`：用于设置创建线程的工厂，可以通过线程工厂给每个创建出来的线程设置更有意义的名字。使用开源框架`guava`提供的`ThreadFactoryBuilder`可以快速给线程池里的线程设置有意义的名字
5. `RejectedExecutionHandler`：饱和策略。当队列和线程池都满了，说明线程池处于饱和状态，那么必须采取一种策略处理提交的新任务。这个策略默认情况下是`AbortPolicy`，表示无法处理新任务时抛出异常。在JDK 1.5中Java线程池框架提供了以下4种策略：
    1. `AbortPolicy`：直接抛出异常
    2. `CallerRunsPolicy`：只用调用者所在线程来运行任务
    3. `DiscardOldestPolicy`：丢弃队列里最近的一个任务，并执行当前任务
    4. `DiscardPolicy`：不处理，丢弃掉

#### 线程池有什么用，为什么要使用线程池
1. 降低资源消耗。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。 
2. 提高响应速度。当任务到达时，任务可以不需要等到线程创建就能执行。
3. 提高线程的可管理性，线程是稀缺资源，如果无限制地创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一分配、调优和监控。

#### ThreadLocal
https://www.jianshu.com/p/98b68c97df9b

`ThreadLocal`是一个本地线程副本变量工具类。多线程中的对象使用`ThreadLocal`维护时，`ThreadLocal`为每个使用该变量的线程分配一个独立的变量副本，每个线程可以独立地改变自己的副本，不会影响其他线程对应的副本

1. 实现思路：
    1. 每个`Thread`线程内部都有一个`ThreadLocalMap`，`ThreadLocalMap`里面存储一份线程自己的变量副本：
        1. key：线程本地对象（变量对应的`ThreadLocal`实例）
        2. value：线程的变量副本（变量的本地值）
    
        ```java
        ThreadLocal.ThreadLocalMap threadLocals = null;
        // ThreadLocalMap内的Entry
        Entry(ThreadLocal<?> k, Object v) {}
        ```
    
    2. `Thread`内部的`ThreadLocalMap`由`ThreadLocal`维护的，由`ThreadLocal`负责向`ThreadLocalMap`获取和设置线程的变量值
2. 方法：
    1. `get()`：用于获取当前线程的副本变量值
    2. `set()`：用于保存当前线程的副本变量值
    3. `initialValue()`：为当前线程初始副本变量值
    4. `remove()`：移除当前前程的副本变量值。
3. Hash冲突解决：采用线性探测的方式，根据初始`key`的`hashcode`确定元素在`table`数组中的位置，如果发现这个位置上已经有其他`key`的元素，则利用固定的算法寻找一定步长的下个位置，依次判断，直至找到能够存放的位置。`ThreadLocalMap`解决Hash冲突的方式就是简单的步长加1或减1
    
    ```java
    // get()时使用
    private static int nextIndex(int i, int len) {
        return ((i + 1 < len) ? i + 1 : 0);
    }
    
    // set()时使用
    private static int prevIndex(int i, int len) {
        return ((i - 1 >= 0) ? i - 1 : len - 1);
    }
    ```
    
4. `ThreadLocalMap`的问题：由于`ThreadLocalMap`的`key`是弱引用，而`value`是强引用。这就导致`ThreadLocal`在没有外部对象强引用时，发生GC时弱引用`key`会被回收，而`value`不会回收，如果创建`ThreadLocal`的线程一直持续运行，那么这个`Entry`对象中的`value`就有可能一直得不到回收，发生内存泄露。避免泄漏：在调用`ThreadLocal`的`get()` `set()`方法使用完成后调用`remove()`方法，将`Entry`节点和`Map`的引用关系移除，这样整个`Entry`对象在GC Roots分析后就变成不可达了，下次GC的时候就可以被回收
    
    ```java
    ThreadLocal<Session> threadLocal = new ThreadLocal<Session>();
    try {
        threadLocal.set(new Session(1, "Misout的博客"));
        // 其它业务逻辑
    } finally {
        threadLocal.remove();
    }
    ```

### 1.7 锁
#### Java中的锁有几种
1. `synchronized`关键字
2. `java.util.concurrent.locks`包下常用的类
    1. `ReentrantLock`：可重入锁，实现了`Lock`接口，并且提供了更多的方法
    2. `ReentrantReadWriteLock`：可重入的读写锁，实现了`ReadWriteLock`接口，最主要的有两个方法：`readLock()`和`writeLock()`用来获取读锁和写锁
    3. `StampedLock`：JDK8新增，该锁提供了三种模式的读写控制
        1. 写锁writeLock，是个排它锁或者叫独占锁
        2. 悲观读锁readLock，是个共享锁
        3. 乐观读锁tryOptimisticRead（在操作数据前并没有通过CAS设置锁的状态）

#### 乐观锁和悲观锁
1. 悲观锁
    1. 总是假设最坏的情况，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻塞直到它拿到锁（共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程）
    2. 传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。Java中`synchronized`和`ReentrantLock`等独占锁就是悲观锁思想的实现
2. 乐观锁
    1. 总是假设最好的情况，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号机制和CAS算法实现
    2. 乐观锁适用于多读的应用类型，这样可以提高吞吐量，像数据库提供的类似于`write_condition`机制，其实都是提供的乐观锁。Java中`java.util.concurrent.atomic`包下面的原子变量类就是使用了乐观锁的一种实现方式CAS实现的

#### `Synchronized` `ReentrantLock`区别
1. 相似点：都是加锁方式同步，而且都是阻塞式的同步，也就是说当如果一个线程获得了对象锁，进入了同步块，其他访问该同步块的线程都必须阻塞在同步块外面等待，而进行线程阻塞和唤醒的代价是比较高的
2. 功能区别
    1. `Synchronized`是java语言的关键字，是原生语法层面的互斥，需要jvm实现
    2. `ReentrantLock`它是JDK1.5之后提供的API层面的互斥锁，需要`lock()`和`unlock()`方法配合`try/finally`语句块来完成
3. 便利性
    1. `Synchronized`的使用比较方便简洁，并且由编译器去保证锁的加锁和释放
    2. `ReenTrantLock`需要手工声明来加锁和释放锁，为了避免忘记手工释放锁造成死锁，所以最好在`finally`中声明释放锁
4. 锁的细粒度和灵活度：很明显`ReenTrantLock`优于`Synchronized`
5. 性能的区别：在`Synchronized`优化以前，`synchronized`的性能是比`ReenTrantLock`差很多，但是自从`Synchronized`引入了偏向锁、轻量级锁（自旋锁）后，两者的性能就差不多了。（在两种方法都可用的情况下，官方甚至建议使用`synchronized`）
    
    > 其实synchronized的优化感觉就借鉴了ReenTrantLock中的CAS技术。都是试图在用户态就把加锁问题解决，避免进入内核态的线程阻塞。

#### `volatile`的作用及适用场景
[Java 理论与实践：正确使用 Volatile 变量](https://www.ibm.com/developerworks/cn/java/j-jtp06197.html)

`volatile`是轻量级的`synchronized`，它在多处理器开发中保证了共享变量的“可见性”：当一个线程修改一个共享变量时，另外一个线程能读到这个修改的值

只能在有限的一些情形下使用`volatile`变量替代锁，例如：状态标志。要` volatile`变量提供理想的线程安全，必须同时满足下面两个条件：
1. 对变量的写操作不依赖于当前值：不能用作线程安全计数器：虽然增量操作`x++`看上去类似一个单独操作，实际上它是一个由读取－修改－写入操作序列组成的组合操作，必须以原子方式执行，`volatile`不能提供必须的原子特性
2. 该变量没有包含在具有其他变量的不变式中

#### CAS
CAS是英文单词Compare And Swap的缩写，翻译过来就是比较并替换。

CAS机制当中使用了3个基本操作数：内存地址V，旧的预期值A，要修改的新值B。更新一个变量的时候，只有当变量的预期值A和内存地址V当中的实际值相同时，才会将内存地址V对应的值修改为B，具体过程：
1. 在内存地址V当中，存储着值为10的变量
2. 此时线程1想要把变量的值增加1。对线程1来说，旧的预期值A=10，要修改的新值B=11
3. 在线程1要提交更新之前，另一个线程2抢先一步，把内存地址V中的变量值率先更新成了11
4. 线程1开始提交更新，首先进行A和地址V的实际值比较Compare，发现A不等于V的实际值，提交失败
5. 线程1重新获取内存地址V的当前值，并重新计算想要修改的新值。此时对线程1来说，A=11，B=12。这个重新尝试的过程被称为自旋
6. 这一次比较幸运，没有其他线程改变地址V的值。线程1进行Compare，发现A和地址V的实际值是相等的
7. 线程1进行SWAP，把地址V的值替换为B，也就是12
从思想上来说，Synchronized属于悲观锁，悲观地认为程序中的并发情况严重，所以严防死守。CAS属于乐观锁，乐观地认为程序中的并发情况不那么严重，所以让线程不断去尝试更新。

CAS的缺点：
1. CPU开销较大。在并发量比较高的情况下，如果许多线程反复尝试更新某一个变量，却又一直更新不成功，循环往复，会给CPU带来很大的压力。
2. 不能保证代码块的原子性。CAS机制所保证的只是一个变量的原子性操作，而不能保证整个代码块的原子性。比如需要保证3个变量共同进行原子性的更新，就不得不使用Synchronized了

#### CAS会产生什么问题
CAS机制当中使用了3个基本操作数：内存地址V，旧的预期值A，要修改的新值B。
1. CPU开销较大：在并发量比较高的情况下，如果许多线程反复尝试更新某一个变量，却又一直更新不成功，循环往复，会给CPU带来很大的压力
2. 不能保证代码块的原子性：CAS机制所保证的只是一个变量的原子性操作，而不能保证整个代码块的原子性。比如需要保证3个变量共同进行原子性的更新，就不得不使用Synchronized了
3. ABA问题：CAS机制最大的问题所在。线程1准备用CAS将变量的值由A替换为B，在此之前，线程2将变量的值由A替换为C，又由C替换为A，然后线程1执行CAS时发现变量的值仍然为A，所以CAS成功。但实际上这时的现场已经和最初不同了，尽管CAS成功，但可能存在潜藏的问题，例如：
    1. 一个用单向链表实现的堆栈，栈顶为A，这时线程T1已经知`A.next`为B，希望用CAS将栈顶替换为B
        
        ```
        graph LR
        A-->B
        ```
        
    2. 在T1执行上面这条指令之前，线程T2介入，将A、B出栈，再`push D C A`，此时堆栈结构如下图，而对象B此时处于游离状态
        
        ```
        graph LR
        A-->C
        C-->D
        ```
        
    3. 此时轮到线程T1执行CAS操作，检测发现栈顶仍为A，所以CAS成功，栈顶变为B，但实际上`B.next`为null，所以此时的情况变为
        
       ```
        graph LR
        B
        C-->D
        ```


### 1.8 IO
#### BIO与NIO、AIO的区别
1. Java中的典型IO操作模式
    1. 同步阻塞模式：Java中的BIO风格的API
        
        ```java
        Socket socket = getSocket();
        socket.getInputStream().read(); //读不到数据誓不返回
        ```
        
        该模式下，最直观的感受就是如果IO设备暂时没有数据可供读取，调用API就卡住了，如果数据一直不来就一直卡住
    2. 同步非阻塞模式：Java中的NIO风格的API
        
        ```java
        SocketChannel socketChannel = getSocketChannel(); //获取non-blocking状态的Channel
        socketChannel.read(ByteBuffer.allocate(4)); //读不到数据就算了，立即返回0告诉你没有读到
        ```
        
        该模式下，通常需要不断调用API，直至读取到数据，不过好在函数调用不会卡住，想继续尝试读取或者先去做点其他事情再来读取都可以
    3. 异步非阻塞模式：Java中的AIO风格的API
        
        ```java
        AsynchronousSocketChannel asynchronousSocketChannel = getAsynchronousSocketChannel();
        asynchronousSocketChannel.read(ByteBuffer.allocate(4), null, new CompletionHandler<Integer, Object>() {
            @Override
            public void completed(Integer result, Object attachment) {
                //读不到数据不会触发该回调来烦你，只有确实读取到数据，且把数据已经存在ByteBuffer中了，API才会通过此回调接口主动通知您
            }
            @Override
            public void failed(Throwable exc, Object attachment) {
            }
        });
        
        ```
        读不到数据不会触发该回调来烦你，只有确实读取到数据，且把数据已经存在`ByteBuffer`中了，API才会通过此回调接口主动通知您
2. Java对BIO、NIO、AIO的支持
    1. BIO
        1. 同步并阻塞，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销，当然可以通过线程池机制改善
        2. 适用于连接数目比较小且固定的架构，这种方式对服务器资源要求比较高，并发局限于应用中，JDK1.4以前的唯一选择，但程序直观简单易理解
    2. NIO
        1. 同步非阻塞，服务器实现模式为一个请求一个线程，即客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有I/O请求时才启动一个线程进行处理
        2. 适用于连接数目多且连接比较短（轻操作）的架构，比如聊天服务器，并发局限于应用中，编程比较复杂，JDK1.4开始支持
    3. AIO
        1. 异步非阻塞，服务器实现模式为一个有效请求一个线程，客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理
        2. 适用于连接数目多且连接比较长（重操作）的架构，比如相册服务器，充分调用OS参与并发操作，编程比较复杂，JDK7开始支持

    另外，I/O属于底层操作，需要操作系统支持，并发也需要操作系统的支持，所以性能方面不同操作系统差异会比较明显。

> 1. 同步：自己亲自出马持银行卡到银行取钱（使用同步IO时，Java自己处理IO读写）；
> 2. 异步：委托一小弟拿银行卡到银行取钱，然后给你（使用异步IO时，Java将IO读写委托给OS处理，需要将数据缓冲区地址和大小传给OS(银行卡和密码)，OS需要支持异步IO操作API）；
> 3. 阻塞：ATM排队取款，你只能等待（使用阻塞IO时，Java调用会一直阻塞到读写完成才返回）；
> 4. 非阻塞：柜台取款，取个号，然后坐在椅子上做其它事，等号广播会通知你办理，没到号你就不能去，你可以不断问大堂经理排到了没有，大堂经理如果说还没到你就不能去（使用非阻塞IO时，如果不能读写Java调用会马上返回，当IO事件分发器会通知可读写时再继续进行读写，不断循环直到读写完成）

> 一般来说I/O模型可以分为：同步阻塞，同步非阻塞，异步阻塞，异步非阻塞IO
> 1. 同步阻塞IO：在此种方式下，用户进程在发起一个IO操作以后，必须等待IO操作的完成，只有当真正完成了IO操作以后，用户进程才能运行。JAVA传统的IO模型属于此种方式！
> 2. 同步非阻塞IO:在此种方式下，用户进程发起一个IO操作以后边可返回做其它事情，但是用户进程需要时不时的询问IO操作是否就绪，这就要求用户进程不停的去询问，从而引入不必要的CPU资源浪费。其中目前JAVA的NIO就属于同步非阻塞IO。
> 3. 异步阻塞IO：此种方式下是指应用发起一个IO操作以后，不等待内核IO操作的完成，等内核完成IO操作以后会通知应用程序，这其实就是同步和异步最关键的区别，同步必须等待或者主动的去询问IO是否完成，那么为什么说是阻塞的呢？因为此时是通过select系统调用来完成的，而select函数本身的实现方式是阻塞的，而采用select函数有个好处就是它可以同时监听多个文件句柄，从而提高系统的并发性！
> 4. 异步非阻塞IO:在此种模式下，用户进程只需要发起一个IO操作然后立即返回，等IO操作真正的完成以后，应用程序会得到IO操作完成的通知，此时用户进程只需要对数据进行处理就好了，不需要进行实际的IO读写操作，因为真正的IO读取或者写入操作已经由内核完成了。目前Java中还没有支持此种IO模型

---
## 2 JVM
### 2.1 内存管理
#### JVM内存模型，垃圾回收回收的是哪部分
*《深入理解Java虚拟机》P39 2.2*

区域 | 是否隔离 | 异常
---|---|---
程序计数栈 | 私有 | 唯一不会抛出OutOfMemoryError异常
Java虚拟机栈 | 私有 | OutOfMemoryError（无法申请到足够内存）；StackOverFlowError（请求栈深度大于虚拟机允许深度）
本地方法栈 | 私有 | OutOfMemoryError（无法申请到足够内存）；StackOverFlowError（请求栈深度大于虚拟机允许深度）
Java堆 | 共享 | OutOfMemoryError（堆中没有完成内存分配且堆无法再扩展）
方法区 | 共享 | OutOfMemoryError（无法满足内存分配需求）

1. 程序计数栈：一块较小的内存空间，是当前线程所执行的字节码的行号指示器。程序计数器只为执行java方法服务，执行Native方法时程序计数器为空
2. Java虚拟机栈：生命周期与线程相同，每个方法在执行的同时都会创建一个栈帧（方法运行时的基础数据结构）用于存储局部变量表、操作数栈、动态链接、方法出口等信息。每一个方法从调用直至执行完成的过程，就对应着一个栈帧在虚拟机栈中入栈到出栈的过程。
3. 本地方法栈：与虚拟机栈所发挥的作用是非常相似的，虚拟机栈为虚拟机执行Java方法（也就是字节码）服务，而本地方法栈则为虚拟机使用到的Native方法服务
4. Java堆：在虚拟机启动时创建。此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例都在这里分配内存。Java堆是垃圾收集器管理的主要区域，因此很多时候也被称做“GC堆”
5. 方法区：Non-Heap（非堆），各个线程共享的内存区域，它用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据

#### Java堆内存分代管理
1. 新生代：
    1. Eden空间：对象优先在Eden分配，空间不足时，虚拟机将发起一次Minor GC
    2. From Survivor空间、To Survivor空间：在Minor GC时交替使用，达到一定次数后，对象会晋升到老年代。如果Minor GC后仍存活的对象无法放入Survivor，则通过分担带包机制提前将对象转移到老年代
2. 老年代：大对象（需要大量连续内存空间）直接进入老年代；长期存活的对象进入老年代，对象每“熬过”一次Minor GC年龄增加一岁，到一定程度（默认为15岁）则晋升到老年代
3. 永久代：存储类定义、结构、字段、方法（数据及代码）以及常量在内的类相关数据
    
    > JDK 1.8中永久代被元空间（Metaspace）取代。两者本质类似，都是对JVM规范中方法区的实现，最大区别是：永久代的大小很难确定，对永久代的调优过程非常困难；元空间并不在虚拟机中，而是使用本地内存，最大可分配空间就是系统可用内存空间

#### 为什么需要两个Survivor区
设置两个Survivor区最大的好处就是解决了碎片化。

1. 如果只有一块Survivor区。Eden满了，第一次触发Minor GC，Eden中的存活对象被移动到Survivor区；再次触发Minor GC，Eden和Survivor各有一些存活对象：
    1. 若此时把Eden区的存活对象直接放到Survivor区，这两部分对象所占有的内存是不连续的，导致了内存碎片化，严重影响Java程序的性能
    2. 若整理Survivor区后再放入Eden区的存活对象，则效率较低

2. 如果有两块Survivor区。第一次触发Minor GC，Eden中的存活对象就会被移动到第一块Survivor区S0，Eden被清空；再次触发Minor GC时，Eden和S0中的存活对象又会被复制送入第二块survivor区S1，S0和Eden被清空；然后下一轮S0与S1交换角色，如此循环往复。整个过程中，永远有一个Survivor区是空的，另一个非空的Survivor区无碎片

#### Eden区和Survivor区的分配的大小比例，为什么
IBM公司的专门研究表明，新生代中的对象98%是“朝生夕死”的，HotSpot虚拟机默认Eden和Survivor的大小比例是8:1，也就是每次新生代中可用内存空间为整个新生代容量的90%（80%+10%），只有10%的内存会被“浪费”。当然，98%的对象可回收只是一般场景下的数据，没有办法保证每次回收都只有不多于10%的对象存活，当Survivor空间不够用时，需要依赖其他内存（这里指老年代）进行分配担保

#### 堆的永久代和JVM方法栈有什么区别
两者并不等价，仅仅是HotSpot虚拟机的设计团队选择把GC分代收集扩展至方法区，或者说使用永久代实现方法区，省去为方法区编写内存管理代码的工作

方法区是JVM规范中的一部分，如何实现方法区属于虚拟机实现细节，不受虚拟机规范约束，但使用永久代来实现方法去容易遇到内存溢出问题

#### 如何判断对象是否存活
1. 引用计数法：给对象中添加一个引用计数器，每当有一个地方引用它时，计数器就加1，当引用失效时，计数器就减1；任何时刻计数器为0的对象就是不可能在被使用的
    
    主流的Java虚拟机里面没有选择引用计数算法来管理内存，其中主要的原因是它很难解决对象之间的相互循环引用的问题
2. 可达性分析法：在主流的商用程序语言中使用。基本思路就是通过一系列的名为GC Roots的对象作为起始点，从这些节点开始向下搜索，搜索所走过的路径称为引用链，当一个对象到GC Roots没有任何引用链相连（对象不可达）时，则证明此对象是不可用的
    
    ```
    graph TB
    A(CG Roots)
    B(Object1)
    C(Object2)
    D(Object3)
    E(Object4)
    F(Object5)
    G(Object6)
    H(Object7)
    A --> B
    B --> C
    B --> D
    D --> E
    F --> G
    F --> H
    ```
    
    在Java语言里，可作为GC Roots的对象包括下面几种：
    1. 虚拟机栈（栈帧中的本地变量表）中的引用的对象
    2. 方法区中的类静态属性引用的对象
    3. 方法区中的常量引用的对象
    4. 本地方法栈中JNI（即一般说的Native方法）的引用的对象

#### 垃圾回收算法
*《深入理解Java虚拟机》P69 3.3*
1. 标记-清除算法：回收老年代。最基础的收集算法，分为“标记”和“清除”两个阶段：首先标记出所有需要回收的对象，标记完成后统一回收所有被标记的对象

    后续的收集算法都是基于这种思路并对其不足进行改进而得到的。它的主要不足有两个：
    1. 效率问题，标记和清除两个过程的效率都不高；
    2. 空间问题，标记清除之后会产生大量不连续的内存碎片，空间碎片太多可能会导致以后在程序运行过程中需要分配较大对象时，无法找到足够的连续内存而不得不提前触发另一次垃圾收集动作
    ![image](https://note.youdao.com/yws/public/resource/be02344987843483827564c4dc494b13/xmlnote/1E6DF344CF02413F81DE0C00318B9DD5/7120)
2. 复制算法：回收新生代。为了解决效率问题，它将可用内存按容量划分为大小相等的两块，每次只使用其中的一块。当这一块的内存用完了，就将还存活着的对象复制到另外一块上面，然后再把已使用过的内存空间一次清理掉。这样使得每次都是对整个半区进行内存回收，内存分配时也就不用考虑内存碎片等复杂情况，只要移动堆顶指针，按顺序分配内存即可，实现简单，运行高效。只是这种算法的代价太高，将内存缩小为了原来的一半
    > 将内存分为一块较大的Eden空间和两块较小的Survivor空间，每次使用Eden和其中一块Survivor。当回收时，将Eden和Survivor中还存活着的对象一次性地复制到另外一块Survivor空间上，最后清理掉Eden和刚才用过的Survivor空间

    ![image](https://note.youdao.com/yws/public/resource/be02344987843483827564c4dc494b13/xmlnote/6FF30D42ABA34313A1D1C1C2D1A370F0/7124)
3. 标记-整理算法：回收老年代。标记过程仍然与“标记-清除”算法一样，标记完成后让所有存活的对象都向一端移动，然后直接清理掉端边界以外的内存
    > 复制收集算法在对象存活率较高时就要进行较多的复制操作，效率将会变低。更关键的是，如果不想浪费50%的空间，就需要有额外的空间进行分配担保，以应对被使用的内存中所有对象都100%存活的极端情况，所以在老年代一般不能直接选用这种算法

    ![image](https://note.youdao.com/yws/public/resource/be02344987843483827564c4dc494b13/xmlnote/A8E7733C7BCF4802AFDAD88E3108DF23/7132)
4. 分代收集算法：当前商业虚拟机的垃圾收集都采用“分代收集”算法，根据对象存活周期的不同将内存划分为几块。一般是把Java堆分为新生代和老年代，这样就可以根据各个年代的特点采用最适当的收集算法
    1. 在新生代中，每次垃圾收集时都发现有大批对象死去，只有少量存活，那就选用复制算法，只需要付出少量存活对象的复制成本就可以完成收集
    2. 老年代中因为对象存活率高、没有额外空间对它进行分配担保，就必须使用“标记—清理”或者“标记—整理”算法来进行回收

#### 垃圾收集器
[深入理解JVM(3)——7种垃圾收集器](https://crowhawk.github.io/2017/08/15/jvm_3/)

![image](https://pic.yupoo.com/crowhawk/56a02e55/3b3c42d2.jpg)

如果两个收集器之间存在连线，就说明它们可以搭配使用。虚拟机所处的区域，则表示它是属于新生代收集器还是老年代收集器。Hotspot实现了如此多的收集器，正是因为目前并无完美的收集器出现，只是选择对具体应用最适合的收集器

收集器 | 执行方式 | 适用分代 | 算法 | 目标 | 适用场景
---|---|---|---|---|--- 
Serial | 串行 | 新生代 | 复制算法 | 响应速度优先 | 单CPU环境下的Client模式
Serial Old | 串行 | 老年代 | 标记-整理 | 响应速度优先 | 单CPU环境下的Client模式、CMS的后备预案
ParNew | 并行 | 新生代 | 复制算法 | 响应速度优先 | 多CPU环境时在Server模式下与CMS配合
Parallel Scavenge | 并行 | 新生代 | 复制算法 | 吞吐量优先 | 在后台运算而不需要太多交互的任务
Parallel Old | 并行 | 老年代 | 标记-整理 | 吞吐量优先 | 在后台运算而不需要太多交互的任务
CMS | 并发 | 老年代 | 标记-清除 | 响应速度优先 | 集中在互联网站或B/S系统服务端上的Java应用
G1 | 并发 | both | 标记-整理+复制算法 | 响应速度优先 | 面向服务端应用，将来替换CMS

#### ==垃圾回收是否有内存泄漏的风险==
[ThreadLocal原理及内存泄露预防](https://blog.csdn.net/puppylpg/article/details/80433271)

#### 强引用、弱引用、软引用、虚引用
[理解Java的强引用、软引用、弱引用和虚引用](https://juejin.im/post/5b82c02df265da436152f5ad#heading-4)

引用类型 | GC回收 | 用途 | 生存时间
---|---|---|---|---
强引用 | 从来不会 | 对象的一般状态 | JVM停止运行时终止
软引用 | 当内存不足时 | 对象缓存 | 内存不足时终止
弱引用 | 正常垃圾回收时 | 对象缓存 | 垃圾回收后终止
虚引用 | 正常垃圾回收时 | 跟踪对象的垃圾回收 | 垃圾回收后终止

1. 强引用：StrongReference，使用最普遍的引用。如果一个对象具有强引用，那垃圾回收器绝不会回收它
    
    ```java
    Object strongReference = new Object();
    ```
    
2. 软引用：SoftReference，如果一个对象只具有软引用，则内存空间充足时，垃圾回收器就不会回收它；如果内存空间不足了，就会回收这些对象的内存。只要垃圾回收器没有回收它，该对象就可以被程序使用。软引用可用来实现内存敏感的高速缓存
    
    ```java
    // 强引用
    String strongReference = new String("abc");
    // 软引用
    String str = new String("abc");
    SoftReference<String> softReference = new SoftReference<String>(str);
    ```
    
3. 弱引用：WeakReference，弱引用与软引用的区别在于：只具有弱引用的对象拥有更短暂的生命周期。在垃圾回收器线程扫描它所管辖的内存区域的过程中，一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。不过，由于垃圾回收器是一个优先级很低的线程，因此不一定会很快发现那些只具有弱引用的对象
    
    ```java
    String str = new String("abc");
    WeakReference<String> weakReference = new WeakReference<>(str);
    str = null;
    ```
    
4. 虚引用：PhantomReference，虚引用顾名思义，就是形同虚设。与其他几种引用都不同，虚引用并不会决定对象的生命周期。如果一个对象仅持有虚引用，那么它就和没有任何引用一样，在任何时候都可能被垃圾回收器回收。虚引用主要用来跟踪对象被垃圾回收器回收的活动

    虚引用与软引用和弱引用的一个区别在于：虚引用必须和引用队列(ReferenceQueue)联合使用。当垃圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在回收对象的内存之前，把这个虚引用加入到与之关联的引用队列中。

    ```java
    String str = new String("abc");
    ReferenceQueue queue = new ReferenceQueue();
    // 创建虚引用，要求必须与一个引用队列关联
    PhantomReference pr = new PhantomReference(str, queue);
    ```

### 2.2 类加载机制
#### 解释ClassLoader，类加载器有几种，类加载器加载顺序
*《深入理解Java虚拟机》P227 7.4*
1. 类加载器用于实现类的加载动作，它在Java程序中起到的作用却远远不限于类加载阶段。对于任意一个类，都需要由加载它的类加载器和这个类本身一同确立其在Java虚拟机中的唯一性，每一个类加载器，都拥有一个独立的类名称空间。也就是说，比较两个类是否“相等”，只有在这两个类是由同一个类加载器加载的前提下才有意义，否则，即使这两个类来源于同一个Class文件，被同一个虚拟机加载，只要加载它们的类加载器不同，那这两个类就必定不相等。
2. Java自带的三种类加载器：
    加载器 | 名称 | 加载目录
    ---|---|---
    ClassLoader | BootStrap启动类加载器 | <JAVA_HOME>/lib
    ExtClassLoader | 扩展类加载器 | <JAVA_HOME>/lib/ext
    AppClassLoader | 应用程序类加载器 | java -classpath
3. 双亲委派模式：如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把这个请求委派给父类加载器去完成，每一个层次的类加载器都是如此，因此所有的加载请求最终都应该传送到顶层的启动类加载器中，只有当父加载器反馈自己无法完成这个加载请求（它的搜索范围中没有找到所需的类）时，子加载器才会尝试自己去加载

    ![image](https://note.youdao.com/yws/public/resource/be02344987843483827564c4dc494b13/xmlnote/7BD96F10EE194313B492B8C7DA8224F4/7363)
    
    双亲委派模式的好处：
    1. 避免类的重复加载
    2. 避免java核心api被串改（如果用户自己编写了一个称为java.lang.Object的类，并放在程序的ClassPath中，可以正常编译，但永远无法被加载运行）

#### 哪个框架破坏了双亲委派模式
*《深入理解Java虚拟机》P233*

典型的打破双亲委派模型的框架和中间件有tomcat与osgi
1. 第一次“被破坏”：发生在双亲委派模型出现之前，即JDK 1.2发布之前。由于双亲委派模型在JDK 1.2之后才被引入，而类加载器和抽象类java.lang.ClassLoader则在JDK 1.0时代就已经存在，面对已经存在的用户自定义类加载器的实现代码，Java设计者引入双亲委派模型时不得不做出一些妥协
2. 第二次“被破坏”：由这个模型自身的缺陷所导致的，双亲委派很好地解决了各个类加载器的基础类的统一问题（越基础的类由越上层的加载器进行加载），基础类之所以称为“基础”，是因为它们总是作为被用户代码调用的API，但世事往往没有绝对的完美，可能存在基础类又要调用回用户的代码。为了解决这个问题，引入了一个不太优雅的设计：线程上下文类加载器
3. 第三次“被破坏”：由于用户对程序动态性的追求而导致的。在OSGi环境下，类加载器不再是双亲委派模型中的树状结构，而是进一步发展为更加复杂的网状结构，的类查找可能是在平级的类加载器中进行

#### 类的生命周期（类加载过程）
*《深入理解Java虚拟机》P214 7.3*

类的加载：将编译好的class类文件中的字节码读入到内存中，将其放在方法区内创建对应的class对象，类的加载分为：加载、验证、准备、解析、初始化。加载、验证、准备、初始化和卸载这5个阶段的顺序是确定的。

![image](https://note.youdao.com/yws/res/3502/WEBRESOURCEe86b40f70a237a69ae1a731224d1be27)

1. 加载：
    1. 通过一个类的全限定名来获取定义此类的二进制字节流
    2. 将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构
    3. 在内存中生成一个代表这个类的java.lang.Class对象，作为方法区这个类的各种数据的访问入口
    > 注意这里不一定非得要从一个Class文件获取，这里既可以从ZIP包中读取（比如从j包和war包中读取），也可以在运行时计算生成（动态代理），也可以由其它文件生成（比如将JSP文件转换成对应的Class类）
2. 验证：重要但不一定必要（对程序运行期没有影响），若所运行的全部代码都已经被反复使用和验证过，那么在实施阶段可关闭类验证，缩短虚拟机类加载的时间。完成四个阶段的检验动作：
    1. 文件格式（通过文件格式验证后，字节流进入内存的方法区中进行存储）
    2. 元数据（检查类）
    3. 字节码（检查方法体）
    4. 符号引用
3. 准备：在方法区中为类变量（被static修饰的变量，不包含final修饰的变量，final修饰的变量在编译时就已分配赋值）分配内存并设置类变量初始值（零值），而非代码中的具体值（代码中值的赋值将在初始化阶段执行）
4. 解析：虚拟机将常量池内的符号引用替换为直接引用的过程。解析动作主要针对类或接口、字段、类方法、接口方法、方法类型、方法句柄和调用点限定符7类符号引用进行。虚拟机规范之中并未规定解析阶段发生的具体时间，执行前解析即可
5. 初始化：真正开始执行类中定义的Java程序代码（或者说是字节码），根据程序员通过程序制定的主观计划去初始化类变量和其他资源。有且仅有以下五种情况，如果类没有初始化则会先触发其初始化：
    1. 遇到new、getstatic、setstatic或者invokestatic这4个字节码指令时：使用new关键字实例化对象；读取或设置一个类的静态字段（final修饰的字段除外）；调用一个类的静态方法
    2. 使用java.lang.reflect包的方法对类进行反射调用的时
    3. 当初始化一个类的时候，如果发现其父类还没有进行过初始化，则需要先触发其父类的初始化
    4. 当虚拟机启动时，用户需要指定一个要执行的主类（包含main（）方法的那个类），虚拟机会先初始化这个主类
    5. 当使用JDK 1.7的动态语言支持时，如果一个java.lang.invoke.MethodHandle实例最后的解析结果REF_getStatic、REF_putStatic、REF_invokeStatic的方法句柄，并且这个方法句柄所对应的类没有进行过初始化，则需要先触发其初始化

#### 编译生成的class存放在哪
类的加载的过程中，加载并通过通过文件格式验证后（验证的第一步），会将编译好的class类文件中的字节码读入到内存中，并将其放在方法区内

#### `A a = new A()`的执行过程，内存分配的方式
虚拟机遇到一条`new`指令时，首先将去检查这个指令的参数是否能在常量池中定位到一个类的符号引用。并且检查代表这个符号引用的类是否已经被加载、解析和初始化过。如果没有，那么必须先执行相应的类加载。

在类加载检查通过后，接下来虚拟机将会为新生的对象分配内存。对象所需的内存大小在类加载完成后就可以确定，为对象分配空间的任务等同于把一块大小确定的内存从Java堆中划分出来。内存分配有两种方式：

1. 指针碰撞：假设Java堆中内存是绝对规整的，所有用过的内存都放在一边，空闲的内存放在另一边，中间放着一个指针作为分界点的指示器。分配内存时就把指针向空闲空间那边挪动一段与对象大小相等的距离。一些新生代GC收集器使用的是复制算法，所以采用指针碰撞方式分配内存
2. 空闲列表：如果Java堆中的内存并不是规整的，已使用的内存和空闲的内存相互交错，那就没有办法简单地进行指针碰撞了，虚拟机就必须维护一个列表，记录上哪些内存块是可用的，在分配的时候从列表中找到一块足够大的空间划分给对象实例， 并更新列表上的记录。老年代垃圾收集器使用了标记清理、整理算法，所以配合空闲列表方式分配内存

---
## 3 设计模式
### 
#### 什么是动态代理和静态代理
1. 静态代理是指使用AOP框架提供的命令进行编译，从而在编译阶段就可生成AOP代理类，因此也称为编译时增强（AspectJ）
2. 动态代理则在运行时借助于JDK动态代理、CGLIB等在内存中“临时”生成AOP动态代理类，因此也被称为运行时增强（Spring AOP）

#### 静态代理和动态代理的优缺点
1. 静态代理
    1. 优点：静态代理对客户端隐藏了被代理类接口（目标类接口）的具体实现类，在一定程度上实现了解耦合，同时提高了安全性
    2. 缺点：
        1. 静态代理类需要实现目标类（被代理类）的接口，并实现其方法，造成了代码的大量冗余
        2. 静态代理只能对某个固定接口的实现类进行代理服务，其灵活性不强。故一般大项目不会选择静态代理
2. 动态代理
    1. 优点：
        1. 动态代理实现了只需要将被代理对象作为参数传入代理类就可以获取代理类对象，从而实现类代理，具有较强的灵活性
        2. 动态代理的服务内容不需要像静态代理一样写在每个代码块中，只需要写在invoke()方法中即可，降低了代码的冗余度
    2. 缺点：JDK动态代理基于接口，需要被代理客户端实现接口；CGLib动态代理基于继承，final类无法代理

#### 动态代理的实现
*《Spring 3.x》P181 6.2.2 & 《Spring 3.x》P184 6.2.3*
1. JDK动态代理：基于接口的代理。JDK动态代理通过反射来接收被代理的类，并且要求被代理的类必须实现一个接口。核心是InvocationHandler接口和Proxy类，具体实现原理：
    1. 实现`InvocationHandler`接口创建自己的编织器。重写`invoke()`方法，反射机制调用业务类的目标方法
    2. 实例化一个编织器`handler`。通过构造方法传入希望被代理的目标对象
    3. 实例化`Proxy`类。通过静态方法`newProxyInstance()`为编织了业务逻辑和增强逻辑的`handler`创建一个符合业务类接口的代理实例
2. CGLIB动态代理：基于继承的代理。如果目标类没有实现接口，那么Spring AOP会选择使用CGLIB来动态代理目标类。CGLIB（Code Generation Library），是一个代码生成的类库。CGLIB是通过继承的方式做的动态代理，因此如果某个类被标记为final，那么它是无法使用CGLIB做动态代理的。采用非常底层的字节码技术：
    1. 实现`MethodInterceptor`接口创建代理创建器`CglibProxy`
        1. 实现`getProxy()`方法。设置需要创建子类的类（业务类），通过字节码技术动态创建子类实例
        2. 实现`intercapt()`方法。拦截父类所有方法的调用，通过代理类调用父类中的方法
    2. 实例化一个代理创建器`proxy`。通过动态生成子类的方法（`getProxy()`）创建代理类

    关于两者之间的性能：
    1. CGLib所创建的动态代理对象在实际运行时候的性能要比JDK动态代理高不少，有研究表明，大概要高10倍
    2. 但是CGLib在创建对象的时候所花费的时间却比JDK动态代理要多很多，有研究表明，大概有8倍的差距
    3. 因此，对于singleton的代理对象或者具有实例池的代理，因为无需频繁的创建代理对象，所以比较适合采用CGLib动态代理，反之，则比较适用JDK动态代理

---
## 4 Spring
### 
#### 为什么要用Spring框架开发
*《Spring 3.x》P5 1.3*
1. 方便解耦，简化开发：通过IoC容器，可以将对象之间的依赖关系交由Spring进行控制，避免硬编码所造成的过度程序耦合。不必再为单实例模式类、属性文件解析等这些很底层的需求编写代码，可以更专注于上层的应用
2. AOP编程的支持：通过Spring提供的AOP功能，方便进行面向切面的编程，许多不易用传统OOP实现的功能可以通过AOP实现
3. 声明事物的支持：通过声明式方式灵活地进行事务的管理，提高开发效率和质量
4. 方便程序的测试：可以用非容器依赖的编程方式进行几乎所有的测试工作，让测试更方便。例如：可以用Junit4通过注解方便的测试
5. 方便集成各种优秀框架：降低各种框架的使用难度，Spring提供了对各种优秀框架的直接支持（如Struts,Hibernate、Hessian、Quartz）
6. 降低Java EE API的使用难度：对很多难用的Java EE API（如JDBC，JavaMail，远程调用）提供了封装层，降低其使用难度
7. 源码是经典学习范例：Spring的源码设计精妙、结构清晰，体现着对Java设计模式灵活运用以及对Java技术的高深造诣。Spring框架源码是Java技术的最佳实践范例

#### IoC AOP 理解
*《Spring 3.x》P41 3.1 & 《Spring 3.x》P174 6.1*

Spring最成功的是其提出的理念，而不是技术本身。它所依赖的两个核心理念，一个是控制反转（IoC），另一个是面向切面编程（AOP）
1. IoC：Spring容器的内核，AOP、声明式事务等功都以此为基础。它涉及代码解耦、设计模式、代码优化等问题的考量。
    
    某一接口具体实现类的选择控制权从调用类中移除，转交给第三方决定。也就是“依赖注入”，让调用类对某一接口实现类的依赖关系有第三方注入，以移除调用类对某一接口实现类的依赖
    1. IoC的类型（Spring支持构造函数注入和属性注入）：
        1. 构造函数注入：在构造函数注入中，我们通过调用类的构造函数，将接口实现类通过构造函数变量传入
        2. 属性注入：属性注入可以有选择地通过Setter方法完成调用类所需依赖的注入，更加灵活方便
        3. 接口注入： 将调用类所有依赖注入的方法抽取到一个接口中，调用类通过实现该接口提供相应的注入方法。由于通过接口注入需要额外声明一个接口，增加了类的数目，而且它的效果和属性注入并无本质区别，因此我们不提倡采用这种方式
    2. 通过容器完成依赖关系的注入：用一个第三方的容器（Spring）帮助完成类的初始化与装配工作，完成底层实现类的实例化、依赖关系装配等工作。Spring通过配置文件或注解描述类和类之间的依赖关系，自动完成类的初始化和依赖注入的工作
2. AOP：面向切面编程，作为OOP的有益补充，AOP的应用场合有限制，它一般只使用与那些具有横切逻辑的应用场合：如性能检测，访问控制，事物管理以及日志记录。AOP实现的关键就在于AOP框架自动创建的AOP代理，AOP代理则可分为静态代理和动态代理两大类：
    1. 静态代理是指使用AOP框架提供的命令进行编译，从而在编译阶段就可生成AOP代理类，因此也称为编译时增强（AspectJ）
    2. 动态代理则在运行时借助于JDK动态代理、CGLIB等在内存中“临时”生成AOP动态代理类，因此也被称为运行时增强（Spring AOP）

#### AOP的使用场景
[AOP的应用场景(异常处理、安全检查和缓存)](https://blog.csdn.net/lzufeng/article/details/89816281)

[Principle of Spring AOP Implementation](https://programmer.help/blogs/principle-of-spring-aop-implementation.html)
1. 异常处理
2. 安全检查
3. 缓存

#### Spring bean生命周期
https://www.cnblogs.com/V1haoge/p/6106456.html

![image](https://images2015.cnblogs.com/blog/592104/201611/592104-20161127142806690-1797767117.jpg)

1. BeanFactoryPostProcessor的postProcessorBeanFactory()方法：若某个IoC容器内添加了实现了BeanFactoryPostProcessor接口的实现类Bean，那么在该容器中实例化任何其他Bean之前可以回调该Bean中的postPrcessorBeanFactory()方法来对Bean的配置元数据进行更改，比如从XML配置文件中获取到的配置信息。
2. Bean的实例化：Bean的实例化是使用反射实现的。
3. Bean属性注入：Bean实例化完成后，利用反射技术实现属性及依赖Bean的注入。
4. BeanNameAware的setBeanName()方法：如果某个Bean实现了BeanNameAware接口，那么Spring将会将Bean实例的ID传递给setBeanName()方法，在Bean类中新增一个beanName字段，并实现setBeanName()方法。
5. BeanFactoryAware的setBeanFactory()方法：如果某个Bean实现了BeanFactoryAware接口，那么Spring将会将创建Bean的BeanFactory传递给setBeanFactory()方法，在Bean类中新增了一个beanFactory字段用来保存BeanFactory的值，并实现setBeanFactory()方法。
6. ApplicationContextAware的setApplicationContext()方法：如果某个Bean实现了ApplicationContextAware接口，那么Spring将会将该Bean所在的上下文环境ApplicationContext传递给setApplicationContext()方法，在Bean类中新增一个ApplicationContext字段用来保存ApplicationContext的值，并实现setApplicationContext()方法。
7. BeanPostProcessor预初始化方法：如果某个IoC容器中增加的实现BeanPostProcessor接口的实现类Bean，那么在该容器中实例化Bean之后，执行初始化之前会调用BeanPostProcessor中的postProcessBeforeInitialization()方法执行预初始化处理。
8. InitializingBean的afterPropertiesSet()方法：如果Bean实现了InitializingBean接口，那么Bean在实例化完成后将会执行接口中的afterPropertiesSet()方法来进行初始化。
9. 自定义的inti-method指定的方法：如果配置文件中使用init-method属性指定了初始化方法，那么Bean在实例化完成后将会调用该属性指定的初始化方法进行Bean的初始化。
10. BeanPostProcessor初始化后方法：如果某个IoC容器中增加的实现BeanPostProcessor接口的实现类Bean，那么在该容器中实例化Bean之后并且完成初始化调用后执行该接口中的postProcessorAfterInitialization()方法进行初始化后处理。
11. 使用Bean：此时有关Bean的所有准备工作均已完成，Bean可以被程序使用了，它们将会一直驻留在应用上下文中，直到该上下文环境被销毁。
12. DisposableBean的destory()方法：如果Bean实现了DisposableBean接口，Spring将会在Bean实例销毁之前调用该接口的destory()方法，来完成一些销毁之前的处理工作。
13. 自定义的destory-method指定的方法：如果在配置文件中使用destory-method指定了销毁方法，那么在Bean实例销毁之前会调用该指定的方法完成一些销毁之前的处理工作。

注意：
1. BeanFactoryPostProcessor接口与BeanPostProcessor接口的作用范围是整个上下文环境中，使用方法是单独新增一个类来实现这些接口，那么在处理其他Bean的某些时刻就会回调响应的接口中的方法。
2. BeanNameAware、BeanFactoryAware、ApplicationContextAware的作用范围的Bean范围，即仅仅对实现了该接口的指定Bean有效，所有其使用方法是在要使用该功能的Bean自己来实现该接口。
3. 第8点与第9点所述的两个初始化方法作用是一样的，我们完全可以使用其中的一种即可，一般情况我们使用第9点所述的方式，尽量少的去来Bean中实现某些接口，保持其独立性，低耦合性，尽量不要与Spring代码耦合在一起。第12和第13也是如此。

---
## 5 Redis
### 
#### Redis为什么快
1. 完全基于内存，绝大部分请求是纯粹的内存操作，非常快速。数据存在内存中，类似于HashMap，HashMap的优势就是查找和操作的时间复杂度都是O(1)
2. 数据结构简单，对数据操作也简单，Redis中的数据结构是专门进行设计的
3. 采用单线程，避免了不必要的上下文切换和竞争条件，也不存在多进程或者多线程导致的切换而消耗 CPU，不用去考虑各种锁的问题，不存在加锁释放锁操作，没有因为可能出现死锁而导致的性能消耗
4. 使用多路I/O复用模型，非阻塞IO
5. 使用底层模型不同，它们之间底层实现方式以及与客户端之间通信的应用协议不一样，Redis直接自己构建了VM机制，因为一般的系统调用系统函数的话，会浪费一定的时间去移动和请求

#### Redis是多线程还是单线程
单线程

#### Redis事务，是否能保证原子性
单个 Redis 命令的执行是原子性的，但 Redis 没有在事务上增加任何维持原子性的机制，所以 Redis 事务的执行并不是原子性的

事务可以理解为一个打包的批量执行脚本，但批量指令并非原子

---
## 6 MyBatis
### 
#### mybatis $#的区别，为什么#可以防止注入
https://www.cnblogs.com/wslook/p/9185448.html

动态SQL是mybatis的强大特性之一。在动态SQL解析阶段，`#{ }`和`${ }`会有不同的表现
1. 解析方式不同：
    1. `#{ }`解析为一个JDBC预编译语句（Prepare Statment）的参数占位符。传入的参数在SQL中显示为字符串，会对传入的数据加引号。例：
        ```sql
        select id,name,age from student where id =#{id}
        // 当传递的参数id为"1"时，解析为
        select id,name,age from student where id ='1'
        ```
    2. `${ }`在动态SQL解析阶段进行变量替换，然后再进行预编译。传入的参数在SQL中直接显示为传入的值。例：
        ```sql
        select id,name,age from student where id =${id}
        // 当我们传递的参数id为"1"时，解析为
        select id,name,age from student where id =1
        ```
2. 安全性：
    1. `#{ }`可以防止SQL注入的风险：MyBatis默认启用了预编译功能，在SQL执行前，先将SQL发送给数据库进行编译，此时`#{ }`解析为一个JDBC预编译语句的参数占位符；执行时，替换编译好的SQL中的占位符。因为SQL注入只能对编译过程起作用，所以这样的方式就很好地避免了SQL注入的问题
    2. `${ }`无法防止Sql注入在MyBatis中：`${ }`这样的参数直接参与SQL编译，从而不能避免注入攻击
3. 一般能用`#{ }`的就不用`${ }`；但有些情况下必须使用`${ }`：传入数据库对象（表名、字段名）

#### ==mybatis的resultmap如何映射，复杂的result类如何序列化==
《MyBatis》技术内幕 P236

---
## 7 数据库
### 7.1 事务
#### ==数据库事务如何实现==

#### 事务的特性
1. 原子性：Atomicity，一个事务中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样
2. 一致性：Consistency，在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作
3. 隔离性：Isolation，数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交、读提交、可重复读和串行化
4. 持续性/永久性：Durability，事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失

#### 事务隔离级别和可能遇到的问题
隔离级别\问题 | 脏读 | 不可重复读 | 幻读
---|---|---|---|--- 
未提交读 | 可能 | 可能 | 可能
已提交读 | 可能 |可能  | 可能
可重复读 | 不可能 | 不可能| 可能
可串行化 | 可能 |不可能 |不可能

1. 事务隔离级别
    1. 未提交读：Read Uncommitted，允许脏读，也就是可能读取到其他会话中未提交事务修改的数据
    2. 已提交读：Read Committed，只能读取到已经提交的数据。Oracle等多数数据库默认都是该级别
    3. 可重复读：Repeated Read，在同一个事务内的查询都是事务开始时刻一致的。InnoDB默认级别
    4. 可串行化：Serializable，完全串行化的读，每次读都需要获得表级共享锁，读写相互都会阻塞
2. 可能产生的问题
    1. 脏读：Dirty Read，脏读就是指当一个事务正在访问数据，并且对数据进行了修改，而这种修改还没有提交到数据库中，这时，另外一个事务也访问这个数据，然后使用了这个数据
    2. 不可重复读：NonRepeatable Read，是指在一个事务内，多次读同一数据。在这个事务还没有结束时，另外一个事务也访问该同一数据。那么，在第一个事务中的两次读数据之间，由于第二个事务的修改，那么第一个事务两次读到的的数据可能是不一样的。这样就发生了在一个事务内两次读到的数据是不一样的，因此称为是不可重复读
    3. 幻读：Phantom Read，是指当事务不是独立执行时发生的一种现象，例如第一个事务对一个表中的数据进行了修改，这种修改涉及到表中的全部数据行。同时，第二个事务也修改这个表中的数据，这种修改是向表中插入一行新数据。那么，以后就会发生操作第一个事务的用户发现表中还有没有修改的数据行，就好象发生了幻觉一样

### 7.2 索引
#### MySQL索引类别
[MySQL索引类型](https://www.cnblogs.com/luyucheng/p/6289714.html)

```sql
CREATE TABLE table_name[col_name data type]
[unique|fulltext][index|key][index_name](col_name[length])[asc|desc]
```
> 1. `unique` `fulltext`为可选参数，分别表示唯一索引、全文索引
> 2. `index` `key`为同义词，两者作用相同，用来指定创建索引
> 3. `col_name`为需要创建索引的字段列，该列必须从数据表中该定义的多个列中选择
> 4. `index_name`指定索引的名称，为可选参数，如果不指定，默认`col_name`为索引值
> 5. `length`为可选参数，表示索引的长度，只有字符串类型的字段才能指定索引长度
> 6. `asc` `desc`指定升序或降序的索引值存储

1. 普通索引：最基本的索引，它没有任何限制
2. 唯一索引：与普通索引类似，不同的就是：索引列的值必须唯一，但允许有空值。如果是组合索引，则列值的组合必须唯一
3. 主键索引：是一种特殊的唯一索引，一个表只能有一个主键，不允许有空值。一般是在建表的时候同时创建主键索引
4. 组合索引：指多个字段上创建的索引，只有在查询条件中使用了创建索引时的第一个字段，索引才会被使用。使用组合索引时遵循最左前缀集合
5. 全文索引：主要用来查找文本中的关键字，而不是直接与索引中的值相比较
    > `fulltext`索引跟其它索引不同，它更像是一个搜索引擎。`fulltext`索引配合`match against`操作使用，而不是一般的`where`语句加`like`。它可以在`create table`，`alter table`，`create index`使用，不过目前只有`char` `varchar` `text` 列上可以创建全文索引。在数据量较大时候，先将数据放入一个没有全局索引的表中，然后再用`CREATE index`创建`fulltext`索引，要比先为一张表建立`fulltext`然后再将数据写入的速度快很多

#### 索引的作用
*高性能MySQL（第3版） p221 5.2*

1. 索引大大减少了服务器需要扫描的数据量
2. 索引可以帮助服务器避免排序和临时表
3. 索引可以将随机I/O变为顺序I/O

#### 索引数据结构，为什么用B+树而不是而不是B树，两者有什么区别
https://blog.csdn.net/u013411246/article/details/81088914
1. B树
    1. B-tree树即B树，B即Balanced。一种多路搜索树（并不是二叉的）
        1. 定义任意非叶子结点最多只有M个儿子；且M>2；
        2. 根结点的儿子数为[2, M]；
        3. 除根结点以外的非叶子结点的儿子数为[M/2, M]；
        4. 每个结点存放至少M/2-1（取上整）和至多M-1个关键字；（至少2个关键字）
        5. 非叶子结点的关键字个数=指向儿子的指针个数-1；
        6. 非叶子结点的关键字：K[1], K[2], …, K[M-1]；且K[i] < K[i+1]；
        7. 非叶子结点的指针：P[1], P[2], …, P[M]；其中P[1]指向关键字小于K[1]的子树，P[M]指向关键字大于K[M-1]的子树，其它P[i]指向关键字属于(K[i-1], K[i])的子树；
        8. 所有叶子结点位于同一层；

        如：（M=3）
    
        ![image](https://p-blog.csdn.net/images/p_blog_csdn_net/manesking/4.JPG)
    
        B树的搜索，从根结点开始，对结点内的关键字（有序）序列进行二分查找，如果命中则结束，否则进入查询关键字所属范围的儿子结点；重复，直到所对应的儿子指针为空，或已经是叶子结点
        
    2. B-树的特性
        1. 关键字集合分布在整颗树中；
        2. 任何一个关键字出现且只出现在一个结点中；
        3. 搜索有可能在非叶子结点结束；
        4. 其搜索性能等价于在关键字全集内做一次二分查找；
        5. 自动层次控制；
        
        由于限制了除根结点以外的非叶子结点，至少含有M/2个儿子，确保了结点的至少利用率，其最底搜索性能为：
        ![image](https://p-blog.csdn.net/images/p_blog_csdn_net/manesking/0.JPG)
        
        其中，M为设定的非叶子结点最多子树个数，N为关键字总数；所以B-树的性能总是等价于二分查找（与M值无关），也就没有B树平衡的问题；由于M/2的限制，在插入结点时，如果结点已满，需要将结点分裂为两个各占M/2的结点；删除结点时，需将两个不足M/2的兄弟结点合并
2. B+树
    1. B+树是B-树的变体，也是一种多路搜索树，其定义基本与B-树同，除了：
        1. 非叶子结点的子树指针与关键字个数相同；
        2. 非叶子结点的子树指针P[i]，指向关键字值属于[K[i], K[i+1])的子树（B-树是开区间）；
        3. 为所有叶子结点增加一个链指针；
        4. 所有关键字都在叶子结点出现；
    
        如：（M=3）
        
        ![image](https://p-blog.csdn.net/images/p_blog_csdn_net/manesking/5.JPG)
    
        B+的搜索与B-树也基本相同，区别是B+树只有达到叶子结点才命中（B-树可以在非叶子结点命中），其性能也等价于在关键字全集做一次二分查找；
    2. B+的特性
        1. 所有关键字都出现在叶子结点的链表中（稠密索引），且链表中的关键字恰好是有序的；
        2. 不可能在非叶子结点命中；
        3. 非叶子结点相当于是叶子结点的索引（稀疏索引），叶子结点相当于是存储（关键字）数据的数据层；
        4. 更适合文件索引系统；

[**为什么MySQL选择B+树做索引**](https://www.jianshu.com/p/7ce804f97967)
1. B+树的磁盘读写代价更低：B+树的内部节点并没有指向关键字具体信息的指针，因此其内部节点相对B树更小，如果把所有同一内部节点的关键字存放在同一盘块中，那么盘块所能容纳的关键字数量也越多，一次性读入内存的需要查找的关键字也就越多，相对IO读写次数就降低了。
2. B+树的查询效率更加稳定：由于非终结点并不是最终指向文件内容的结点，而只是叶子结点中关键字的索引。所以任何关键字的查找必须走一条从根结点到叶子结点的路。所有关键字查询的路径长度相同，导致每一个数据的查询效率相当。
3. B+树更便于遍历：由于B+树的数据都存储在叶子结点中，分支结点均为索引，方便扫库，只需要扫一遍叶子结点即可，但是B树因为其分支结点同样存储着数据，我们要找到具体的数据，需要进行一次中序遍历按序来扫，所以B+树更加适合在区间查询的情况，所以通常B+树用于数据库索引。
4. B+树更适合基于范围的查询：B树在提高了IO性能的同时并没有解决元素遍历的效率低下的问题，正是为了解决这个问题，B+树应用而生。B+树只需要去遍历叶子节点就可以实现整棵树的遍历。而且在数据库中基于范围的查询是非常频繁的，而B树不支持这样的操作或者说效率太低。

#### 组合索引的注意事项，查询`c>5 a=1`是否命中索引`ac`和`ca`
1. 使用组合索引时遵循最左前缀集合
2. 查询是否命中索引，和索引顺序有关，和查询顺序无关
3. 前位查询精确匹配（`=` `in` `between`）索引，后位查询才能命中索引
4. 对于字符串，采用左匹配索引，`xxx%`可命中索引，`%xxx`无法命中索引

因此，查询`c>5 a=1`时，索引`ac`会全部命中，索引`ca`只会部分命中`c`

```java
// 表t(a,b,c) 三个字段组成组合索引
select * from t where a=? and b=? and c=?  //全命中
select * from t where c=? and b=? and a=?  //全命中（MySQL的查询优化器会自动调整where子句的条件顺序以使用适合的索引）
select * from t where a=?  //命中a（最左前缀匹配）
select * from t where a=? and b=?  //命中a和b（最左前缀匹配）
select * from t where a=? or b=?  //一个没命中（or无法命中）
select * from t where a=? and c=?  //命中a（最左前缀匹配，中间没有则无法使用索引）
select * from t where a=? and b in ( x, y, z) and c=?  //全部命中（in精确匹配可以使用索引）
select * from t where b=?  //一个没命中（最左前缀匹配原则）
select * from t where b=? and c=?  //一个没命中（最左前缀匹配原则）
select * from t where a=? and b like 'xxx%'  //命中a和b
select * from t where a=? and b like '%xxx'  //命中a（`%xxx`无法命中索引）
select * from t where a<? and b=?  //命中a（a为范围查找）
select * from t where a between ? and ? and b=?  //命中a和b（BETWEEN相当于in操作是精确匹配）
select * from t where a between ? and ?  and b=? and c  and between ? and ?  //全命中
select * from where a-1=?  //函数和表达式无法命中索引
```

#### 索引命中后的查找对应行的过程
[MySQL优化：如何避免回表查询？什么是索引覆盖？](https://www.cnblogs.com/myseries/p/11265849.html)

> InnoDB有两大类索引，聚集索引（clustered index）、普通索引（secondary index）。InnoDB普通索引的叶子节点存储主键值。InnoDB聚集索引的叶子节点存储行记录，因此， InnoDB必须要有，且只有一个聚集索引：
> 1. 如果表定义了PK，则PK就是聚集索引
> 2. 如果表没有定义PK，则第一个not NULL unique列是聚集索引
> 3. 否则，InnoDB会创建一个隐藏的row-id作为聚集索引

1. 回表查询：从普通索引无法直接定位行记录，需要回表查询，先定位主键值，再定位行记录，通常情况下，需要扫码两遍索引树，性能较扫一遍索引树更低

![image](https://img2018.cnblogs.com/blog/885859/201907/885859-20190729184911699-676257427.png)

2. 覆盖索引：只需要在一棵索引树上就能获取SQL所需的所有列数据，无需回表，速度更快。常见的实现方法是：将被查询的字段，建立到联合索引里去（索引叶子节点存储了主键id）

### 7.3 锁
#### 数据库死锁是怎么造成的
*《MySQL技术内幕 InnoDB存储引擎》6.7.1*

两个或两个以上的事务在执行过程中，因争夺资源而造成的一种相互等待的现象

#### MySQL如何处理死锁
*《MySQL技术内幕 InnoDB存储引擎》6.7.1*

1. 超时机制：两个事务相互等待时，当一个等待时间超过阈值时，其中一个事务进行回滚，另一个等待的事务就能继续进行
2. 采用等待图进行死锁检测：根据数据库保存锁的信息链表和事务等待链表，得到一个有向图wait-for graph；在每个事务请求锁并发生等待时都会判断是否存在回路（环），若存在则有死锁，回滚undo量最小的事务
    
    ![image](https://note.youdao.com/yws/public/resource/be02344987843483827564c4dc494b13/xmlnote/90FBBC79C9BC48ACB30B30FE9D1A2937/8735)

#### InnoDB支持什么锁
*《MySQL技术内幕 InnoDB存储引擎》6.3.1*

1. 行级锁：InnoDB存储引擎实现了两种标准的行级锁：
    1. 共享锁（S Lock）：允许事务读一行数据
    2. 排他锁（X Lock）：允许事物删除或更新一行数据
2. 意向锁：InnoDB存储引擎支持多粒度锁定，允许事务在行级锁和表级锁同时存在。为了支持在不同粒度上进行加锁，InnoDB存储引擎支持意向锁，即为表级别的锁，两种意向锁：
    1. 意向共享锁（IS Lock）：事务想要获得一张表中某几行的共享锁
    2. 意向排他锁（IX Lock）：事务想要获得一张表中某几行的排他锁

兼容性 | IS | IX | S | X
---|---|---|---|---
IS | 兼容 | 兼容 | 兼容 | 不兼容
IX | 兼容 | 兼容 | 不兼容 | 不兼容
S | 兼容 | 不兼容 | 兼容 | 不兼容
X | 不兼容 | 不兼容 | 不兼容 | 不兼容

由于InnoDB存储引擎支持的是行级锁，因此意向锁不会阻塞除全表扫以外的任何请求

### 7.4 引擎
#### MySQL引擎类别
![image](https://img-blog.csdn.net/20170427001438572?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzIzNTI1NjU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

索引类别 | 事务安全 | 锁 | 其他说明 |应用
---|---|---|---|---|---
MyISAM存储引擎 | 不支持 | 表级锁 | 不能在表损坏后恢复数据 | 做很多count的计算；查询非常频繁
InnoDB存储引擎 | 支持 | 行级锁 | 仅此引擎支持外键 | 更新和查询频繁，多重并发；要求事务，或可靠性要求高；外键约束
MEMORY（HEAP）引擎| 不支持 | 表级锁 | 仅支持长度不变的数据类型 | 内存中存储数据，非常高速
ARCHIVE引擎 | 不支持 | 行级锁和专用的缓存区 | 支持最基本的插入和查询。MySQL 5.5支持索引 | 适合存储大量日志、历史数据
BLACKHOLE引擎 | 支持 | 持mvcc的行级锁 | | |
CSV引擎 | | | 不支持索引，主键列，不允许表中的字段为null| 

1. MyISAM存储引擎

    默认的存储引擎，提供高速存储和检索，以及全文搜索能力。每个表会生成三个文件(文件名就是表名)：.frm 表结构；.MYD 数据；.MYI 索引
    1. 不支持事务。表级锁。不能在表损坏后恢复数据
    2. 适合在以下几种情况下使用：
        1. 做很多count的计算
        2. 查询非常频繁
2. InnoDB存储引擎
    
    具有提交、回滚和崩溃恢复能力的事务安全（ACID兼容）存储引擎。基于聚簇索引建立，聚簇索引对主键查询有很高的性能。不过它的二级索引（secondary index，非主键索引）中必须包含主键列，所以如果主键列很大的话，其他的所有索引都会很大。因此表上的索引较多的话，主键应当尽可能的小。一般来说，如果需要事务支持，并且有较高的并发读取频率，InnoDB是不错的选择
    1. 支持事务和外键。行级锁
    2. 适合在以下几种情况下使用：
        1. 更新和查询都相当的频繁，多重并发
        2. 要求事务，或者可靠性要求比较高
        3. 外键约束，MySQL支持外键的存储引擎只有InnoDB
3. MEMORY（HEAP）引擎
    
    数据保存在内存中，拥有极高的插入、更新和查询效率。但是不稳定，重启以后数据都会丢失。每个表会生成一个.frm文件，该文件只存储表的结构
    1. 不支持事务。支持表级锁，因此并发写入的性能较低。支持长度不变的数据类型，不支持BLOB或TEXT长度可变的数据类型。VARCHAR是一种长度可变的类型，但因为它在MySQL内部当做长度固定不变的CHAR类型，所以可以使用。支持HASH索引和B-Tree索引，默认使用HASH索引。
    2. 内存中存储数据，非常高速。mysql关闭后所有数据消失，mysql启动时会创建空表

    > B-Tree索引的优于HASH索引的是，可以使用部分查询和通配查询，也可以使用<、>和>=等操作符方便数据挖掘。HASH索引进行“相等比较”非常快，但是对“范围比较”的速度就慢多了，因此HASH索引值适合使用在`=`和`<>`的操作符中，不适合在`<`或`>`操作符中，也同样不适合用在`order by`子句中。在内存中存放数据，所以会造成内存的使用，可以通过参数`max_heap_table_size`控制MEMORY表的大小
    
4. ARCHIVE引擎
    
    拥有很好的压缩机制，它使用zlib压缩库，在记录被请求时会实时压缩。支持最基本的插入和查询两种功能。在MySQL 5.5开始支持索引。
    1. 不支持事务。支持行级锁和专用的缓存区，所以可以实现高并发的插入
    2. 适合存储大量日志、历史数据
5. BLACKHOLE引擎
    
    接受但不存储数据，但是如果MySQL启用了二进制日志，SQL语句被写入日志（并被复制到从服务器）。用于做日志记录或同步归档的中继存储。但这种应用方式会碰到很多问题，因此并不推荐。
    1. 支持事务，而且支持mvcc的行级锁
6. CSV引擎
    
    每个表会生成一个.CSV文件，将CSV类型的文件当做表进行处理。把数据以逗号分隔的格式存储在文本文件中，这种文件是一种普通文本文件，每个数据行占用一个文本行。
    1. 不支持索引，即使用该种类型的表没有主键列，也不允许表中的字段为null

### 7.5 SQL优化
#### `limit`为什么慢
[mysql优化之limit优化](https://zhuanlan.zhihu.com/p/29090098)

limit扫描的额外行数过多，因此效率低
1. 直接`limit`查询：将扫描全表
    
    ```sql
    select * from actor limit 1,1;
    ```

2. 加上合适的`order by`：扫描目标行及目标行以前的所有行
    
    ```sql
    select * from actor order by actor_id limit 10,1;
    ```

#### 优化SQL `select * from t limit 1,100`
*《高性能MySQL》6.7.5*

1. 避免请求不必要的数据：避免`select *`的写法
2. 将`limit`替换为已知位置的查询：使用`where`

    ```sql
    select id, name from t where position between 10 and 20;
    ```

3. 避免使用`offset`：（高效率实现上下翻页）
    
    ```sql
    // 查询第一页
    select * from t order by position desc limit 20;
    // 查询结果为 position 1020到1001的记录，则查询下一页
    select * from t where position < 1001 order by position desc limit 20;
    ```

#### ==数据库设计中遇到的问题==
1. wl case：时间排序，会有相同时间情况。用多参数排序
2. 大表翻页

---
## 8 网络
### 8.1 网络基础
#### 长连接和短链接
1. 短连接：连接->传输数据->关闭连接。比如：HTTP是无状态的的短链接，浏览器和服务器每进行一次HTTP操作，就建立一次连接，但任务结束就中断连接
2. 长连接：连接->传输数据->保持连接->传输数据->……->直到一方关闭连接（多是客户端关闭连接），长连接建立SOCKET连接后不管是否使用都保持连接，但安全性较差。比如：数据库的连接用长连接

#### 从在浏览器输入网址到返回页面，经历了什么
1. DNS解析
2. TCP连接
3. 发送HTTP请求
4. 服务器处理请求并返回HTTP报文
5. 浏览器解析渲染页面
6. 连接结束

### 8.2 TCP协议
#### TCP协议为什么是三次握手，两次为什么不可以
TCP 是可靠通信协议：接收方收到的数据是完整、有序、无差错的。

为了实现可靠传输，发送方和接收方始终需要同步（SYNchronize）序号。 需要注意的是，序号并不是从 0 开始的，而是由发送方随机选择的初始序列号（Initial Sequence Number, ISN）开始。由于TCP是一个双向通信协议，通信双方都有能力发送信息，并接收响应。因此，为了建立双向连接，通信双方都需要随机产生一个初始的序列号， 并且把这个起始值告诉对方。

![image](https://note.youdao.com/yws/res/3443/WEBRESOURCE496f621895461a1f26b71cb30240c991)
![image](https://img-blog.csdn.net/20180919162218818?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xlbmd4aWFvMTk5Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### ==TCP链接通道里有什么，报文包含什么内容==
[TCP报文格式详解](https://blog.csdn.net/mary19920410/article/details/58030147)

#### TCP可靠传输（如果传输中丢失了报文如何处理）
*《计算机网络—自顶向下方法》3.5.4*

简化的TCP发送方有3个与发送和重传有关的主要事件：
1. 从上层接收数据：TCP从应用程序接收数据，将数据封装在一个报文段中，并把该报文段交给IP。每个报文段都包含一个序号，及报文段第一个数据字节的字节流编号。如果定时器还没有为某些其他报文段运行，则当报文段传给IP时，TCP就启动该定时器，间隔为TimeoutInterval
2. 定时器超时：TCP通过重传引起超时的报文段来响应超时事件。然后TCP重启定时器
3. 收到ACK：TCP将ACK的值y与它的变量SendBase进行比较。SendBase是最早未被确认的字节的序号。TCP采用累积确认，所以y确认了字节编号在y之前的所有字节都已经收到。如果y>SendBase，则该ACK是在确认一个或多个先前未被确认的报文段。因此发送方更新它的SendBase变量；如果当前有未被确认的报文段，TCP还要重新启动定时器

#### TCP流量控制（如果发送方的速率高于接收方，如何避免接收缓存溢出）
*《计算机网络—自顶向下方法》3.5.5*

TCP为它的应用程序提供了流量控制服务以消除发送方使接收方缓存溢出的可能性。流量控制因此是一个速度匹配服务，即发送方的发送速率与接收方应用程序的读取速率相匹配

流量控制的实现：TCP通过让发送方维护一个称为接收窗口的变量（TCP报文段首部的接收窗口字段）来提供流量控制。通俗的讲，接收窗口用于给发送方一个指示——该接收方还有多少可用的缓存空间。因为TCP是全双工通信，在连接两端的发送方都各自维护了一个接收窗口

#### TCP连接，其中一端断电会如何
1. TCP连接双方定时发握手消息
2. 利用TCP协议栈中的KeepAlive探测

#### 能ping通的两台机器是否一定能通过TCP连接能成功
不一定
1. ping命令使用ICMP协议：Internet控制报文协议，是TCP/IP协议簇的一个子协议，属于网络层协议。用于在IP主机、路由器之间传递控制消息。控制消息是指网络通不通、主机是否可达、路由是否可用等网络本身的消息
    
    > ping用来探测主机到主机之间是否可通信，如果不能ping到某台主机，表明不能和这台主机建立连接。它发送ICMP回送请求消息给目的主机，ICMP协议规定：目的主机必须返回ICMP回送应答消息给源主机。如果源主机在一定时间内收到应答，则认为主机可达

2. TCP：传输控制协议，是一种面向连接的、可靠的、基于字节流的传输层通信协议

## 9 操作系统
### 9.1 线程 进程
#### 线程进程的区别（资源、通信）
1. 进程是资源（CPU、内存等）分配的基本单位，它是程序执行时的一个实例。程序运行时系统就会创建一个进程，并为它分配资源，然后把该进程放入进程就绪队列，进程调度器选中它的时候就会为它分配CPU时间，程序开始真正运行
2. 线程是程序执行时的最小单位，它是进程的一个执行流，是CPU调度和分派的基本单位，一个进程可以由很多个线程组成，线程间共享进程的所有资源，每个线程有自己的堆栈和局部变量。线程由CPU独立调度执行，在多CPU环境下就允许多个线程同时运行。同样多线程也可以实现并发操作，每个请求分配一个线程来处理

线程和进程区别和优劣：
1. 进程是资源分配的最小单位，线程是程序执行的最小单位。
2. 地址空间：
    1. 进程有自己的独立地址空间，每启动一个进程，系统就会为它分配地址空间，建立数据表来维护代码段、堆栈段和数据段，这种操作非常昂贵。多进程程序更健壮，而一个进程死掉并不会对另外一个进程造成影响，因为进程有自己独立的地址空间
    2. 线程是共享进程中的数据的，使用相同的地址空间，因此CPU切换一个线程的花费远比进程要小很多，同时创建一个线程的开销也比进程要小很多。多线程程序只要有一个线程死掉，整个进程也死掉了
3. 通信
    1. 进程之间的通信需要以通信的方式（IPC）进行。不过如何处理好同步与互斥是编写多线程程序的难点。
    2. 线程之间的通信更方便，同一进程下的线程共享全局变量、静态变量等数据

单线程与多线程：
1. 单线程（单个cpu等运行）：比如，打开任意一个浏览器，里面有多个标签页；当某一个标签页系统崩溃时，其他标签页也不能使用，必须关掉浏览器。
2. 多线程（多个cpu等运行）：比如，浏览器中有多个标签页，当某一个标签页系统崩溃时，只是该标签页不能使用，不影响其他标签页的正常使用。

#### 线程有几种状态
线程的五种状态：新建状态、就绪状态、运行状态、阻塞状态及死亡状态

### 9.2 死锁
#### 多线程死锁
[用个通俗的例子讲一讲死锁](https://zhuanlan.zhihu.com/p/26945588)
1. 死锁的定义

    死锁是指多个线程因竞争资源而造成的一种僵局（互相等待），若无外力作用，这些进程都将无法向前推进
2. 系统资源的竞争
    1. 竞争不可剥夺资源（例如：系统中只有一台打印机，可供进程P1使用，假定P1已占用了打印机，若P2继续要求打印机打印将阻塞）
    2. 竞争临时资源（临时资源包括硬件中断、信号、消息、缓冲区内的消息等），通常消息通信顺序进行不当，则会产生死锁
3. 死锁产生的必要条件：死锁发生时，四个条件必须同时满足，其中任意一个不成立，死锁就不会发生
    1. 互斥条件：指进程对所分配到的资源进行排它性使用，即在一段时间内某资源只由一个进程占用。如果此时还有其它进程请求资源，则请求者只能等待，直至占有资源的进程用毕释放（只有一副钥匙）
    2. 请求和保持条件：指进程已经保持至少一个资源，但又提出了新的资源请求，而该资源已被其它进程占有，此时请求进程阻塞，但又对自己已获得的其它资源保持不放（拿着红钥匙的人在没有归还红钥匙的情况下，又提出要蓝钥匙）
    3. 不剥夺条件：指进程已获得的资源，在未使用完之前，不能被剥夺，只能在使用完时由自己释放（除非归还了钥匙，不然一直占用着钥匙）
    4. 环路等待条件：指在发生死锁时，必然存在一个进程——资源的环形链，即进程集合{P0，P1，P2，···，Pn}中的P0正在等待一个P1占用的资源；P1正在等待P2占用的资源，……，Pn正在等待已被P0占用的资源（拿着红钥匙的人在等蓝钥匙，同时那个拿着蓝钥匙的人在等红钥匙）
4. 避免死锁

    要避免出现死锁的问题，只需要破坏四个条件中的任何一个就可以了
    1. 破坏互斥条件
    
        ![image](https://pic1.zhimg.com/80/v2-6245ebc4fa33da062dd3269696c757d0_hd.jpg)
        
        只有一副钥匙，这是形成死锁的最关键的原因。显然，如果我们能在两个线程跑之前，能给每个线程单独拷贝一份钥匙的副本，就能有效的避免死锁了。当然，这种方法试用范围并不广。因为有时如果系统拷贝那副钥匙的成本极高，而线程又很多的话，这种方法就不适用了
    2. 破坏请求和保持条件
    
        ![image](https://pic3.zhimg.com/80/v2-5897c4b4a827a3a55886d4eed142c282_hd.jpg)
        
        任何一个线程“贪心”，都可能会导致死锁。大致就是说有了一把钥匙还没还就要另一把。这里我们可以通过规定在任何情况下，一个线程获取一把钥匙之后，必须归还了钥匙之后才能请求另一把钥匙，就可以有效解决这个问题
    3. 破坏不剥夺条件
    
        ![image](https://pic3.zhimg.com/80/v2-581b37523034b9c383be05a2bc3d46b2_hd.jpg)
        
        除非线程自己还钥匙，否则线程会一直占有钥匙，是形成不可剥夺条件的原因。这里，我们可以通过设置“最长占用时间“的阈值来解决这个问题——如果过了10分钟仍然没有进入下一个步骤，则归还已有的钥匙。这样的话，两个线程都能取到所需的钥匙继续下去了
    4. 破坏环路等待条件
    
        ![image](https://pic1.zhimg.com/80/v2-360a73cf950da233cf277a95db8147a4_hd.jpg)
        
        会出现死锁的两两组合，一定都是一个线程先取了红钥匙而另一个线程先取了蓝钥匙，从而导致了可能形成了“环路等待”。所以我们可以强制规定任何线程取钥匙的顺序只能是 “先取蓝钥匙再取红钥匙”的话，就能避免死锁了

#### ==银行家算法==
[操作系统：银行家算法（避免死锁）](https://blog.csdn.net/s634772208/article/details/46324257)

### 9.3 存储管理
#### 分页算法，FIFO、LRU
1. FIFO：先进先出页面置换算法
2. LRU：Least Recently Used 最近最少使用算法

---
## 10 数据结构 算法
### 10.1 数据结构
#### 了解的数据结构
![image](https://gss1.bdstatic.com/9vo3dSag_xI4khGkpoWK1HF6hhy/baike/c0%3Dbaike80%2C5%2C5%2C80%2C26/sign=1da695bc58b5c9ea76fe0bb1b450dd65/71cf3bc79f3df8dcf83d2c26ce11728b47102800.jpg)

#### 红黑树插入元素的过程（变色）
[红黑树 性质及操作 维基百科](https://wikipedia.hk.wjbk.site/wiki/%E7%BA%A2%E9%BB%91%E6%A0%91)

红黑树是每个节点都带有颜色属性的二叉查找树（自平衡的查找树），颜色为红色或黑色。在二叉查找树强制一般要求以外，对于任何有效的红黑树我们增加了如下的额外要求（保证了红黑树从根到叶子的最长路径不会超过最短路径的2倍）：
1. 节点是红色或黑色
2. 根节点是黑色
3. 每个叶子节点都是黑色的空节点（NIL节点）
4. 每个红色节点的两个子节点都是黑色(从每个叶子到根的所有路径上不能有两个连续的红色节点)
5. 从任一节点到其每个叶子的所有路径都包含相同数目的黑色节点

#### Java在哪里引入了红黑树
`TreeMap` `HashMap`

#### 为什么要引入红黑树
1. 红黑树是一个平衡二叉查找树，够以O(lgn)的时间复杂度进行搜索、插入、删除操作
2. 红黑树放弃了追求完全平衡，追求大致平衡，在与平衡二叉树的时间复杂度相差不大的情况下，保证每次插入最多只需要三次旋转就能达到平衡，实现起来也更为简单

#### 红黑树的查找的时间复杂度
```math
O(lgn) = O(log_2n)
```

#### 队列和栈的区别
1. 队列：先进先出
2. 栈：先进后出

### 10.2 算法
#### 常见排序算法
![image](https://upload-images.jianshu.io/upload_images/1156494-62f859c2ac6f95ff.png)

#### 堆排序
```java
public class HeapSort {
    private int[] heap;
    private int size;

    private void swap(int i, int j) {
        int temp = heap[i];
        heap[i] = heap[j];
        heap[j] = temp;
    }

    public int getFirstVal() {
        return size > 0 ? heap[0] : 0;
    }

    public void setFirstVal(int val) {
        heap[0] = val;
    }

    public int delLastVal() {
        return size > 0 ? heap[--size] : 0;
    }

    // i节点更新后，调整最大堆
    public void minHeapify(int i) {
        int leftVal = (i << 1) + 1 < size ? heap[(i << 1) + 1] : Integer.MAX_VALUE;
        int rightVal = (i << 1) + 2 < size ? heap[(i << 1) + 2] : Integer.MAX_VALUE;
        if (leftVal <= rightVal && leftVal < heap[i]) {
            swap(i, (i << 1) + 1);
            minHeapify((i << 1) + 1);
        } else if (rightVal < leftVal && rightVal < heap[i]) {
            swap(i, (i << 1) + 2);
            minHeapify((i << 1) + 2);
        }
    }

    public void buildMinHeap(int[] array) {
        this.size = array.length;
        this.heap = new int[size];
        System.arraycopy(array, 0, heap, 0, size);
        for (int i = (size >> 1) - 1; i >= 0; --i) {
            minHeapify(i);
        }
    }

    private int[] ascendingSort(int[] array) {
        buildMinHeap(array);
        int[] ascArray = new int[array.length];
        for (int i = 0; i < array.length; ++i) {
            ascArray[i] = getFirstVal();
            setFirstVal(delLastVal());
            minHeapify(0);
        }
        return ascArray;
    }
}
```

#### 一句话描述动态规划，举一个生活中的例子
1. 求出f(n)，只需要知道几个更小的f(c)。我们将求解f(c)称作求解f(n)的“子问题”。这就是DP（动态规划，dynamic programming）
2. 生活问题

    假设一个土豪，身上带了足够的1、5、10、20、50、100元面值的钞票。现在的目标是凑出某个金额w，需要用到尽量少的钞票。长期的生活经验表明，贪心策略是正确的。
    
    但是，如果我们换一组钞票的面值，贪心策略就也许不成立了。如果钞票面额分别是1、5、11，那么我们在凑出15的时候，贪心策略会出错：15=1×11+4×1（贪心策略使用了5张钞票）；15=3×5（正确的策略，只用3张钞票）
    
    贪心策略错在了“鼠目寸光”。贪心是一种只考虑眼前情况的策略。
    
    如果直接暴力枚举凑出w的方案，明显复杂度过高。太多种方法可以凑出w了，枚举它们的时间是不可承受的。我们现在来尝试找一下性质：
    
    w=15时，
    1. 如果取11，接下来就面对w=4的情况
    2. 如果取5，则接下来面对w=10的情况
    3. 如果取1，则接下来面对w=14的情况
    
    我们发现这些问题都有相同的形式：“给定w，凑出w所用的最少钞票是多少张？”
    
    用f(n)来表示“凑出n所需的最少钞票数量”。那么，
    1. 取11：cost = f(4) + 1 = 4 + 1 = 5
    2. 取5：cost = f(10) + 1 = 2 + 1 = 3
    3. 取1：cost = f(14) + 1 = 4 + 1 = 5  
    
    `f(n)`只与`f(n - 1)` `f(n - 5)` `f(n - 11)`相关，确切的说：`f(n) = min{f(n-1), f(n-5), f(n-11)} + 1`
    
    要求出f(n)，只需要求出几个更小的f值；既然如此，我们从小到大把所有的f(i)求出来就好了
    
    它与暴力的区别在于暴力枚举了“使用的硬币”，然而这属于冗余信息。要求出f(15)，只需要知道f(14),f(10),f(4)的值。其他信息并不需要。舍弃了冗余信息。我们只记录了对解决问题有帮助的信息——f(n).　　
    
    我们能这样干，取决于问题的性质：求出f(n)，只需要知道几个更小的f(c)。我们将求解f(c)称作求解f(n)的“子问题”。这就是DP（动态规划，dynamic programming）

#### 栈的最大值问题
可以修改栈的存储方式，push，pop的操作
1. 方法1：时间复杂度`O(1)`，空间复杂度`O(n)`。一个数据栈`Sn`，一个最大值栈`Sm`
    1. `push()`方法：将元素`push`入数据栈`Sn`；当`push`入栈的元素大于/等于当前最大值栈`Sm`的栈顶元素时，将该元素`push`入最大值栈`Sm`
    2. `pop()`方法：将数据栈`Sn`栈顶元素`pop`出栈`Sn`；当`pop`出栈的元素等于最大值栈`Sm`的栈顶元素时，将最大值栈`Sm`栈顶元素`pop`出栈
    3. 最大值：`Sm`栈顶始终保存当前栈中的最大元素
2. 方法2：若能提前读入所有元素/所有元素均为非负数，则可使用本方法。时间复杂度`O(1)`，空间复杂度`O(1)`。一个数据栈`Sn`，一个最大值`max`（若可提前读入元素，则`max`的初始值为最小元素）
    1. `push()`方法：将`当前元素-max`的值`push`入栈。若当前元素大于等于`max`，将`max`的值替换为当前元素
    2. `pop()`方法：`pop`出栈顶元素。若栈顶元素为负数，则当前值为`栈顶元素+max`；若栈中元素为非负数，则当前值为`max`，并将`max`替换为`max-栈顶元素`
    3. 最大值：`max`始终保存当前栈中的最大元素

---
## 11 Linux
### 
#### 使用 Linux命令找出日志文件中访问量最大的top10 IP地址
[使用Linux命令找出日志文件中访问量最大的top10 IP地址](https://www.cnblogs.com/zhaijing/p/9767285.html)

```
cat  test.log|awk -F" " '{print $2}'|sort|uniq -c|sort -nrk 1 -t' '|awk -F" " '{print $2}'|head -10
```

---
## 12 设计题
### 
#### 设计一个路由，为编号为1、2、3、4、5的五台服务器分配流量，期望比例为1：2：3：3：1
1. 方法1：利用随机数生成对应的服务器编号：`routeNum = (1/3)*random(1,5)+(1/3)*random(2,4)+(1/3)*random(3,4)`其中`random(n,m) = random(m-n)+n`表示随机生成`[n,m]`区间内的数字
2. 方法2：根据当前流量比例动态选择当前请求对应服务器编号

---
## 13 Coding
### 
#### 非递归实现二叉树的中序遍历

---
## 14 智力题


---
## 15 项目
### 
#### 解释：低耦合性、可扩展性、可复用性
1. 低耦合性：高内聚低耦合，是软件工程中的概念，是判断软件设计好坏的标准，主要用于程序的面向对象的设计，主要看类的内聚性是否高，耦合度是否低。目的是使程序模块的可重用性、移植性大大增强。通常程序结构中各模块的内聚程度越高，模块间的耦合程度就越低。内聚是从功能角度来度量模块内的联系，一个好的内聚模块应当恰好做一件事，它描述的是模块内的功能联系；耦合是软件结构中各模块之间相互连接的一种度量，耦合强弱取决于模块间接口的复杂程度、进入或访问一个模块的点以及通过接口的数据
2. 可扩展性（可伸缩性）：一种对软件系统计算处理能力的设计指标，高可伸缩性代表一种弹性，在系统扩展成长过程中，软件能够保证旺盛的生命力，通过很少的改动甚至只是硬件设备的添置，就能实现整个系统处理能力的线性增长，实现高吞吐量和低延迟高性能
3. 可复用性：复用的好处可以得到较高的生产效率以及随之而来的成本降低、较高的软件质量（错误可以更快的被纠正）以及恰当的使用复用可以改善系统的可维护性

---
## 16 论文
http://crad.ict.ac.cn/fileup/HTML/2018-2-289.shtml
### 主要工作
给定一个需要加速的目标应用，确定其中哪些部分最适合在DRAM中加速：理想的加速目标应该是内存带宽受限的，因为3D堆叠DRAM具有比现有系统高一个数量级的高内存带宽。数据重组只包含CPU和DRAM之间的往返数据移动，它不消耗浮点运算周期。因此，在DRAM中加速数据重组是很自然的想法

文中，进行了一个hash joins的案例研究，探索如何将它在CPU和DRAM之间进行合适的加速任务划分.选择hash joins有3个原因：
1. hash joins不仅是传统数据库系统的基本操作，还是诸如Spark [9] 等主流大数据系统的关键操作
2. 很多文献都对hash joins做了充分研究 [10-15] ，因此加速的baseline已经高度优化；
3. 最先进的hash joins: optimized version of radix join algorithm(PRO) [11,15] 是带宽资源受限的，它可能会从内存加速中受益.我们对PRO以及PARSEC benchmark suite中具有代表性的多线程应用程序分别进行了带宽分析

> DRAM(Dynamic Random Access Memory)，即动态随机存取存储器，最为常见的系统内存。DRAM 只能将数据保持很短的时间。为了保持数据，DRAM使用电容存储，所以必须隔一段时间刷新(refresh)一次，如果存储单元没有被刷新，存储的信息就会丢失。 (关机就会丢失数据)