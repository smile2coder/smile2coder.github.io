---
title: Lombok-你的开发利器
top: false
cover: false
toc: true
mathjax: false
date: 2020-01-16 00:47:23
password:
summary: Lombok-你的开发利器
tags: lombok
categories: lombok
---
本文的代码在[这里](https://github.com/smile2coder/springboot-collection)，希望收到一个star，感谢支持
## 前提
Lombok项目是一个Java库，它会自动插入您的编辑器和构建工具中，从而为您的Java增光添彩。 永远不要再写另一个getter或equals方法，带有一个注释的您的类有一个功能全面的生成器，自动化您的日志记录变量等等。但是Lombok在使用过程中也有许多值得注意的点，如果不清楚的话，可能会给代码调试带来意想不到的后果！！！

## 安装
从[Lombok](https://projectlombok.org/)的官网中可以了解到，目前Lombok插件支持Eclipse，MyEclipse，IntelliJ IDEA, Netbeans等主流开发工具。当然，我们也可以在开发工具中直接获取该插件。 

## 引入依赖（Maven）

```
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```

## 这个要怎么玩

重点来了，这么好的工具要怎么用？且听我慢慢道来...

#### @Getter/@Setter
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
@Getter/@Setter可以修饰在成员变量和类上。修饰在成员变量上时，会生成该变量的getter()和setter()方法；修饰在类上时，会生成该类中所有的非静态成员变量生成getter()和setter()方法。我们还可以为getter()和setter()方法设置访问级别。当然我们需要的就是public级别，而这个也恰好是默认的。

```
@Getter
//@Setter(value = AccessLevel.PROTECTED)
@Setter
public class GetterSetterDemo {
    String name;
    int age;
}
```
以上Lombok写法，和下面的写法是等价的
```
public class GetterSetterDemo {
    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```
#### @NoArgsConstructor, @RequiredArgsConstructor and @AllArgsConstructor
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
这三个注解都是类注解。      @NoArgsConstructor会生成一个无参构造器。值得注意的是，在Java中类没有构造器，会默认生成一个无参构造器，那么@NoArgsConstructor还有什么作用呢？这个后续会解释道。

```
@NoArgsConstructor
public class NoArgsConstructorDemo {
}
```
以上Lombok写法，和下面的写法是等价的
```
public class NoArgsConstructorDemo {
}
```
@RequiredArgsConstructor会生成一个带参构造器，这个参数包括final或者@NonNull修饰的成员变量。**值得注意的是，如果@NonNull修饰的参数为null时，会在构造器中抛出NullPointerException异常。**
```
@RequiredArgsConstructor
public class RequiredArgsConstructorDemo {
    private final String name;
    private int age;
    @NonNull private String house;

    public static void main(String[] args) {
        RequiredArgsConstructorDemo requiredArgsConstructorDemo = new RequiredArgsConstructorDemo("smile2coder", "big house");
    }
}
```
以上Lombok写法，和下面的写法是等价的
```
public class RequiredArgsConstructorDemo {
    private final String name;
    private int age;
    private String house;

    public RequiredArgsConstructorDemo(String name, String house) {
        if (house == null) {
            throw new NullPointerException("house is marked non-null but is null");
        }
        this.name = name;
        this.house = house;
    }

    public static void main(String[] args) {
        RequiredArgsConstructorDemo requiredArgsConstructorDemo = new RequiredArgsConstructorDemo("smile2coder", "big house");
    }
}
```
@AllArgsConstructor会生成一个全参的构造器
```
@AllArgsConstructor
public class AllArgsConstructorDemo {
    private String name;
    private int age;
}
```
以上Lombok写法，和下面的写法是等价的
```
public class AllArgsConstructorDemo {
    private String name;
    private int age;

    public AllArgsConstructorDemo(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```
有个点需要注意，使用@RequiredArgsConstructor和@AllArgsConstructor生成构造器，则不会再生成默认的无参构造器。而在许多框架中需要用到无参构造器，这时就需要配合@NoArgsConstructor使用啦。

使用这三个注解还可以生成静态工厂类

```
@RequiredArgsConstructor(staticName = "of")
public class StaticNameDemo {
    private final String name;
    private int age;

    public static void main(String[] args) {
        StaticNameDemo smile2coder = StaticNameDemo.of("smile2coder");
    }
}

```
以上Lombok写法，和下面的写法是等价的
```
public class StaticNameDemo {
    private final String name;
    private int age;

    public StaticNameDemo(String name) {
        this.name = name;
    }

    public static StaticNameDemo of(String name) {
        return new StaticNameDemo(name);
    }

    public static void main(String[] args) {
        StaticNameDemo smile2coder = StaticNameDemo.of("smile2coder");
    }
}
```


#### @Data
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
@Data恐怕是我们用到最多的啦，那么它的作用是什么呢？当@Data修饰在类上时，相当于@Getter, @Setter, @ToString, @EqualsAndHashCode和@RequiredArgsConstructor的功能联合体。让我们简单中寻找更简单的方式。
#### @Builder
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
构建者模式，一种一步步构建复杂类的设计模式。当不用Lombok时，我们来看一下怎么写

```
public class BuilderDemo {
    private String name;
    private int age;
    public BuilderDemo(String name, int age) {
        this.name = name;
        this.age = age;
    }
    public static BuilderDemoBuilder builder() {
        return new BuilderDemoBuilder();
    }

    public static class BuilderDemoBuilder {
        private String name;
        private int age;
        public BuilderDemoBuilder name(String name) {
            this.name = name;
            return this;
        }
        public BuilderDemoBuilder age(int age) {
            this.age = age;
            return this;
        }
        public BuilderDemo build() {
            return new BuilderDemo(name, age);
        }
    }

    public static void main(String[] args) {
        BuilderDemo smile2coder = BuilderDemo.builder().name("smile2coder").age(11).build();
        System.out.println(smile2coder);
    }
}
```
这还不是一个复杂的类，写起来都这么复杂了...如果是一个复杂的类，保守估计都要2根头发的代价。我们来看一下Lombok怎么写

```
@Builder
public class BuilderDemo {
    private String name;
    private int age;

    public static void main(String[] args) {
        BuilderDemo smile2coder = BuilderDemo.builder().name("smile2coder").age(11).build();
    }
}
```
**世界都安静了...**

#### @Log
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
@Log注解也是我比较常用的。日志是常见的开发手段，没有日志的代码是没有灵魂的。
让我们来看看以前是怎么写的

```
public class LogDemo {
    private static Logger log = LoggerFactory.getLogger(LogDemo.class);
    public static void main(String[] args) {
        log.info("smile2coder");
    }
}
```
有人说这不是很简单吗？看一下Lombok再说吧

```
@Log
public class LogDemo {
    public static void main(String[] args) {
        log.info("smile2coder");
    }
}
```
#### @Cleanup
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
@Cleanup可以用来确保在代码执行路径退出当前作用域之前自动清除给定的资源。默认情况下，退出当前作用域前会调用资源的close()方法，当然，你也可以指定一个无参方法。
**值得注意的是，当close()方法中包含参数的话，此方法不会被自动调用**

```
public class CleanupDemo {

    public static void main(String[] args) throws IOException {
        @Cleanup InputStream inputStream = new FileInputStream(new File("/home/smile2coder"));
        //指定一个无参方法
        @Cleanup("clean") CleanupDemo cleanupDemo = new CleanupDemo();
    }

    public void clean() {
        System.out.println("clean");
    }
}
```
以上Lombok写法，和下面的写法是等价的，是不是简单了很多
```
public class CleanupDemo {

    public static void main(String[] args) throws IOException {
        InputStream inputStream = null;
        try {
            inputStream = new FileInputStream(new File("/home/smile2coder"));
        } finally {
            if(inputStream != null) {
                inputStream.close();
            }
        }

        CleanupDemo cleanupDemo = null;
        try {
            cleanupDemo = new CleanupDemo();
        } finally {
            if(cleanupDemo != null) {
                cleanupDemo.clean();
            }
        }
    }

    public void clean() {
        System.out.println("clean");
    }
}
```
#### @NonNull
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
@NonNull可以在方法的参数和构造器的参数上为你生成一个“空值”判断代码块。在方法中，代码块将会插入在方法顶部；在构造器中，代码块将会在任何显式this()或者 super()方法调用后插入。
**值得注意的是，当方法中已经有“空值”判断代码块，@NonNull不会再生成代码。**

```
public class NonNullDemo {
    @NonNull private String name;

    public NonNullDemo(@NonNull String name) {
        this.name = name;
    }
    public void demo(@NonNull String str) {
        this.demo(str, "smile2coder");
    }

    public void demo(@NonNull String str1, String str2) {
        System.out.println(str1 + " " + str2);
    }

    public static void main(String[] args) {
        NonNullDemo nonNullDemo = new NonNullDemo("smile2coder");
//        nonNullDemo.demo("hello");
        nonNullDemo.demo(null);
    }
}
```
以上Lombok写法，和下面的写法是等价的
```
public class NonNullDemo {
    @NonNull private String name;

    public NonNullDemo(@NonNull String name) {
        if (name == null) {
            throw new NullPointerException("name is marked non-null but is null");
        }
        this.name = name;
    }
    public void demo(String str) {
        this.demo(str, "smile2coder");
        if(str == null) {
            throw new NullPointerException("str is marked non-null but is null");
        }
    }

    public void demo(String str1, String str2) {
        System.out.println(str1 + " " + str2);
    }

    public static void main(String[] args) {
        NonNullDemo nonNullDemo = new NonNullDemo("smile2coder");
//        nonNullDemo.demo("hello");
        nonNullDemo.demo(null);
    }
}
```
#### @ToString
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
@ToString会为类生成toString()方法。默认情况下，它会打印类的名，成员变量。每个成员变量以“,”分隔。当然，你也可以用以下参数定制打印变量。  
- boolean includeFieldNames() default true:是否打印变量
- String[] exclude() default {}:不打印的变量列表
- String[] of() default {}:打印的变量列表
- boolean callSuper() default false:是否调用父类toString()方法
- boolean doNotUseGetters() default false:打印是否调用Getter()方法
- boolean onlyExplicitlyIncluded() default false:默认打印非静态变量；当设置为true时，不再自动包含非静态变量

我们还可以用注解的方式定制
- @ToString.Exclude:修饰在成员变量，表示不包含该变量
- @ToString.Include:修饰在成员变量，表示包含该变量（这是默认的）

```
@ToString
public class ToStringDemo {

    private String name;
    private int age;

    @ToString
    static class ExcludeDemo {
        @ToString.Exclude private String name;
        private int age;
    }
    @ToString(exclude = {"name"})
    static class ExcludeArrayDemo {
        private String name;
        private int age;
    }
    @ToString
    static class IncludeDemo {
        @ToString.Include private String name;
        private int age;
    }
    @ToString(of = {"name"})
    static class OfDemo {
        private String name;
        private int age;
    }
    @ToString(includeFieldNames = false)
    static class IncludeFieldNamesDemo {
        private String name;
        private int age;
    }
}
```
以上Lombok写法，和下面的写法是等价的
```
public class ToStringDemo {

    private String name;
    private int age;

    @Override
    public String toString() {
        return "ToStringDemo(" +
                "name='" + name + '\'' +
                ", age=" + age +
                ')';
    }
    static class ExcludeDemo {
        private String name;
        private int age;
        @Override
        public String toString() {
            return "ToStringDemo.ExcludeDemo(" +
                    "age=" + age +
                    ')';
        }
    }
    static class ExcludeArrayDemo {
        private String name;
        private int age;
        @Override
        public String toString() {
            return "ToStringDemo.ExcludeDemo(" +
                    "age=" + age +
                    ')';
        }
    }
    static class IncludeDemo {
        private String name;
        private int age;
        @Override
        public String toString() {
            return "ToStringDemo.IncludeDemo(" +
                    "name='" + name + '\'' +
                    ", age=" + age +
                    ')';
        }
    }
    static class OfDemo {
        private String name;
        private int age;
        @Override
        public String toString() {
            return "ToStringDemo.OfDemo(" +
                    "name='" + name + '\'' +
                    ')';
        }
    }
    static class IncludeFieldNamesDemo {
        private String name;
        private int age;
        @Override
        public String toString() {
            return "ToStringDemo.IncludeFieldNamesDemo()";
        }
    }
}
```
#### @EqualsAndHashCode
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
@EqualsAndHashCode会为类生成equals()和hashCode()方法。用法和@ToString类似

```
@EqualsAndHashCode
public class EqualsAndHashCodeDemo {
    private String name;
    private int age;
}
```
以上Lombok写法，和下面的写法是等价的
```
public class EqualsAndHashCodeDemo {
    private String name;
    private int age;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof EqualsAndHashCodeDemo)) return false;
        EqualsAndHashCodeDemo that = (EqualsAndHashCodeDemo) o;
        return age == that.age &&
                Objects.equals(name, that.name);
    }
    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}
```

#### @Value
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
@Value是@Data的不可变形式；默认情况下，所有变量都设为私有和最终字段，并且不会生成setters。默认情况下，该类本身也将最终确定为final，因为不可改变性不能强加于子类。像@Data一样，还会生成有用的toString（），equals（）和hashCode（）方法，每个字段都有一个getter方法，并且还会生成一个覆盖每个参数的构造函数（除了在字段声明中初始化的最终字段之外） 。
#### @SneakyThrows
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
@SneakyThrows可用于偷偷地抛出已检查的异常，而无需在方法的throws子句中实际声明。**值得注意的是，不建议使用**

```
public class SneakyThrowsDemo {
    @SneakyThrows(UnsupportedEncodingException.class)
    public String utf8ToString(byte[] bytes) {
        return new String(bytes, "UTF-8");
    }

    @SneakyThrows
    public void run() {
        throw new Throwable();
    }
}
```

```
public class SneakyThrowsDemo {
    public String utf8ToString(byte[] bytes) {
        try {
            return new String(bytes, "UTF-8");
        } catch (UnsupportedEncodingException e) {
            throw Lombok.sneakyThrow(e);
        }
    }

    public void run() {
        try {
            throw new Throwable();
        } catch (Throwable t) {
            throw Lombok.sneakyThrow(t);
        }
    }
}
```

#### @Synchronized
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
@Synchronized是同步方法修饰符的更安全的变体。与同步一样，注释只能在静态和实例方法上使用。它的操作类似于synced关键字，但是它锁定在不同的对象上。关键字对此进行了锁定，但注解锁定了一个名为$ lock的私有字段。 如果该字段不存在，则会为您创建。如果注释静态方法，则注释将锁定在名为$ LOCK的静态字段上。当然，你也可以指定锁。

```
public class SynchronizedDemo {
    private final Object readLock = new Object();

    @Synchronized
    public static void hello() {
        System.out.println("smile2coder");
    }
    @Synchronized
    public int answerToLife() {
        return 42;
    }
    @Synchronized("readLock")
    public void foo() {
        System.out.println("smile2coder");
    }
}
```
以上Lombok写法，和下面的写法是等价的
```
public class SynchronizedDemo {
    private static final Object $LOCK = new Object[0];
    private final Object $lock = new Object[0];
    private final Object readLock = new Object();

    public static void hello() {
        synchronized($LOCK) {
            System.out.println("smile2coder");
        }
    }
    public int answerToLife() {
        synchronized($lock) {
            return 42;
        }
    }
    public void foo() {
        synchronized(readLock) {
            System.out.println("smile2coder");
        }
    }
}
```
#### val  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 我们可以使用val作为本地变量的类型来代替该变量的真正的类型，该本地变量也会被修饰为final。val只能用在本地变量和循环遍历中的变量。 **值得注意的是，val不能在NetBeans中正确的使用。**

```
public class ValDemo {
    public void demo() {
        val string = "hello springboot";
        System.out.println(string);

        val list = new ArrayList<String>();
        list.add("smile2coder");
        list.forEach(e -> {
            System.out.println(e);
        });

        val map = new HashMap<String, String>();
        map.put("smile2coder", "hello springboot");
        System.out.println(map.get("smile2coder"));
    }
}
```
以上Lombok写法，和下面的写法是等价的
```
public class ValDemo {
    public void demo() {
        final String string = "hello springboot";
        System.out.println(string);

        final List<String> list = new ArrayList<>();
        list.add("smile2coder");
        list.forEach(e -> {
            System.out.println(e);
        });
        
        final Map<String, String> map = new HashMap<>();
        map.put("smile2coder", "hello springboot");
        System.out.println(map.get("smile2coder"));
    }
}
```

#### var
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;var的用法和val的用法基本一致，除了变量不会被修饰为final。 
**值得注意的是，虽然变量不会被修饰为final，但是已经确定的类型是不会再改变的。** 例如

```
var x = "smile2coder"; 
x = 1; //x已经确定为java.lang.String类型，当x再被赋值为int类型时，编译器会提示错误
```
## 总结
以上就是Lombok的全部内容，相信使用后会大大提高编码效率，目前许多优秀的开源软件都在使用，还等什么呢。
初来乍到，请多指教，本文中有什么错误，请联系我修改，十分感谢

本文的代码在[这里](https://github.com/smile2coder/springboot-collection)，希望收到一个star，感谢支持