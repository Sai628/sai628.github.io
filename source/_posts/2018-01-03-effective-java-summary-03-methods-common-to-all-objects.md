---
title: Effective Java中文版(第2版)读书笔记总结之 三(对于所有对象都通用的方法)
date: 2018-01-03 15:13:49
tags: [Java, Effective-Java]
categories:
---

# 三. 对于所有对象都通用的方法
## 8. 覆盖 _equals_ 时请遵守通用约定
**_以下情况时不要覆盖_**:

* 类的每个实例本质上是唯一的. 例如: _Thread_
* 不关心类是否提供了"逻辑相等"的测试功能. 例如: _java.util.Random_
* 超类已经覆盖了 _equals_, 从超类继承过来的行为对于子类也是合适的. 例如: _Set_, _List_, _Map_
* 类是私有的或者是包级私有的, 可以确定它的 _equals_ 方法永远不会被调用.

<!-- more -->

<br/>

**_以下情况时, 应该覆盖 equals:_**

如果类具有自己特有的"逻辑相等"概念(不同于对象等同的概念), 而且超类还没有覆盖 _equals_ 以实现期望的行为.

<br/>

**_覆盖 equals 方法时, 需实现了等价关系:_**

* 自反性: _x.equals(x)==true_
* 对称性: _x.equals(y)==y.equals(x)_
* 传递性: _x.equals(y)==y.equals(z)==z.equals(x)_
* 一致性: _x.equals(y)==x.equals(y)==x.equals(y)=..._
* 非空性: _x.equals(null)->false_

<br/>

**_实现高质量 equals 方法的决窍:_**

1. 使用 _==_ 操作符, 检查"参数是否为这个对象的引用". (这是为了性能的优化)
2. 使用 _instanceof_ 操作符, 检查"参数是否为正确的类型".
3. 把参数转换成正确的类型.
4. 对于该类中的每个"关键(_significant_)"域, 检查参数中的域是否与该对象中对应的域相匹配.
5. 当你编写完成了 _equals_ 方法之后, 应该问自己三个问题: 它是否是对称的, 传递的, 一致的? (自反性和非空性通常上会自动满足)

```java
@Override
public boolean equals(Object o) {
    if (o == this) {
        return true;
    }

    if (!(o instanceof PhoneNumber)) {
        return false;
    }

    PhoneNumber pn = (PhoneNumber)o;
    return pn.lineNumber == lineNumber
        && pn.prefix == prefix
        && pn.areaCode == areaCode;
}
```

<br/>

**_最后的一些告诫:_**

* 覆盖 _equals_ 时总要覆盖 _hashCode_
* 不要企图让 _equals_ 方法过于智能(简单才是你的朋友)
* 不是将 _equals_ 声明中的 _Object_ 对象替换为其它的类型

<br/>

## 9. 覆盖 _equals_ 时总要覆盖 _hashCode_
**_hashCode 的通用约定:_**

* 在应用程序的执行期间, 只要对象的 _equals_ 方法的比较操作所用到的信息没有被修改, 那么对这同一个对象调用多次, _hashCode_ 方法都必须始终如一地返回同一个整数. 在同一个应用程序的多次执行过程中, 每次执行所返回的整数可以不一致.

* 如果两个对象根据 _equals(Object)_ 方法比较是相等的, 那么调用这两个对象中任意一个对象的 _hashCode_ 方法都必须产生同样的整数结果.

* 如果两个对象根据 _equals(Object)_ 方法比较是不相等的, 那么调用这两个对象中任意一个对象的 _hashCode_ 方法, 则不一定要产生不同的整数结果. 但是程序员应该知道, 给不相等的对象产生截然不同的整数结果, 有可能提高散列表(_hash table_)的性能.

<br/>

**_覆盖 hashCode 方法的决窍:_**

