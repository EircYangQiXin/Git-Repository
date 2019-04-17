## Java 8 新特性笔记

### 一、Lambda 表达式概念

​	Java 8 中加入了 Lambda 表达式，Lambda 表达式可以使原本冗余、复杂或难以理解的代码变得更加简洁和容易理解

代码比对：

```java
//Java8 之前 Runnable 类的实现方式
Runnable run1 = new Runnable({
    @Override
    public void run(){
        System.out.println("Java8 之前使用匿名内部类的方式实现run ");
    }
});
run1.start();
//Java 8 中使用Lambda 表达式的实现方式
Runnable run2 = () -> System.out.println("Java 8 中 使用Lambda 表达式");
run2.start();
```

通过上面的对比，可以看出Java 8中使用 Lambda 表达式 可以简写方法 减少重复代码 使代码更易读懂

 使用 Lambda 表达式时，参数列表可以省略参数类型，JVM 会自动通过 “类型判断” 来检测数据类型

比如：

```java
Comparator<Integer> com = (x,y) -> {
			return Integer.compare(x, y);
		}
```

### 二、使用 Lambda 表达式的条件

​	Lambda 表达式的最佳使用方式，是基于函数是接口进行方法的实现。比如：

先声明一个函数式接口,Java 8 中声明函数式接口，可以使用@FunctionalInterface注解来标注

```java
@FunctionalInterface
public interface MyFunction<T>{
	
    T test(T t);

}
```

在对方法中的参数进行处理 ， 如：对字符串进行大写转换

```java
public class LambdaTest{
    
    public String strHandler(String str , MyFunction<String> fun){
        return fun.test(str);
    }
    @Test
    public void test1(){
    	//使用lambda 表达式对 字符串进行大写转换
       String strUpper = strHandler("abcdef",(d) -> d.toUpperCase());
        System.out.println(strUpper);
    }
}
```
使用 Lambda 表达式可以让代码更加简洁易懂

### 三、Java 8 中内置的四大核心函数接口

|               |      |         |            |
| ------------- | ---- | ------- | ---------- |
| 函数名        | 参数 | 返回值  | 说明       |
| Consumer<T>   | T    | void    | 消费型接口 |
| Supplier<T>   | void | T       | 供给型接口 |
| Function<T,R> | T    | R       | 函数型接口 |
| Predicate<T>  | T    | Boolean | 断言型接口 |

使用Java 8 内置函数式接口 可以代替我们大多数使用情况，此外Java8中还有许多的四大核心函数接口实现类，可以用于更复杂的使用情况中
### 四、 方法引用和构造引用
    方法引用 是指，在某些java 的类中已经有了的方法，这样我们不必要去重复写方法的实现 ，在 lambda 方法中直接引用 方法即可

#### 方法引用的方式
    1. 实例名::方法名
    2. 类名::方法名
    3. 类名::静态方法名
1. 类名::方法名
 ```java
        @Test
        public void test3(){
            //之前的 lambda 表达式 表现方式
            BiFunction<Integer, Integer, Integer> function = (x, y) -> x.compareTo(y);
             function.apply(1,2);

            /*
            * 使用方法引用的方式
            */
            BiFunction<Integer,Integer,Integer> fun1 = Integer::compareTo;
            fun1.apply(1,2);
        }
 ```
2. 实例名::方法名
   ```java
        @Test
        public void test4(){
            
            PrintStream ps = System.out;
            Consumer<String> con = (x) -> ps::println;
            con.accept("123")

             /*
            * 与第一点类似
            * 注意 方法的参数类型、返回值类型 要去函数接口中的方法相同
            * 参数自动填入方法中
            * 注意这里的引用的实例 是根据参数的顺序来的，第一个表示引用方法的实例
            * 第二个参数则表示方法的参数 (x,y) -> x.compareTo(y) 的意思
            */
            Consumer<String> con1 = ps::println;
            con.accept("123");
        }
   ```
3. 类名::静态方法名 
    ```java
        @Test
        public void test4(){
            
            //之前的 lambda 表达式 表现方式，compare 是静态方法
            BiFunction<Integer, Integer, Integer> function = (x, y) -> Integer.compare(x, y);
            function.apply(1,2);

            //使用方法引用的方式
            BiFunction<Integer,Integer,Integer> fun1 = Integer::compare;
            fun1.apply(1,2);
        }
    ```
#### 构造引用
  1.自动选择构造方法 
  ```java
    //此处省略getter setter
    public class Employee {
        private int id;
        private String name;
        private int age;
        private double salary;

        public Employee(){

        }
        public Employee(int id){
            this.id = id; 
        }

        public static Employee get(){
            //此处无参数的话，创建的Employee 则为空
            Supplier<Employee> supplier = Employee::new;
            return supplier.get();
        }

        public static Employee get(int id ){
            Function<Integer,Employee> fun = Employee::new;
            return fun.apply(id);
            
        }
        
    }
    
  ```
  2.数组构造
  ```java
    @Test
    public void test5(){
        Function<Integer,String[]> fun = (x) - > new String[x];
        fun.apply(10);

        Function<Integer,String[]> fun1 = String[]::new;
        fun1.apply(20);
    }
  ```

