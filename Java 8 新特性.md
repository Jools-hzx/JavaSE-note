## Java 8 新特性

### 1.Lambda 表达式：

---

Lambda 是一个 匿名 函数， 我们可以把 Lambda 表达式理解为是 一段 可以传递的代码

### 函数式接口：只包含一个抽象方法的接口，称为函数式接口

可以通过 Lambda 表达式来创建该接口的对象；
我们可以在一个接口上使用 @FunctionalInterface 注解，用来检查是否是一个函数式接口。

```java
public class LambdaTest1 {

    /**
     * 举例： (o1, o2) -> Integer.compare(o1, o2);
     *  ->： lambda 操作符 或 箭头操作符
     *  左边: lambda 形参列表（接口中的抽象方法的形参列表）
     *  右边： lambda 体（骑士就是重写的抽象方法的方法体）
     *
     *  本质：作为函数式接口的实例
     *  使用： 分为 6 种情况
     *  总结：
     *     -> 左边： lambda 形参列表的参数类型可以省略; 如果参数列表只有一个参数； 其一对（）可以省略
     *     -> 右边： lambda 体应该是用一对 {} 包裹，如果 lambda 体只有一条执行语句（可能是return语句），
     *              省略这一对 {} 和 return
     */
    //一： 对于无参无返回值的
    @Test
    public void test01() {
        Runnable run1 = () -> {
            System.out.println("你好 何泽熹");
        };
    }

    //二： Lambda 需要一个参数，但是没有返回值
    @Test
    public void test02() {
        Consumer<String> con = new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        };
        con.accept("ABC");

        System.out.println("===================");
        Consumer<String> con2 = (String s) -> {
            System.out.println(s);
        };
        con2.accept("CDE");
    }

    //三：数据类型可以省略，可以由编译器推断得出，称为“类型推断”
    @Test
    public void test3() {
        Consumer<String> con3 = (s) -> {
            System.out.println(s);
        };
        con3.accept("CDE");
    }

    //四： Lambda 若只需要一个参数时，参数的小括号可以省略
    @Test
    public void test05() {
        Consumer<String> con4 = s -> {
            System.out.println(s);
        };
        con4.accept("CDE");
    }

    //五： Lambda 需要两个或以上的两个参数， 多条执行语句，并且有返回值
    @Test
    public void test06() {
        Comparator<Integer> com2 = ((o1, o2) -> {
            System.out.println(o1);
            System.out.println(o2);
            return o1.compareTo(o2);
        });
        System.out.println(com2.compare(21, 22));
    }

    //六：当 Lambda 体只有一条语句时， return 与大括号若有，都可以省略
    @Test
    public void test7() {
        Comparator<Integer> com2 = ((o1, o2) ->
             o1.compareTo(o2)
        );
        System.out.println(com2.compare(21, 22));

        System.out.println("============================");

        Comparator<Integer> com3 = ((o1, o2) -> o1.compareTo(o2));
        System.out.println(com2.compare(21, 22));
    }
}
```



## Java 四大函数式接口

------

|        函数式接口         | 参数类型 | 返回类型 | 用途              |
| :-----------------------: | :------: | -------- | ----------------- |
|  Consumer<T> 消费型接口   |    T     | void     | accept(T t)       |
| Supplier<T> 供给型接口额  |    无    | T        | T get()           |
| Function<T, R> 函数型接口 |    T     | R        | R apply(T t)      |
|  Predicate<T>断定型接口   |    T     | boolean  | boolean test(T t) |

```java
/**
 * @author Zexi He.
 * @description:
 */
public class LambdaTest2 {
    //消费型接口 Consumer<T> void accept(T t)
    //供给型接口 Supplier<T> T get()
    //函数型接口 Function<T, R>  R apply(T t)
    //断定型接口 Predicate<T>    boolean test(T t)

    @Test
    public void test1() {
        happyTime(500, new Consumer<Double>() {
            @Override
            public void accept(Double aDouble) {
                System.out.println("学习学累了，休息会");
            }
        });

        System.out.println(" ---------- Lambda 表达式 ----------");
        happyTime(500, money -> System.out.println("学习学累了，用 Lambda 表达式来简化"));
    }

    public void happyTime(double money, Consumer<Double> con) {
        con.accept(money);
    }

    @Test
    public void test02() {
        List<String> strings = Arrays.asList("深圳", "兰州", "东京", "北京");
        List<String> stringList = filterString(strings, new Predicate<String>() {
            @Override
            public boolean test(String s) {
                return s.contains("京");
            }
        });
        System.out.println(stringList);

        System.out.println(" ------------- Lambda 表达式 -----------");
        List<String> res = filterString(strings, s -> s.contains("深"));
        System.out.println(res);
    }

    //根据指定规则去过滤指定字符串
    public List<String> filterString(List<String> list, Predicate<String> pre) {
        ArrayList<String> filterStringList = new ArrayList<>();
        for (String s : list) {
            if (pre.test(s)) {
                filterStringList.add(s);
            }
        }
        return filterStringList;
    }
}
```