1. 把某个非零的常数值, 比如: 17, 保存在一个名为 _result_ 的 _int_ 类型的变量中.
2. 对于对象中每个关键域 _f_ (指 _equals_ 方法中涉及的每个域), 完成以下步骤:

    a. 为该域计算 _int_ 类型的散列码 _c_:

        i. boolean 类型: (f ? 1 : 0)

        ii. byte, char, short 或者 int 类型: (int)f

        iii. long 类型: (int)(f ^ (f >>> 32))

        iv. float 类型: Float.floatToIntBits(f)

        v. double 类型: Double.doubleToLongBits(f), 然后按照步骤 2.a.iii, 为得到的 long 类型值计算散列值

        vi. 对象引用: 如果该类的 equals 方法通过递归地调用 equals 的方式来比较这个域, 则同样为这个域递归地调用 hashCode.  
        如果这个域的值为 null, 则返回 0(或者其它某个常数, 但通常为 0)

        vii. array 类型: 把每一个元素当做单独的域来处理. 递归地应用上述规则, 对每个重要的元素计算一个散列码, 
        然后根据步骤 2.b 中的做法把这些散列值组合起来.  
        如果数组域中的每个元素都很重要, 可以利用 Java 1.5 中新增的 Arrays.hashCode 方法.

    b. 按照下面的公式, 把步骤 2.a 中计算得到的散列码 _c_ 合并到 _result_ 中:

        result = 31 * result + c;

3. 返回 _result_.
4. 问问自己 "相等的实例是否都具有相等的散列码?".

```java
// Lazily initialized, cached hashCode
private volatile int hashCode;

@Override
public int hashCode() {
    int result = hashCode;
    if (result == 0) {
        result = 17;
        result = 31 * result + areaCode;
        result = 31 * result + prefix;
        result = 31 * result + lineNumber;
    }
    return result;
}
```

<br/>

**_需要注意的是:_**

* 在散列码的计算过程中, 可以把冗余域排除在外. (必须排除 _equals_ 比较计算中没有用到的任何域)
* 不要试图从散列码计算中排除掉一个对象的关键部分来提高性能.

<br/>

## 10. 始终要覆盖 _toString_
提供好的 _toString_ 实现可以使类用起来更加舒适.

在实际应用中, _toString_ 方法应该返回对象中包含的所有值得关注的信息.

无论你是否决定指定格式, 都应该在文档中明确地表明你的意图.

无论是否指定格式, 都为 _toString_ 返回值中包含的所有信息, 提供一种编程式的访问途径, 使得对象的使用者不需要自己去解析这些字符串. 例如: _PhoneNumber_ 类应该包含针对 _areaCode_, _prefix_ 和 _lineNumber_ 的访问方法.

<br/>

## 11. 谨慎地覆盖 _clone_
_Cloneable_ 接口并没有包含任何方法. 如果一个类实现了 _Cloneable_ 接口, _Object_ 的 _clone_ 方法就会返回该对象的逐域拷贝, 否则就会抛出 _CloneNotSupportedException_ 异常.

如果你覆盖了非 _final_ 类中的 _clone_ 方法, 则应该返回一个通过调用 _super.clone_ 而得到的对象. 对于实现了 _Cloneable_ 的类, 我们总是期望它也提供一个功能适当的公有的 _clone_ 方法.

<br/>

如果对象中 **没有包含** 可变对象的域, 可以使用简单的 _clone_ 的实现:

```java
@Override
public PhoneNumber clone() {
    try {
        // PhoneNumber.clone must cast the result of super.clone() before returning it.
        return (PhoneNumber)super.clone();
    } catch (CloneNotSupportedException e) {
        throw new AssertionError();  // Can't happen
    }
}
```

<br/>

但如果对象中 **包含了** 可变对象的域, 我们就需要另一种解决方案了. 因为可变域会指向内存中相同的对象, 原始的实例与被克隆的实例将会共享这些对象.

_clone_ 方法就是另一个构造器, 你必须确保它不会伤害到原始的对象, 并确保正确地创建被克隆对象中的约束条件.

在可变域对象上递归地调用 _clone_ 是最容易的做法:

```java
@Override
public Stack clone() {
    try {
        Stack result = (Stack)super.clone();
        // From Java 1.5, don't need casting when cloning arrays.
        result.elements = elements.clone();
        return result;
    } catch (CloneNotSupportedException e) {
        throw new AssertionError();
    }
}
```

<br/>

_clone_ 架构与引用可变对象的 _final_ 域的正常用法是不相兼容的, 除非在原始对象和克隆之间可以安全地共享此可变对象.

对于更复杂的对象的克隆, 有时递归地调用 _clone_ 是不够的, 还需要一些特别的方法.  
例如:

* 对于被克隆对象中的对象数组域, 很可能需要进行"深度拷贝". 对于容易导致栈溢出的调用, 可以在 _deepCopy_ 中用迭代代替递归.

