# Java核心技术卷Ⅰ

## 阅读笔记	7.13 ~ 7.21

起始日期：2022.7.10

---



## 第三章：Java 的基本程序涉及结构

**数值之间的转化：**

**无信息丢失的转化：**
	byte -> short -> int -> long
	char -> int
	int -> double
	float -> double

---

### 子串：

substring 方法的第二个参数是不想复制的第一个位置。这里要复制的位置为 0、1 和 2 的字符。在 substring 中从 0 开始计数，直到 3 位置，但不包括3.

```java
public class Main {
    public static void main(String[] args) {

        String greeting = "Hello";
        String substring = greeting.substring(0, 3);
        System.out.println(substring);
    }
}
```

​	

### 静态方法 join:

需要把多个字符串放在一起，用一个界定符分割，可以使用静态 join 方

```java
String all = String.join(" ! ", "ABC", "123");
        System.out.println(all);
```



字符串为不可变，不能修改Java字符串中的单个字符，但是可以通过 substring 方法进行拼接

---

### 检测字符串是否相等

可以使用 equals 方法 检测两个字符串是否相等。
想要检测两个字符串是否相等，而不区分大小写，可以使用 equalsIgnoreCase 方法

```java
String greeting02 = "hello";
System.out.println(greeting.equalsIgnoreCase(greeting02));
```

---

### 大数：

java.math 包中 两个 很有用的类。 BigInteger 和 BigDecimal

处理大数类需要使用大数类中的 add 和 multiply 等方法

---

### 增强 for 循环

for (variable : collection) statement

它定义了一个变量用于暂存集合中的每一个元素，并执行相应的语句。collection 这一集合表达式必须是一个数组或者是一个实现了 Iterable 接口的类的对象。	

---

### 多维数组，不规则数组

**java.util.Arrays 常用方法**

**1.static void sort**	//使用优化的快速排序算法对数组进行排序

**2.
static int binarySearch(xxx[] a, xxx v)**	
**static int binarySearch(xxx[] a, int start, int end, xxx v)**
使用二分查找算法在有序数组 a 中查找值 v。如果找到 v，则返回相应的下表；否则，返回一个负数值 r 。**-r-1是 v 应该插入的位置**



不规则数组的构造和遍历

```java
public class Exercise01 {

    //不规则数组定义与遍历
    @Test
    public void test01() {
        int[][] multiArray = new Exercise01().createMultiArray(10);
        printMultiArray(multiArray);
    }

    public int[][] createMultiArray(int maxNum) {
        int[][] ints = new int[maxNum + 1][];
        //先创建不规则数组
        for (int i = 0; i <= maxNum; i++) {
            ints[i] = new int[i + 1];
        }
        //给数组赋值
        int temp;   //临时变量，用于赋值
        for (int i = 0; i < ints.length; i++) {
            for (int j = 0; j < ints[i].length; j++) {
                if (j == 0 | j == ints[i].length - 1) {
                    ints[i][j] = 1; //每一行的开头和结尾为 1
                } else {
                    ints[i][j] = (int) (Math.random() * maxNum);    //否则，给予任意的值
                }
            }
        }
        return ints;
    }

    //遍历二维数组 【规则 / 不规则】
    public void printMultiArray(int[][] arr) {
        for (int i = 0; i < arr.length; i++) {
            for (int j = 0; j < arr[i].length; j++) {
                System.out.print(arr[i][j] + "\t") ;
            }
            System.out.println();
        }
    }
}
```

---



## 第四章：对象与类

1. 对象变量并没有实际包含一个对象，它只是引用一个对象

2. 构造器的注意事项：

   构造器与类同名
   每个类可以有一个以上的构造器
   构造器可以有 0 个、1个或多个参数
   构造器没有返回值
   构造器总是伴随着 new 操作符一起调用

3. **final 关键字:** 拥有 final 关键字的字符按必须在构造对象时初始化。
   也就是说，必须确保在每一个构造器执行后，这个字段的值已经设置。并且以后不能再修改这个字段。