## 方法引用和构造器引用

---

**1.使用情境：** 当要传递给 Lambda 体的操作，已经与实现的方法了，可以使用方法引用

**2.方法引用：** 本质上就是 Lambda 表达式，而 lambda 表达式作为函数式接口的实例，所有方法引用也是函数式接口的实例

**3.使用格式：** 类（或对象） :: 方法名

**4.分为三种情况:**

 *              4.1:非静态方法
 *              4.2:类调用静态方法
 *          4.3:类调用非静态方法

**5.**方法引用的使用要求，要求接口中的抽象方法的形参列表和返回类型与方法的引用的方法的形参列表和返回类型相同

```java
/**
 * @author Zexi He.
 * @date 2022/7/8 16:30
 * @description:
 *          1.使用情境： 当要传递给 Lambda 体的操作，已经与实现的方法了，可以使用方法引用
 *          2.方法引用： 本质上就是 Lambda 表达式，而 lambda 表达式作为函数式接口的实例，所有方法引用也是函数式接口的实例
 *          3.使用格式： 类（或对象） :: 方法名
 *          4.分为三种情况:
 *              4.1:非静态方法
 *              4.2:类调用静态方法
 *              4.3:类调用非静态方法
 *
 *          5. 方法引用的使用要求，要求接口中的抽象方法的形参列表和返回类型与方法的引用的方法的形参列表和返回类型相同
 */
public class MethodRefTest {

    //情况一：对象 :: 实例方法
    //Consumer 中的 void accept(T t)
    //PrintStream 中的 void println(T t)
    @Test
    public void test01() {
        //Lambda 表达式写法
        Consumer<String> com = str -> System.out.println(str);
        com.accept("何泽熹");

        System.out.println("---------- 方法引用 ------------");
        PrintStream ps = System.out;
        Consumer<String> cons2 = ps::println;
        cons2.accept("韩伟豪");
    }

    @Test
    public void test02() {
        ArrayList<String> strings = new ArrayList<>();
        strings.add("深圳");
        Supplier<String> sup1 = () -> strings.get(0);
        System.out.println(sup1.get());

        System.out.println("---------- 方法引用 ------------");
        Supplier<String> sup2 = strings::toString;
        System.out.println(sup2.get());
    }

    //情况二：类::静态方法
    //Comparator 中的 int compare(T t1, T t2)
    //Integer 中的 int compare(T t1, T t2)
    @Test
    public void test3() {
        Comparator<Integer> com = (t1, t2) -> Integer.compare(t1, t2);
        System.out.println(com.compare(21, 12));

        System.out.println("-----方法引用形式-----");
        Comparator<Integer> com2 = Integer::compare;
        System.out.println(com2.compare(21, 12));
    }

    @Test
    public void test4() {
        Function<Double, Long> func1 = new Function<Double, Long>() {
            @Override
            public Long apply(Double d) {
                return Math.round(d);
            }
        };

        System.out.println("------- Lambda 方法 -------");
        Function<Double, Long> func2 = d -> Math.round(d);
        System.out.println(func2.apply(11.5));

        System.out.println("------- 方法引用 方式 --------");
        Function<Double, Long> func3 = Math::round;
        System.out.println(func3.apply(11.3));
    }

    //情况三： 类::实例方法
    //Comparator 中的 int compare(T t1, T t2)
    //String 中的 int t1.compareTo(t2)
    @Test
    public void test5() {
        System.out.println("---Lambda---");
        Comparator<String> com1 = (s1, s2) -> s1.compareTo(s2);
        System.out.println(com1.compare("ABC", "CDE"));

        System.out.println("----方法引用----");
        Comparator<String> com2 = String::compareTo;
        System.out.println(com2.compare("abc", "abm"));
    }

    //BiPredicate 中的 boolean test(T t1, T t2);
    //String 中的 boolean t1.equals(t2)
    @Test
    public void test6() {
        BiPredicate<String, String> pre1 = (s1, s2) -> s1.equals(s2);
        System.out.println(pre1.test("avd", "dfs"));

        System.out.println("-----方法引用-----");
        BiPredicate<String, String> pre2 = String::equals;
    }
}
```





