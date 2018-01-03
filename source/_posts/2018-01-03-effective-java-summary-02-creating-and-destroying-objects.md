---
title: Effective Java中文版(第2版)读书笔记总结之 二(创建和销毁对象)
date: 2018-01-03 14:52:04
tags: [Java, Effective-Java]
categories:
---

# 二. 创建和销毁对象
## 1. 考虑用静态工厂方法代替构造器
**_优点_**

静态工厂方法与构造器不同的优势在于:

* 它们有名称
* 不必在每次调用它们的时候都创建一个新对象
* 它们可以返回原返回类型的任何子类型的对象

<!-- more -->

<br/>

**_缺点_**

但是, 静态工厂方法也有缺点, 主要在于:
* 类如果不含公有的或者受保护的构造器, 就不能被子类化.(鼓励使用复合, 而不是继承)
* 它们与其它的静态方法实际上没有任何区别.  
(静态工厂方法的一些惯用名称: valueOf, of, getInstance, newInstance, getType 以及 newType)

```java
public static Boolean valueOf(boolean b) {
    return b ? Boolean.TRUE : Boolean.FALSE;
}
```

<br/>

## 2. 遇到多个构造器参数时要考虑用构建器
如果类的构造器或者静态工厂方法中具有多个参数, 设计这种类时, Builder模式就是种不错的选择.

Builder模式模拟了具名的可选参数, 就像Ada和Python中的一样.

```java
public static NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        // Required paramters
        private final int servingSize;
        private final int servings;

        // Optional paramters - initialized to default values
        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        public Builder calories(int val) {
            calories = val;
            return this;
        }

        public Builder fat(int val) {
            fat = val;
            return this;
        }

        public Builder sodium(int val) {
            sodium = val;
            return this;
        }

        public Builder carbohydrate(int val) {
            carbohydrate = val;
            return this;
        }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }
}
```

**_调用builder_**

```java
NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
    .calories(100).sodium(35).carbohydrate(27).build();
```

<br/>

## 3. 用私有构造器或者枚举类型强化 _Singleton_ 属性
创建单例有多种的方法:

* **_公有静态成员是个final域_**

```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();

    private Elvis() { ... }
    ...
    public void singASong() { ... }
}
```

有一点要提醒的是: 享有特权的客户端可以借助 _AccessibleObject.setAccessible_ 方法, 通过反射机制调用私有构造器.  
如果需要抵御这种攻击, 可以修改构造器, 让它在被要求创建第二个实例的时候抛出异常.

<br/>

* **_公有的成员是个静态工厂方法_**

```java
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }

    public static Elvis getInstance() {
        return INSTANCE;
    }

    public void singASong() { ... }
}
```

工厂方法的优势之一在于, 它提供了灵活性: 在不改变其中API的前提下, 我们可以改变该类是否应该为 _Singleton_ 的想法.

<br/>

* **_序列化一个Singleton_**

为了维护并保证 _Singleton_, 除了在声明中加上 "_implements Serializable_", 还必须声明所有实例域都是瞬时(_transient_)的, 并提供一个 _readResolve_ 方法.

```java
private Object readResolve() {
    // Return the one true Elvis and let the garbage collector take care of the Elvis impersonator
    return INSTANCE;
}
```

<br/>

* **_单元素的枚举类型, 最好的实现方式_**(Java 1.5)

```java
public enum Elvis() {
    INSTANCE;
    ...
    public void singASong() { ... }
}
```

这种方法在功能上与公有域方法相近, 但是它更加简洁, 无偿地提供了序列化机制, 绝对防止多次实例化, 即使是在面对复杂的序列化或者反射攻击的时候.

单元素的枚举类型已经成为实现 _Singleton_ 的最佳方法.

<br/>

## 4. 通过私有构造器强化不可实例化的能力
用于只包含静态方法和静态域的类.

例如用于:

* 把基本类型的值或者数组类型上的相关方法组织起来. (比如: _java.lang.Math_ 或者 _java.util.Arrays_)
* 把实现特定接口的对象上的静态方法(包括工厂方法)组织起来. (比如: _java.util.Collections_)
* 把 _final_ 类上的方法组织起来, 以取代扩展该类的做法.

**_包含私有构造器_**

```java
public class UtilityClass {
    // Suppress default constructor for noninstantiability
    private UtilityClass() {
        throw new AssertionError();
    }

    ... // Remainder omitted
}
```

<br/>

## 5. 避免创建不必要的对象
* **_重用不可变的对象_**

**_不要这样做:_**

```java
String s = new String("stringette");
```

该语句每次被执行的时候都创建一个新的 _String_ 实例. 参数 "_stringette_" 本身就是一个 _String_ 实例, 如果这种用法是在一个循环中, 或者是在一个被频繁调用的方法中, 就会创建出成千上万不必要的 _String_ 实例.

**_应该这样做:_**

```java
String s = "stringette";
```

这个版本只用了一个 _String_ 实例, 而不是每次执行的时候都创建一个新的实例.

<br/>

* **_使用静态工厂方法要优于构造器_**

比如: Boolean.valueOf(String) 几乎总是优先于构造器 Boolean(String)

<br/>

* **_重用那些已知不会被修改的可变对象_**

**_不要这样做:_**