### 五、Optional容器

#### Optional容器的概念和常用方法

 optional容器的出现主要是为了解决每个程序员都会遇到的空指针异常，对空指针也有了个更好的容错率

下面列出几个Optional 中常用的方法

```java
public class Man{
    private String name;
    
    private Integer age;
    // getter setter ....
}
```



of（T t）:构造一个 Optional 实例

```java
@Test
public void test6(){
    //构建一个含有泛型为 Man 的 Optional 实例
    Optional<Man> man  = Optioanl.of(new Man());
    //get() 为获取 Optional 容器中的实例
    Sysytem.out.println(man.get());
    
}
```

empty() : 构建一个为空的 Optional 实例

```java
@Test
public void test7(){
    //构建一个为空的 Optional 实例
	Optional<Man> op = Optional.empty();
    //会出现 NoSuchElementException 异常
    System.out.println(op.get());
    
}
```

ofNullable(T t): 创建一个 类型为T 的 Optional 实例 如若参数为 null 则创建 null 的 Optional 实例

```java
//ofNullable ：源码如下
//实则为 of() 和 empty() 的结合
public static <T> Optional<T> ofNullable(T value) {
    return value == null ? empty() : of(value);
}
```
orElse(T t)：如若 Optional 实例对象为空 则给定默认值 否则不变

```java
public void test8(){
    //构建一个为空的 Optional 实例
	Optional<Man> op = Optional.empty();
    //如果 op 中为空 则接受新的参数
    op.orElse(new Man("zhangsan",18));
}
```

orElseGet(Supplier s)：如若 Optional 实例对象为空 则返回 s
```java
public void test9(){
    //构建一个为空的 Optional 实例
	Optional<Man> op = Optional.empty();
    //如果 op 中为空 则返回参数 s
    Man man = optional.orElseGet(() -> new Man("zhangsan",18) );
}
 
```

ifPresent() : 判断对象是否为空

```java
@Test
public void test10(){
    Optional<Man> op = Optional.empty();
    if(op.ifPresent()){
        System.out.println(op.get());
    }else{
        System.out.println("this is null");
    }
}
```

### 六、Stream流

#### Stream流的使用

 Java 8 中 对 Collection 集合新增了 Stream 流的用法，Stream 流更好的支持了对集合数据的操作

下面列出几点流的使用

 1.创建流的方式

```java
	@Test
	public void test1(){
		//1. Collection 提供了两个方法  stream() 与 parallelStream()
		List<String> list = new ArrayList<>();
		Stream<String> stream = list.stream(); //获取一个顺序流
		Stream<String> parallelStream = list.parallelStream(); //获取一个并行流
		
		//2. 通过 Arrays 中的 stream() 获取一个数组流
		Integer[] nums = new Integer[10];
		Stream<Integer> stream1 = Arrays.stream(nums);
		
		//3. 通过 Stream 类中静态方法 of()
		Stream<Integer> stream2 = Stream.of(1,2,3,4,5,6);
		
		//4. 创建无限流
		//迭代
		Stream<Integer> stream3 = Stream.iterate(0, (x) -> x + 2).limit(10);
		stream3.forEach(System.out::println);
		
		//生成
		Stream<Double> stream4 = Stream.generate(Math::random).limit(2);
		stream4.forEach(System.out::println);
		
	}
```

2. 中间操作

   ```java
   List<Employee> emps = Arrays.asList(
   			new Employee(102, "李四", 59, 6666.66),
   			new Employee(101, "张三", 18, 9999.99),
   			new Employee(103, "王五", 28, 3333.33),
   			new Employee(104, "赵六", 8, 7777.77),
   			new Employee(104, "赵六", 8, 7777.77),
   			new Employee(104, "赵六", 8, 7777.77),
   			new Employee(105, "田七", 38, 5555.55)
   	);
   
   /*
   	  筛选与切片
   		filter——接收 Lambda ， 从流中排除某些元素。
   		limit——截断流，使其元素不超过给定数量。
   		distinct——筛选，通过流所生成元素的 hashCode() 和 equals() 去除重复元素
   	 */
   	
   	//内部迭代：迭代操作 Stream API 内部完成
   	@Test
   	public void test2(){
   		//所有的中间操作不会做任何的处理
   		Stream<Employee> stream = emps.stream()
   			.filter((e) -> {
   				System.out.println("测试中间操作");
   				return e.getAge() <= 35;
   			});
   		
   		//只有当做终止操作时，所有的中间操作会一次性的全部执行，称为“惰性求值”
   		stream.forEach(System.out::println);
   	}
   	@Test
   	public void test5(){
           //skip(n) —— 跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一个空流。与 limit(n) 互补
   		emps.parallelStream()
   			.filter((e) -> e.getSalary() >= 5000)
   			.skip(2)
   			.forEach(System.out::println);
   	}
   	
   	@Test
   	public void test6(){
   		emps.stream()
   			.distinct()
   			.forEach(System.out::println);
   	}
   ```

  map 提取参数