## 构造器引用：

```java
/**
 * @author Zexi He.
 * @description:
 *      一、构造器引用：
 *          和方法引用类似，函数式接口的抽象方法的形参列表和构造器的形参列表一致
 *          抽象方法的返回值类型 即为 构造器所属的类的类型
 *
 *      二、数组引用：
 *            可以把数组看作是一个特殊的类，则写法与构造器引用是一致的
 */
public class ConstructorRefTest {

    //构造器引用
    //Supplier 中的 T get()
    //Employee 的空参
    @Test
    public void test01() {
        System.out.println("----普通方法----");
        Supplier<Employee> sup1 = new Supplier<Employee>() {
            @Override
            public Employee get() {
                return new Employee();
            }
        };
        System.out.println(sup1.get());

        System.out.println("----Lambda方法----");
        Supplier<Employee> sup2 = () -> new Employee();
        System.out.println(sup2.get());

        System.out.println("----构造器引用----");
        Supplier<Employee> sup4 = Employee::new;
        System.out.println(sup4.get());
    }

    @Test
    //Function 中的 R apply(T t)
    public void test2() {
        System.out.println("---- Lambda 方法 ----");
        Function<String, Employee> function01 = id -> new Employee(id);
        Employee employee01 = function01.apply("123");
        System.out.println(employee01);

        System.out.println("---- 构造器引用 ----");
        Function<String, Employee> function02 = Employee::new;
        Employee employee02 = function02.apply("123");
        System.out.println(employee02);
    }

    @Test
    //BiFunction 中的 apply(T t, U u)
    public void test03() {
        System.out.println("---- Lambda 方法 ----");
        BiFunction<String, String, Employee> function01 = (id, name) -> new Employee(id, name);
        System.out.println(function01.apply("123", "何泽熹"));

        System.out.println("---- 构造器引用 ----");
        BiFunction<String, String, Employee> function02 = Employee::new;
        System.out.println(function02.apply("123", "何泽熹"));
    }

    @Test
    //数组引用
    //Function
    public void test04() {
        // Lambda 表达式
        Function<Integer, String[]> function01 = length -> new String[length];
        String[] arr1 = function01.apply(10);
        System.out.println(Arrays.toString(arr1));

        //数组引用
        Function<Integer, String[]> function02 = String[]::new;
        System.out.println(Arrays.toString(function02.apply(10)));
    }
}
```





# Stream API

---

是数据渠道，用于数据操作源（集合、数组等）所生成的元素序列

### 注意点：

1.自己不会存储元素
2.改变源对象。相反，他们会返回一个持有结果的新 Stream
3.操作是延迟执行的。这意味着他们会等到需要结果的时候才执行

## Stream 的实例化

```java
public class StreamAPI {

    //创建 Stream 方式一：通过集合
    @Test
    public void test01() {
        ArrayList<Employee> employees = new ArrayList<>();
        employees.add(new Employee());
        //返回一个顺序流
        Stream<Employee> stream = employees.stream();
        //返回一个并行流
        Stream<Employee> employeeStream = employees.parallelStream();
    }

    //方式二： 通过数组
    public void test02() {
        //调用 Arrays 类的方法
        int[] arr = new int[]{1, 2, 3, 4, 5};
        IntStream stream = Arrays.stream(arr);
    }

    //方式三：通过 Stream的 of（）
    public void test03() {
        Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5, 6);
    }

    //方式四：创建无限流 造数据
    @Test
    public void test04() {
        //迭代
        //遍历前十个偶数
        Stream.iterate(0, t -> t + 2).limit(10).forEach(System.out::println);
        
        //生成
        Stream.generate(Math::random).limit(10).forEach(System.out::println);
    }
}
```



## Stream 的中间操作

---