4. **静态字段 static**
   如果将一个属性定义为静态属性，则每个类只有一个这样的属性。即使有多个对象，也只是共享一个静态属性。即使没有对象，静态属性也存在。因为它属于类，而不属于任何单个对象

5. **静态常量 static final**

6. **静态方法： static ...**：静态方法是不再对象上执行的方法。静态方法不能访问非静态变量，因为它不适用在对象上执行操作

   使用静态方法的两种情况：
   1.方法不需要访问对象状态，因为它需要的所有参数都通过显示参数提供
   2.方法只需要访问类的静态字段。

---



## 工厂方法：

```java
//工厂方法
        /**
         * 类使用静态方法来构造对象
         */
        NumberFormat currencyInstance = NumberFormat.getCurrencyInstance();
        NumberFormat percentInstance = NumberFormat.getPercentInstance();
        double x = 0.1;
        System.out.println(currencyInstance.format(x));
        System.out.println(percentInstance.format(x));
```

---

## 方法参数：

**一个方法不能修改基本数据类型的参数：**方法得到的是所有参数值的一个副本。具体来讲，方法不能修改传递给它的任何参数变量的内容

**但是可以实现一个改变对象参数状态的方法：**因为方法得到的是对象引用的副本（参数变量），原来的对象引用和这个副本都引用了同一个对象；方法结束后，参数变量 x 不再使用。但是对象变量继续引用对象。

### Java中的方法参数的能力总结：

1. 方法不能修改基本数据类型的参数
2. 方法可以改变对象参数的状态（属性的值）
3. 方法不能让一个对象参数引用一个新的对象

测试代码：

```java
public class Exercise02 {
    public static void main(String[] args) {
        /*
        测试方法能否修改 基本数据类型
         */
        System.out.println("Test tripleValue:");
        double percent = 10;
        System.out.println("Before: percent = " + percent);
        tripleSalary(percent);
        System.out.println("After: percent = " + percent);

        /*
        测试方法能否改变引用类型
         */
        System.out.println("\n Test tripleSalary");
        Employee hzx = new Employee("Hzx", 2000);
        System.out.println("Before: Salary = " + hzx.getSalary());
        tripleSalary(hzx);
        System.out.println("After: Salary = " + hzx.getSalary());

        /*
        测试方法能否改变对象引用的对象
         */
        System.out.println("\n Test swap");
        Employee a = new Employee("A", 2000);
        Employee b = new Employee("B", 3000);
        System.out.println("Before: a = " + a.getName());
        System.out.println("Before: b = " + b.getName());
        swap(a, b);
        System.out.println("After: a = " + a.getName());
        System.out.println("After: b = " + b.getName());

    }

    public static void tripleSalary(double x) {
        x = 3 * x;
        System.out.println("End of the method: x = " + x);
    }

    public static void tripleSalary(Employee e) {
        e.raiseSalary(200);
        System.out.println("End of the method: x = " + e.getSalary());
    }

    public static void swap(Employee e1, Employee e2) {
        Employee temp = e1;
        e1 = e2;
        e2 = temp;
        System.out.println("End of the method:" + e1.getName());
        System.out.println("End of the method:" + e2.getName());
    }
}

class Employee {
    private String name;
    private double salary;

    public Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public double getSalary() {
        return salary;
    }

    public void setSalary(double salary) {
        this.salary = salary;
    }

    public void raiseSalary(double byPercent) {
        double raise = this.salary * byPercent / 100;
        this.salary += raise;
    }
}
```

---



## 构造方法：

注意：仅当没有任何其他构造器的时候，你次啊会得到一个默认的无参构造器。

---

## 类设计的技巧：

1. 一定要保证数据私有
2. 一定要对数据进行初始化
3. 不要在类中使用过多的基本类型
4. 不是所有的成员属性都需要单独的getter 和 setter方法
5. 分解有过多职责的类
6. 类名和方法要能体现它们的职责

---

## 继承：

1. 在覆盖一个方法的时候，子类方法 不能低于 父类方法 的可见性