```java
public class Person {
    private final Date birthDate;
    ...

    public boolean isBabyBoomer() {
        // Unnecessary allocation of expensive object
        Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
        gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
        Date boomStart = gmtCal.getTime();
        gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
        Date boomEnd = gmtCal.getTime();
        return birthDate.compareTo(boomStart) >= 0 && birthDate.compareTo(boomEnd) < 0;
    }
}
```

_isBabyBoomer_ 每次被调用的时候, 都会新建一个 _Calendar_, 一个 _TimeZone_ 和两个 _Date_ 实例, 这是不必要的.

**_应该这样做:_**

```java
public class Person() {
    private final Date birthDate;
    ...
    private static final Date BOOM_START;
    private static final Date BOOM_END;

    static {
        Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
        gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
        BOOM_START = gmtCal.getTime();
        gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
        BOOM_END = gmtCal.getTime();
    }

    public boolean isBabyBoomer() {
        return birthDate.compareTo(BOOM_START) >= 0 && birthDate.compareTo(BOOM_END) < 0;
    }
}
```

<br/>

* **_要优先使用基本类型而不是装箱基本类型, 要当心无意识的自动装箱_**

**_不要这样做:_**

```java
// Slow program. Where is the object creation?
public static void main(String[] args) {
    Long sum = 0L;
    for (long i = 0; i < Integer.MAX_VALUE; i++) {
        sum += i;
    }

    System.out.println(sum);
}
```

变量 _sum_ 被声明成 _Long_ 而不是 _long_, 意味着程序构造了大约 2<sup>31</sup> 个多余的 _Long_ 实例.

<br/>

* **_对象池一般来说不是一个好的做法_**

除非池中的对象是非常重量级的, 比如像: 数据库连接池.

<br/>

## 6. 消除过期的对象引用
**_你能找到其中的"内存泄漏"吗?_**

```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_ININTIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_ININTIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        return elements[--size];
    }

    private void ensureCapacity() {
        if (elements.length == size) {
            elements = Arrays.copyOf(elements, 2 * size + 1);
        }
    }
}
```

如果一个栈先是增长, 然后再收缩, 从栈中弹出来的对象将不会被当作垃圾回收. 因为栈内部维护着这些对象的过期引用(指的是永远都不会被解除的引用).

* **_清空对象引用_**

```java
    public pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }

        Object result = elements[--size];
        elements[size] = null;  // Eliminate obsolete reference.
        return result;
    }
```

清空对象引用应该是一种例外, 而不是一种规范行为. 不要过度的去清空每一个对象的引用, 这样做既没必要, 也不是我们所期望的.

只有类是自己管理内存的, 才应该去清空对象引用.

<br/>

* **_缓存中的内存泄漏_**

解决缓存的内存泄漏有几种可能的方案:

使用 _WeakHashMap_. 记住只有当所要缓存项的生命周期, 是由该键的外部引用而不是由值决定时, _WeakHashMap_ 才有用处.

一个更为常见的做法是, 缓存应该时不时地清除掉没用的项. 这项工作可以使用一个后台线程来完成, 或者也可以在给缓存添加新条目的时候顺便进行清理. _LinkedHashMap_ 类利用它的 _removeEldestEntry_ 方法可以很容易地实现后一种方案.

<br/>

* **_监听器和回调中的内存泄漏_**

若客户端注册了回调, 但却没有显式地取消注册, 就很可能会发生内存泄漏.

解决这个问题的方法是, 只保存它们的弱引用(_weak reference_). 例如, 只将它们保存成 _WeakHashMap_ 中的键.

<br/>

* **_时不时的使用 Heap Profiler 工具去发现不可见的内存泄漏问题_**

<br/>

## 7. 避免使用终结方法
终结方法通常是不可预测的, 也是很危险的, 一般情况下是不必要的.

* **_注重时间的任务不应该由终结方法来完成_**

    > 不能保证终结方法会被及时地执行.

* **_不应该依赖终结方法来更新重要的持久状态_**

    > _Java_ 语言规范不仅不保证终结方法会被及时执行, 而且根本就不保证它们会被执行.

* 如果异常发生在终结方法之中, 甚至连警告都不会打印出来.

* 使用终结方法会有非常严重的性能损失.

<br/>

**_解决方案_**:

提供一个显式的终止方法, 比如像: _InputStream_, _OutputStream_ 和 _java.sql.Connection_ 上的 _close_ 方法.

显式的终止方法通常与 _try-finally_ 结构结合起来使用, 以确保及时终止.

```java
Foo foo = new Foo(...);
try {
    // Do what must be done with foo
    ...
} finally {
    foo.terminate();  // Explicit termination method
}
```

<br/>

**终结方法有两个合理的用途:**

* 当对象的所有者忘记调用前面建议的显式终止方法时, 终结方法可以充当"安全网".  
(如果你正考虑编写这样的安全网终结方法, 就要认真考虑清楚, 这种额外的保护是否值得你付出这份额外的代价)

* 在本地对等体(_native peer_)中使用, 因为垃圾回收器不会知道它们.  
(在本地对等体并不拥有关键资源的前提下, 终结方法正是执行这项任务最合适的工具. 如果本地对等体拥有必须被及时终止的资源, 那么该类就应该具有一个显式的终止方法)

在以上很少见的情况下, 既然使用了终结方法, 就要记住调用 _super.finalize_

<br/>