```java
/**
 * @author Zexi He.
 * @description:    Stream 的中间操作
 */
public class StreamAPITest01 {

    /**
     *   1 - 筛选与切片
     */
    @Test
    public void test01() {
        ArrayList<Employee> employees = new ArrayList<>();
        employees.add(new Employee("hzx", "123", 12));
        employees.add(new Employee("hsp", "456",12));
        employees.add(new Employee("jhe", "789",11));

        Stream<Employee> stream01 = employees.stream();
        //Lambda (Predicate p ) 接收 Lambda, 从流中排除某些元素
        stream01.filter(employee -> (employee.getAge() == 12)).forEach(System.out::println);

        // limit(n) 截断流，使其元素不超过给定数量
        Stream<Employee> stream02 = employees.stream();
        System.out.println("--- 截断流 ---");
        stream02.limit(3).forEach(System.out::println);

        //skip(n) -- 跳过元素， 返回一个扔掉了前 n 个元素的流。若不足 n 个元素，则返回一个空流
        Stream<Employee> stream03 = employees.stream();
        System.out.println("--- skip ----");
        stream03.skip(2).forEach(System.out::println);

        // distinct -- 筛选，通过流生成元素的 hashCode() 和 equals() 去除重复元素
        //前提： 重写 equals 和 hashCode 的方法； 已属性值比较，而不是对象地址比较
        employees.add(new Employee("刘强东", "123", 12));
        employees.add(new Employee("刘强东", "123", 12));
        employees.add(new Employee("刘强东", "123", 12));
        employees.stream().distinct().forEach(System.out::println);
    }

    /**
     *  映射
     */
    @Test
    public void test02() {
        //map(Function f) 接收一个函数作为参数，将元素转化成其他形式或提取信息
        List<String> strings = Arrays.asList("aa", "bb", "cc");
        strings.stream().map(str -> str.toLowerCase()).forEach(System.out::println);

        Stream<Stream<Character>> streamStream = strings.stream().map(StreamAPITest01::changeStringToStream);
        //遍历方式一： 用 Map
        System.out.println("--- Map ---");
        streamStream.forEach(characterStream -> {
            characterStream.forEach(System.out::println);
        });

        //flatMap(Function f) -- 接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流
        System.out.println("--- FlatMap ---");
        Stream<Character> characterStream = strings.stream().flatMap(StreamAPITest01::changeStringToStream);
        characterStream.forEach(System.out::println);
    }

    //将字符串中的索格字符构成的集合转化为对应的 Stream 实例
    public static Stream<Character> changeStringToStream(String str) {
        ArrayList<Character> characters = new ArrayList<>();
        for (Character character : str.toCharArray()) {
            characters.add(character);
        }
        return characters.stream();
    }

    /**
     *    排序
     */
    @Test
    public void test04() {
        //sorted 自然排序
        List<Integer> list = Arrays.asList(12, 43, 65, 34, 23, -23, 0, 64);
        list.stream().sorted().forEach(System.out::println);
        
        //sorted(Comparator com)    定制排序
        
    }
}
```



## 终止操作：

---

1.匹配与查找 （allMatch 、 anyMatch、 findFirst、count、max、min)
2.规约    reduce(T identity, BinaryOperator)
3.收集    collect(Collector c)