2. 被 final 修饰的方法不能被 重写（覆盖）

3. 强制类型转换：

   在进行强制类型转换之前，先查看是否能够成功地转换。

   ```java
   if(staff[i] instanceof Manager) {
   	boss = (Manager) staff[i];
   }
   ```

   

4. 抽象类：

   即使不包含抽象方法，也可以将类声明为抽象类

   抽象类不能实例化

5. static boolean equal(xxx[] a, xxx[] b):     如果两个数组长度相同，并且再对应的位置上数据元素也相同，将返回true。

---

## hashCode

需要组合多个散列值时，可以调用 Object.hash 并提供所有这些参数。

```java
public int hasCode() {
	return Object.hash(name, salary, hireDay);
}
```

---

## 自动装箱和自动拆箱

boolean、byte、char <= 127	介于 -128 ~ 127 之间的 short 和 int 被包装到固定的对象中。

```java
//包装类引用可能为 null， 所以自动装箱有可能会抛出一个 NullPointerException 异常：
Integer n = null;
System.out.println(2 * n);

//
```



---

## 反射

能够分析类能力的程序称为	反射

**一个 Class 对象实际上表示的是一个类型，这可能是 类，也可能不是类。**
如：	int 不是类， 但是 int.class 是一个 Class 类型的对象

### 通用数组拷贝

```java
 public static Object goodObject(Object a, int newLength) {
        //获取反射对象
        Class<?> aClass = a.getClass();
        //判断是否为数组
        if (!aClass.isArray()) {
            return false;
        }
        //判断数组类型
        Class<?> componentType = aClass.getComponentType();
        //获取原数组长度
        int length = Array.getLength(a);
        //初始化新数组
        Object newArray = Array.newInstance(componentType, newLength);
        //进行拷贝
        System.arraycopy(a, 0, newArray, 0, Math.min(length, newLength));
        return newArray;
    }
```

 

## 接口：

---

1. 接口中的所有方法都是自动 **public** 方法。 因此，在接口中声明方法时，**不必提供关键字**

2. 接口不是类

3. 尽管不能构造接口对象，却能声明接口的变量

4. 接口变量必须引用实现了这个接口的类的对象；

5. 虽然接口中不能包含有实例字段，但是可以包含常量

6. 接口中的方法皆为 public  接口中的字段都为 public static final

7. Java 8 中允许在接口中增加静态方法

8. 默认方法

   添加 default

   ```
   public interface Comparable<T> 
   {
   	default int compareTo(T other) { return 0; }
   }
   ```

   



## 回调设计模式：

---

假设程序中有一个时钟，可以请求每秒通知一次，以便更新时钟的表盘。

```java
public class TimerTest {
    public static void main(String[] args) {
        TimePrinter timePrinter = new TimePrinter();
        Timer timer = new Timer(1000, timePrinter);
        timer.start();
        JOptionPane.showMessageDialog(null, "Quit program");
        System.exit(0);
    }
}

class TimePrinter implements ActionListener {
    @Override
    public void actionPerformed(ActionEvent e) {
        System.out.println("At the tone, the time is :" +
                Instant.ofEpochMilli(e.getWhen()));
        Toolkit.getDefaultToolkit().beep();
    }
}
```



## Lambda 表达式与内部类：

---

对于一个抽象方法的接口，需要对这种接口的对象时，就可以提供一个 lambda 表达式。这种接口称为 函数式接口

接口 Predicate:

```java
//	ArrayList 删除空值的方法
public class Exercise04 {
    public static void main(String[] args) {
        ArrayList<Integer> integers = new ArrayList<>();
        integers.add(1);
        integers.add(2);
        integers.add(3);
        integers.add(null);
        System.out.println("删除null值之前");
        for (int i = 0; i < integers.size(); i++) {
            System.out.print(integers.get(i) + "\t");
        }

        System.out.println("\n删除null值之后");
        integers.removeIf(integer -> integer == null);
        for (int i = 0; i < integers.size(); i++) {
            System.out.print(integers.get(i) + "\t");
        }
    }
}
```