```java
/*
		映射
		map——接收 Lambda ， 将元素转换成其他形式或提取信息。接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素。
		flatMap——接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流
	 */
	@Test
	public void test1(){
		Stream<String> str = emps.stream()
			.map((e) -> e.getName());
		
		System.out.println("-------------------------------------------");
		
		List<String> strList = Arrays.asList("aaa", "bbb", "ccc", "ddd", "eee");
		
		Stream<String> stream = strList.stream()
			   .map(String::toUpperCase);
		
		stream.forEach(System.out::println);
		
		Stream<Stream<Character>> stream2 = strList.stream()
			   .map(TestStreamAPI1::filterCharacter);
		
		stream2.forEach((sm) -> {
			sm.forEach(System.out::println);
		});
		
		System.out.println("---------------------------------------------");
		
		Stream<Character> stream3 = strList.stream()
			   .flatMap(TestStreamAPI1::filterCharacter);
		
		stream3.forEach(System.out::println);
	}

	public static Stream<Character> filterCharacter(String str){
		List<Character> list = new ArrayList<>();
		
		for (Character ch : str.toCharArray()) {
			list.add(ch);
		}
		
		return list.stream();
	}
```

3.终止操作

```java
//3. 终止操作
	/*
		allMatch——检查是否匹配所有元素
		anyMatch——检查是否至少匹配一个元素
		noneMatch——检查是否没有匹配的元素
		findFirst——返回第一个元素
		findAny——返回当前流中的任意元素
		count——返回流中元素的总个数
		max——返回流中最大值
		min——返回流中最小值
	 */
	@Test
	public void test1(){
			boolean bl = emps.stream()
				.allMatch((e) -> e.getStatus().equals(Status.BUSY));
			
			System.out.println(bl);
			
			boolean bl1 = emps.stream()
				.anyMatch((e) -> e.getStatus().equals(Status.BUSY));
			
			System.out.println(bl1);
			
			boolean bl2 = emps.stream()
				.noneMatch((e) -> e.getStatus().equals(Status.BUSY));
			
			System.out.println(bl2);
	}
	
	@Test
	public void test2(){
		Optional<Employee> op = emps.stream()
			.sorted((e1, e2) -> Double.compare(e1.getSalary(), e2.getSalary()))
			.findFirst();
		
		System.out.println(op.get());
		
		System.out.println("--------------------------------");
		
		Optional<Employee> op2 = emps.parallelStream()
			.filter((e) -> e.getStatus().equals(Status.FREE))
			.findAny();
		
		System.out.println(op2.get());
	}
	
	@Test
	public void test3(){
		long count = emps.stream()
						 .filter((e) -> e.getStatus().equals(Status.FREE))
						 .count();
		
		System.out.println(count);
		
		Optional<Double> op = emps.stream()
			.map(Employee::getSalary)
			.max(Double::compare);
		
		System.out.println(op.get());
		
		Optional<Employee> op2 = emps.stream()
			.min((e1, e2) -> Double.compare(e1.getSalary(), e2.getSalary()));
		
		System.out.println(op2.get());
	}
	
	//注意：流进行了终止操作后，不能再次使用
	@Test
	public void test4(){
		Stream<Employee> stream = emps.stream()
		 .filter((e) -> e.getStatus().equals(Status.FREE));
		
		long count = stream.count();
		
		stream.map(Employee::getSalary)
			.max(Double::compare);
	}
}
```

归约

```java
/*
		归约
		reduce(T identity, BinaryOperator) / reduce(BinaryOperator) ——可以将流中元素反复结合起来，得到一个值。
	 */
	@Test
	public void test1(){
		List<Integer> list = Arrays.asList(1,2,3,4,5,6,7,8,9,10);
		
		Integer sum = list.stream()
			.reduce(0, (x, y) -> x + y);
		
		System.out.println(sum);
		
		System.out.println("----------------------------------------");
		
		Optional<Double> op = emps.stream()
			.map(Employee::getSalary)
			.reduce(Double::sum);
		
		System.out.println(op.get());
	}
	//collect——将流转换为其他形式。接收一个 Collector接口的实现，用于给Stream中元素做汇总的方法
	@Test
	public void test3(){
		List<String> list = emps.stream()
			.map(Employee::getName)
			.collect(Collectors.toList());
		
		list.forEach(System.out::println);
		
		System.out.println("----------------------------------");
		
		Set<String> set = emps.stream()
			.map(Employee::getName)
			.collect(Collectors.toSet());
		
		set.forEach(System.out::println);

		System.out.println("----------------------------------");
		
		HashSet<String> hs = emps.stream()
			.map(Employee::getName)
			.collect(Collectors.toCollection(HashSet::new));
		
		hs.forEach(System.out::println);
	}
```