```java
public class StreamAPITest02 {

    /**
     *   一：匹配与查找
     *
     */
    @Test
    public void test01() {
        ArrayList<Employee> employees = getEmployees();
        //allMatch(Predicate p) 检查时是否匹配所有元素
        boolean b = employees.stream().allMatch(employee -> employee.getAge() > 11);
        System.out.println(b);
        //anyMatch(Predicate p) 检查是否至少匹配一个元素
        boolean b1 = employees.stream().anyMatch(employee -> employee.getAge() >= 11);
        System.out.println(b1);
        //noneMatch     检查是否没有匹配的元素
        boolean b2 = employees.stream().noneMatch(employee -> employee.getId().equals("123"));
        System.out.println(b2);
        //findFirst -- 返回值第一个元素
        Optional<Employee> first = employees.stream().sorted((e1, e2) -> e1.getAge() - e2.getAge()).findFirst();
        System.out.println(first);
        //findAny -- 返回当前流中的任意元素
        Optional<Employee> any = employees.parallelStream().findAny();
        System.out.println(any);
    }

    @Test
    public void test02() {
        ArrayList<Employee> employees = getEmployees();
        //count 返回流中元素的总个数
        long count = employees.stream().filter(employee -> employee.getAge() > 11).count();
        System.out.println(count);
        //max(Comparator c) 返回流中的最大值
        Stream<Integer> stream = employees.stream().map(employee -> employee.getAge());
        Optional<Integer> max = stream.max(Integer::compareTo);
        System.out.println(max);
        //min(Comparator) 返回流中的最小元素
        Optional<Employee> min = employees.stream().min((e1, e2) -> e1.getAge() - e2.getAge());
        System.out.println(min);

        //forEach(Consumer c) -- 内部迭代
        employees.stream().forEach(System.out::println);
    }

    /**
     * 二： 规约
     */
    @Test
    public void test03() {
        ArrayList<Employee> employees = getEmployees();
        //reduce(T identity, BinaryOperator) -- 可以将流中元素反复结合起来，得到一个值
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        Integer sum = list.stream().reduce(0, Integer::sum);
        System.out.println(sum);

        //reduce(BinaryOperator) -- 可以将流中元素反复结合起来，得到一个值。返回 Optional<T>
        Stream<Integer> streamAge = employees.stream().map(employee -> employee.getAge());
        //Optional<Integer> result = streamAge.reduce(Integer::sum);
        Optional<Integer> reduce = streamAge.reduce((age1, age2) -> age1 + age2);
        System.out.println(reduce);
    }

    /**
     *  收集
     */
    @Test
    public void test04() {
        //collect(Collector c) 将流转化为其他形式。接收一个 Collector 接口实现
        ArrayList<Employee> employees = getEmployees();
        List<Employee> collect = employees.stream().filter(employee -> employee.getAge() > 10).
                collect(Collectors.toList());
        System.out.println("--- 转化成 List ---");
        collect.forEach(System.out::println);

        Set<Employee> set = employees.stream().filter(employee -> employee.getAge() > 10).
                collect(Collectors.toSet());
        System.out.println("--- 转化为 Set ---");
        set.forEach(System.out::println);
    }

    public static ArrayList<Employee> getEmployees() {
        ArrayList<Employee> employees = new ArrayList<>();
        employees.add(new Employee("hzx", "123", 12));
        employees.add(new Employee("hsp", "456",12));
        employees.add(new Employee("jack", "789",11));
        return employees;
    }
}
```



## Optional 类

---

Optional<T> 类（java.util.Optional) 是一个容器类，它可以保存类型 T 的值

**一：创建 Optional 类对象的方法：**
Optional.of(T, t)	// 必须是非空
Optional.empty()	//创建一个空的 Optional 实例
Optional.ofNullabl(T t)	// t 可以为 null

**二：判断 Optional 容器中包含的对象**
boolean isPresent(): 判断是否包含对象
等

**三：获取Optional 容器的对象**
1.T get(): 如果调用对象包含值，返回该值，否则抛出该异常
2.T orElse(T other): 如果有值则将其返回，否则返回指定的 other 对象
等

```java
public class OptionalTest01 {

    /**
     * Optional.of(T t): 创建一个 Optional 实例，t必须为非空
     * Optional.empty(): 创建一个空的 Optional 实例
     * Optional.ofNullable(T t): t 可以为 null
     */
    @Test
    public void test01() {
        Girl girl = new Girl();
        Optional<Girl> optional_1 = Optional.of(girl);  //必须为非空
        System.out.println(optional_1);
    }

    @Test
    public void test02() {
        Girl girl = new Girl();
        girl = null;        //必须为空
        Optional<Girl> optional_2 = Optional.ofNullable(girl);
        System.out.println(optional_2);

        //orElse(T t1): 如果当前的 Optional 内部封装的 t 是非空的，则返回内部的 t，
        //如果内部的 t 是空的，则返回 orElse() 方法中的参数
        Girl girl1 = optional_2.orElse(new Girl("迪丽热巴"));
        System.out.println(girl1);
    }

    @Test
    public void test03() {
        Boy boy = new Boy();
        //boy = null;
        String girlName = getGirlName02(boy);
        System.out.println(girlName);
    }

    //以前的 方法； 容易出现空指针异常 Boy对象为空 Girl 对象为空
    public String getGirlName(Boy boy) {
        return boy.getGirl().getName();
    }

    //优化
    public String getGirlName02(Boy boy) {
        Optional<Boy> boyOptional = Optional.ofNullable(boy);
        //此时 boy1 一定是非空
        Boy boy1 = boyOptional.orElse(new Boy(new Girl("吴梦淇")));
        Girl girl = boy1.getGirl();
        Optional<Girl> girlOptional = Optional.ofNullable(girl);
        //girl1 一定是非空
        Girl girl1 = girlOptional.orElse(new Girl("吴梦淇"));
        return girl1.getName();
    }
}
```