重要限制：在 lambda 表达式中，只能引用值不会改变的变量。



## 内部类：

---

1. 声明局部内部类的时候不能有访问说明。局部类的作用域被限定在声明这个局部类的块中

### 静态内部类：

1. 只要内部类不需要访问外围类对象，就应该使用静态内部类。
2.  与常规内部类不同，静态内部类可以有静态字段和方法
3. 在接口中声明的内部类自动是 static 和 public



## 异常：

---

程序可能在一下 3 种情况下执行 finally 语句

1. 代码没有抛出异常
2. 代码抛出一个异常，并在一个 catch 子句种捕获
3. 代码抛出一个异常，但没有任何 catch 子句捕获这个异常

### finally:

警告：当 finally 子句包含 return 语句时，在方法返回前，会执行  finally 字句快。 如果 finally 块也有一个 return 语句， 这个返回值将会遮蔽原来的返回值



## 集合：

---

### Collection 接口：

Collection 接口扩展了 Iterable 接口。因此对于表中类库中的任何集合都可以使用 "for each" 循环

遍历集合：
1.迭代器
2.for each 循环
3.lambda 表达式

```
iterator.forEachRemaining(element -> do something with element);
```



### 1. List ：

是一个有序集合，有两种方式访问元素：	1.使用迭代器方法问	2.使用一个整数索引访问【随机访问】

**2. NavigableSet 和 NavigableMap**:包含一些用于搜索和遍历有序集和映射的方法
【TreeSet 和 TreeMap 实现了这些接口】

**3.链表：**
链表是一个有序集合，每个对象的位置十分重要。LinkedList.add 方法将对象添加到链表的尾部。
**子接口：ListIterator**	
可以完成将元素添加到链表的中间

```java
    public void test01() {
        LinkedList<Integer> integers = new LinkedList<>();
        integers.add(1);
        integers.add(2);
        integers.add(3);
        ListIterator<Integer> integerListIterator = integers.listIterator();
        integerListIterator.next(); //跳过第一个元素，插入位置为第一个元素之后
        integerListIterator.add(4);
        integerListIterator.add(5);
        Iterator<Integer> iterator = integers.iterator();
        while (iterator.hasNext()) {
            System.out.print(iterator.next() + " ");
        }
    }
```

### **为了避免发生并发修改异常：**

可以根据需要为一个集合关联多个迭代器，前提是这些迭代器只能读取集合。或者，可以再关联一个能同时读写的迭代器



**两个链表之间进行合并插入、删除元素**

```java
public class Test02 {

    public static void main(String[] args) {

        LinkedList<String> a = new LinkedList<>();
        a.add("Amy");
        a.add("Carl");
        a.add("Erica");

        LinkedList<String> b = new LinkedList<>();
        b.add("Bob");
        b.add("Doug");
        b.add("Frances");
        b.add("Gloria");

        ListIterator<String> aListIt = a.listIterator();
        ListIterator<String> bListIt = b.listIterator();

        while (bListIt.hasNext()) {
            if (aListIt.hasNext()) {
                aListIt.next();
            }
            aListIt.add(bListIt.next());
        }

        System.out.println(a);

        //Remove every second word from b
        Iterator<String> iterator = b.iterator();
        while (iterator.hasNext()) {
            iterator.next();    //跳过第一个元素
            if (iterator.hasNext()) {
                iterator.next();    //在跳过一个元素
                iterator.remove();
            }
        }

        System.out.println(b);

        //bulk operation: remove all words in b from a
        a.removeAll(b);
        System.out.println(a);
    }
}
```



### 双端队列：Deque 接口， ArrayDeque 和 LinkedList 类实现了这个接口。

----

**boolean offer(E element):**	如果队列没有满，将给定元素添加到这个队列的队尾并返回 true	而第二个方法返回 false

**E poll():** 假如队列不为空，删除并返回这个队列队头的元素。	如果为空，返回 null