* 使用另一种方式去完成克隆操作: 先调用 _super.clone_, 然后把结果对象中的所有域都设置成它们的空白状态, 然后调用高层的方法来重新产生对象的状态.

<br/>

如同构造器一样, _clone_ 方法不应该在构造的过程中, 调用新对象中任何非 _final_ 的方法.

_Object_ 的 _clone_ 方法被声明为可抛出 _CloneNotSupportedException_ 异常,  
但是, 覆盖版本的 _clone_ 方法可能会忽略这个声明.  
公有的 _clone_ 方法应该省略这个声明.

如果专门为了继承而设计的类覆盖了 _clone_ 方法, 覆盖版本的 _clone_ 方法就应该模拟 _Object.clone_ 的行为:

* 它应该被声明为 _protected_;
* 它应该被声明为抛出 _CloneNotSupportedException_ 异常;
* 它 **不应该** 实现 _Cloneable_ 接口.

这样可使得子类具有实现或不实现 _Cloneable_ 接口的自由, 就仿佛它们直接扩展了 _Object_ 一样.

<br/>

值得注意的是:

> 如果你决定用线程安全的类实现 _Cloneable_ 接口, 要记得它的 _clone_ 方法必须得到很好的同步.

简而言之, 实现了 _Cloneable_ 接口的类, 都应该像以下步骤这样创建一个方法:

* 用一个公有的方法覆盖 _clone_;
* 返回的对象类型是当前的类;
* 首先调用 _super.clone_ 方法;
* 然后修改任何需要修正的域.

<br/>

最好提供某些其它的途径来代替对象拷贝, 或者干脆不提供这样的功能.

**_拷贝构造器_**

```java
public Yum(Yum yum);
```

**_拷贝工厂_**

```java
public static Yum newInstance(Yum yum);
```

拷贝构造器的做法, 及其静态工厂方法的变形, 都比 _Cloneable/clone_ 的方式具有更多的优势:

* 不依赖于某一种很有风险的, 语言之外的对象创建机制;
* 不要求遵守尚未制定好文档的规范;
* 不会与 _final_ 域的正常使用发生冲突;
* 不会抛出不必要的受检异常;
* 不需要进行类型转换.

除此之外, 基于接口的拷贝构造器和拷贝工厂(更准确的应叫 "转换构造器" 和 "转换工厂"), 允许客户选择拷贝的实现类型

```java
public HashSet(Set set) -> TreeSet;
```

<br/>

## 12. 考虑实现 _Comparable_ 接口
_Comparable_ 是一个接口, 它并没有在 _Object_ 中声明.

为实现 _Comparable_ 接口的对象数组进行排序可以简单地使用: _Arrays.sort(a);_

一旦类实现了 _Comparable_ 接口, 它就可以跟许多泛型算法以及依赖于该接口的集合实现进行协作. 你付出很小的努力就可以获得非常强大的功能.

_compareTo_ 方法需遵循以下的约定(自反性, 对称性, 传递性)

* if a > b then b < a;  if a == b then b == a;  if a < b then b > a;
* if a > b and b > c then a > c;
* if a == b and b == c then a == c;
* 强烈建议: (x.compareTo(y) == 0) == (x.equals(y))

<br/>

比较整数型基本类型的域, 可以使用关系操作符 < 和 >. 对于浮点域, 需使用 _Double.compare_ 或者 _Float.compare_. 对于数组域, 则要把这些指导原则应用到每个元素上.

如果一个类有多个关键域, 那么, 按什么样的顺序来比较这些域是非常关键的. 你必须从最关键的域开始, 逐步进行到所有的重要域.

```java
public int compareTo(PhoneNumber pn) {
    // Compare area codes
    if (areaCode < pn.areaCode) {
        return -1;
    }
    if (areaCode > pn.areaCode) {
        return 1;
    }

    // Area codes are equal, compare prefixes
    if (prefix < pn.prefix) {
        return -1;
    }
    if (prefix > pn.prefix) {
        return 1;
    }

    // Area codes and prefixes are equal, compare line numbers
    if (lineNumber < pn.lineNumber) {
        return -1;
    }
    if (lineNumber > pn.lineNumber) {
        return 1;
    }

    return 0;  // All fields are equal
}
```

<br/>