**E peek():**如果队列为空，返回这个队列对头的元素，但不删除。如果队列为空，返回 null



### 优先队列：PriorityQueue	

---

优先队列中的元素可以按照任意顺序插入，但会按照有序顺序进行检索。调用 remove 方法，总会获得当前优先队列总最小的元素。

优先队列典型的用法是**任务调度**



### 映射视图：

---

1. Set<K> keySet()			：可以从这个集中删除元素，但是不能添加任何元素

2. Collection<V> values()     ：可以从这个集中删除元素，但是不能添加任何元素

3. Set<Map.Entry<K, V>> entrySet()    ：可以从这个集中删除元素，但是不能添加任何元素

   ```java
   //如果想要同时查看键值对
   for (Map.Entry<String, Employee> entry : staff.entrySet())
   {
   	String k = entry.getKey();
   	Employee v = entry.getValue();
   	do something with k, v
   }
   
   //或者可以用 forEach 方法
   map.forEach(k, v) -> {
   	fo something with k,v
   }
   
   ```

   

###  Collection 类的 binarySearch:

集合必须是有序的，否则算法会返回错误的答案。

如果 binarySearch 方法返回一个非负的值，这表示匹配对象的索引。

可以利用返回值来计算应该将 element 插入到集合的哪个位置，以保证集合的有序性。

```java
insertionPoint = -i - 1;
```



### 集合与数组的转换：

如果需要把一个数组转换成集合，List.of 包装器可以达到这个目的。

```java
//数组 -> 集合
String[] values = ...;
HashSet<String> strings = new HashSet<>(List.of(values));

//集合 -> 数组
Object[] values = staff.toArray();	//但是这个返回的是对象数组

//提供一个指定类型而且长度为 0 的数组
String[] values = staff.toArray(new String[0]);
//或者可以构建一个大小正确的数组
staff.toArray(new String[staff.size()]);
```



### 批操作：

```java
collection01.retain(collection02);	
//会从collection01中删除所有未在 collection02 中出现的元素

//求交集
HashSet<String> result = new HashSet<>(firstSet);
result.retainAll(secondSet);		//这会保留两个集中都出现的所有元素
   
//对视图应用一个批操作
Map<String, Employee> staffMap = ...;
Set<String> terminatedIDs = ...;
//只需要建立一个链集，并删除终止聘用关系的所有员工的 ID
staffMap.keySet().removeAll(terminatedIDs);
```



### 编写自己的方法:

---

如果编写自己的算法，应该尽可能的地使用 **接口**，而不要使用具体地实现。

```java
//例如：完成这项工作的最通用的集合接口是什么？关心顺序码？如果顺序很重要，就应当接受 List。如果顺序不重要，那么可以接受任意类型的集合。
public void processItems(Collection<Item> items) {		//甚至可以接受 Iterable<Item>
	for(Item item : items) {
		do something with item
	}
}

//再比如：
public ArrayList<Item> lookupItems(...) {		//可以将返回一个 ArrayList 改成 List
    ArrayList<Item> result = new ArrayList();
    return result;
}
```



## Properties

```java
//用一组默认值创建一个空属性映射
Properties(Properties defaults);

//获得一个属性。
String getProperty(String key, String defaultValue);

//设置一个属性
Object setProperty(String key, String value);

//从一个输入流加载一个属性映射
void load(InputStream in) throws IOException;

//将一个属性映射保存到一个输出流。
void store(OuputStream out, String header);

```



## 第12章 并发：

---

实现 Runnable 接口非常简单，只有一个方法： **由于 Runnable 是一个函数式接口，可以用 lambda 表达式创建一个实例**

```java
//用 lambda 表达式创建一个实例
Runnable r = () -> {test code};
//从这个 Runnable 构造一个 Thread 对象
Thread t = new Thread(r);
//启动线程
t.start();

//java.lang.Thread方法
Thread(Runnable target);			//构造一个新线程，调用指定目标的 run() 方法

//启动这个线程，从而调用 run() 方法。这个方法会立即返回。新线程会并发执行
void start();

//调用相关 Runnable 的 run 方法
void run();

//休眠指定的毫秒数
static void sleep(long millis);
```

**警告：**不要调用 Thread 类或 Runnable 对象的 run 方法。直接调用 run 方法只会在同一个线程中执行这个任务 --- 而没有启动新的线程。实际上，应当调用 **Thread.start** 方法，这会创建一个执行 **run** 方法的新线程



## 线程状态：

---

1. New （新键态）
2. Runnable (可运行)
3. Blocked (阻塞)
4. Waiting (等待)
5. Timed waiting (计时等待)
6. Terminated (终止)

```java
//使当前正在执行的线程向另一个线程交出运行权。
static void yield()	
```



### 线程进入非活动状态的途径：

1. 当一个线程试图获取一个内部的对象锁，而这个锁目前被其他线程占有。
2. 当线程等待另一个线程通知调度器出现一个条件时，这个线程就会进入**等待状态**。
3. 有几个方法有超时参数，调用这些方法会让线程进入计时等待（timed waiting)



### 终止线程的原因：

1. run 方法正常退出，线程自然终止

2. 因为一个没有捕获的异常终止了 run 方法，使线程意外终止。

   ```java
   void join();	//等待终止指定的线程
   void join(long mills)	//等待指定的线程终止或等待经过指定的毫秒数
   Thread.State getState()		//得到这个线程的状态； 取值为 NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING 或 TERMINATED
   ```



### 线程属性：

如果循环调用了 sleep, 不要检测中断状态，而应当捕获 InterruptedException 异常。

```java
//向线程发送中断请求。线程的中断状态将被设置为 true。如果当前该线程被一个sleep调用阻塞，则抛出一个 InterruptedException异常
void interrupt();

//测试当前线程是否被中断。注意，这是一个静态方法。它会将当前线程的中断状态重置为 false。
static boolean interrupted;

//测试线程是否被中断。与 static interrupted 方法不同，这个调用不改变线程的中断状态
boolean isInterrupted;

//返回表示当前正在执行的线程的 Thread 对象
static Thread currentThread();
```



### 锁对象：

---

```java
/*
重入锁（reentrant) 锁	因为线程可以反复获得已拥有的锁。
锁有一个持有计数（hold count) 来跟踪对 lock 方法的嵌套使用。被一个锁保护的代码可以调用另一个使用相同锁的方法。
*/
myLock.lock();		//a ReentrantLock object
try {
	critical section;
}
finally {
	myLock.unlock();	//make sure the lock is unlocked even if an exception is thrown;
}

//返回一个与这个锁相关联的条件对象
Condition newCondition();	

//将该线程放在这个条件的等待集中
void await();

//解除该条件等待集中所有线程的阻塞状态
void signalAll();


```



### 使用重入锁实现银行转账业务和查询总余额

```java
//银行类
public class Bank {

    private final double[] accounts;
    private Lock bankLock;
    private Condition sufficientFunds;

    /**
     *  含参构造器
     * @param n
     * @param initialBalance
     */
    public Bank(int n, double initialBalance) {
        this.accounts = new double[n];
        Arrays.fill(accounts, initialBalance);
        bankLock = new ReentrantLock();
        sufficientFunds = bankLock.newCondition();
    }

    /**
     *  进行转账业务
     * @param from  被转账账户
     * @param to    目标转账账户
     * @param amount    转账金额
     */
    public void transfer(int from, int to, double amount) throws InterruptedException {
        bankLock.lock();
        try {
            while (accounts[from] < amount) {      //验证转账金额
                System.out.println("余额不足");
                sufficientFunds.await();
                return;
            }
            System.out.print(Thread.currentThread());
            accounts[from] -= amount;       //被转账用户减去转账金额
            System.out.printf(" %10.2f from %d to %d", amount, from, to);
            accounts[to] += amount;         //目标转账用户增加转账金额
            System.out.printf(" Total Balance: %10.2f%n", getTotalBalance());
            sufficientFunds.signalAll();
        } finally {
            bankLock.unlock();
        }
    }

    /**
     *  获取所有账户的余额总和
     * @return  总金额
     */
    public double getTotalBalance() {
        bankLock.lock();
        try {
            double sum = 0;
            for (double a : accounts) {
                sum += a;
            }
            return sum;
        } finally {
            bankLock.unlock();
        }
    }

    //返回账户的数目
    public int size() {
        return accounts.length;
    }
}


//测试类
public class Test01 {

    public static final int NACCOUNTS = 100;
    public static final double MAX_AMOUNT = 1000;
    public static final int DELAY = 10;
    public static final double INITIAL_BALANCE = 1000;

    public static void main(String[] args) {
        Bank bank = new Bank(NACCOUNTS, INITIAL_BALANCE);
        for (int i = 0; i < NACCOUNTS; i++) {
            int fromAccount = i;
            Runnable r = () -> {
                try {
                    while (true) {
                        //获取随机目标转账账户
                        int toAccount = (int) (bank.size() * Math.random());    
                        double amount = MAX_AMOUNT * Math.random();     //获取随机转账金额
                        bank.transfer(fromAccount, toAccount, amount);
                        Thread.sleep((int) (DELAY * Math.random()));
                    }
                } catch (InterruptedException e) {

                }
            };
            Thread thread = new Thread(r);
            thread.start();
        }
    }
}
```



### 锁和条件的要点做一个总结：

1. 锁用来保护代码片段，一次只能有一个线程执行被保护的代码
2. 锁可以管理试图进入被保护代码段的线程
3. 一个锁可以有一个或多个相关联的条件对象
4. 每个条件对象管理那些已经进入被保护代码段但还不能运行的线程。



---

Java 每个对象都有一个 内部锁。如果这个方法声明时有 **synchronized** 关键字，那么对象的锁将保护整个方法。

内部对象锁只有一个关联条件。wait 方法将一个线程增加到等待集中， notifyAll / notify 方法可以解除等待线程的阻塞。

```java
//解除在这个对象上调用 wait 方法的那些线程的阻塞状态。只能在同步方法和同步块中调用
void NotifyAll();

//导致一个线程进入等待状态
void wait();
```



建议：

1. 最好既不要使用 Lock / Condition 也不使用 synchronized 关键字，在许多情况下，可以使用 java.util.concurrent 包中的某种机制，它会为你处理所有的指令。

2.  尽量使用 synchronized 关键字。

3. 如果特别需要 Lock / Condition 结构提供的额外能力，则使用 Lock / Condition

   ```java
       /**
        *  实现转账 (synchronized)
        * @param from
        * @param to
        * @param amount
        * @throws InterruptedException
        */
       public synchronized void synTransfer(int from, int to, double amount) throws InterruptedException {
           while (accounts[from] < amount) {
               wait();
           }
           System.out.print(Thread.currentThread());
           accounts[from] -= amount;
           System.out.printf(" %10.2f from %d to %d", amount, from, to);
           accounts[to] += amount;
           System.out.printf(" Total Balance: %10.2f%n", getTotalBalance());
           notifyAll();
       }
       
       public synchronized double SynGetTotalBalance() {
           double sum = 0;
           for (double a : accounts) {
               sum += a;
           }
           return sum;
       }
   ```

   

### volatile 关键字：

---

**volatile** 关键字为实例字段的同步访问提供了一种免锁机制。如果声明一个字段为 **volatile**，那么编译器和虚拟机就知道该字段可能被另一个线程并发更新

**警告：**volatile 关键字不能提供原子性。 不能确保翻转字段中的值。不能保证读取、反转和写入不被中断



### 线程安全集合：

---

任何集合类都可以通过使用同步包装器 （synchronization wrapper) 变成线程安全的；

```java
List<E> synchArrayList = Collections.synchronizedList(new ArrayList<E>());
Map<K, V> synchHashMap = Collections.synchronizedMap(new HashMap<K, V>());
```

