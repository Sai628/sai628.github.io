---
title: Android 编码规范
date: 2017-01-19 14:39:47
tags: [Android, standard]
categories:
---


> 无规矩，不成方圆。

<br/>

# 0x00 源文件基础

## 文件名
源文件以最顶层的类名来命名, 大小写敏感.
比如: `LoginActivity.java` `MainActivity.java`


## 文件编码
UTF-8

<!-- more -->

<br/>

# 0x01 命名

> 简洁明了, 但慎用缩写, 除非约定俗定.
> 尽量使用常用英文单词, 而非中文拼音.


## 包(package)
包名全部使用小写, 按文件的功能作用来组织不同的包

```java
com.[domain].[appname].activity.account
com.[domain].[appname].activity.main

com.[domain].[appname].config

com.[domain].[appname].service.db
com.[domain].[appname].service.network

com.[domain].[appname].util
com.[domain].[appname].view
...
```


## 类(class)
__类名采用 [大驼峰法](http://baike.baidu.com/link?url=S3f2dkN7rbxkebQtoRFIi8M6yHvACTC1wiahDLDLpzxCdvI1W4cQ0o3WQ6YjBavWT5LjN-RzTyygGNIiYBKBLa)__.

- 对于继承自 Android 组件的类来说, 命名应以该组件的名称结尾.
- __模型实体类可直接使用名词或名词组合, 不需在后面添加类似 `Model` `Entity` 等后缀__.
- 工具类使用 `XXXUtil` 形式的命名, 辅助帮助类使用 `XXXHelper`, 管理类使用 `XXXManager`

```java
public class LoginActivity
public class ChooseImageActivity
public class EditUserLocationFragment
public class BaseNetService
public class DownloadReceiver

public class CustomMenuDialog
public class NewsDetailAdapter

public class UserInfo
public class Books
public class FavouriteHistory

public class StringUtil
public class ImageUtil
public class NetHelper
public class AnimationHelper
...
```


## 接口(interface)
__接口采用 [大驼峰法](http://baike.baidu.com/link?url=S3f2dkN7rbxkebQtoRFIi8M6yHvACTC1wiahDLDLpzxCdvI1W4cQ0o3WQ6YjBavWT5LjN-RzTyygGNIiYBKBLa)__.

- 对于一般的接口, 在接口名中添加 `Callback` `able` `ible` 等后缀.
- 对于自定义的UI控件监听类的接口, 使用类似 `OnXXXListener` `ClassNameOnXXXListener` 等形式来命名.

```java
public interface NetCallback
public interface UploadCallback
public interface Runnable

public interface OnRefreshListener
public interface ScrollerOnTouchListener
...
```


## 方法(methods)
__方法采用 [小驼峰法](http://baike.baidu.com/link?url=S3f2dkN7rbxkebQtoRFIi8M6yHvACTC1wiahDLDLpzxCdvI1W4cQ0o3WQ6YjBavWT5LjN-RzTyygGNIiYBKBLa)__.

开头一般为动词或动名词, 不要使用名词. 因为一个方法一般都为一个 `动作` 或者 `操作`.

方法名|说明
:---|:---
initXXX() | 与初始化相关
isXXX() checkXXX() | 返回值为 `boolean` 型的方法
getXXX() | 返回某个值
handleXXX() | 对数据进行处理
saveXXX() | 与数据保存相关
resetXXX() | 与数据重置相关
clearXXX() removeXXX() | 与数据清除相关
... | ...


## 变量(variables)
__变量采用 [小驼峰法](http://baike.baidu.com/link?url=S3f2dkN7rbxkebQtoRFIi8M6yHvACTC1wiahDLDLpzxCdvI1W4cQ0o3WQ6YjBavWT5LjN-RzTyygGNIiYBKBLa)__.

Good|Bad
:---|:---
String url | String URL
String name | String nameString
`ArrayList<User>` users | `ArrayList<User>` userArrayList

> 在变量中__不要使用__ `m` `s` 等前缀, 比如:
> `private mName  // 不要这样做`
> `private List<NoticeInfo> mInfos  // 不要这样做`


## 常量(Constants)
__常量命名使用大写字母加 `_` 符的方式__. 而且常量开头的单词, 最好应说明其类型或用途.

```java
public static final String CONFIG_WEIXIN_APPID = "abcdefg"
public static final String API_DOMAIN = "http://api.domain.com"

public static final int LOCATION_TYPE_HOME = 0
```

> 这里值得一提的是, 在 Activity 间跳转传参时, 人们往往习惯性的在 `intent.putExtra()` 时直接使用字符串的字面量值去指定其中的 `name`.
> 这时, 你更应该在将要跳转到的 Activity 类中将这些 `name` 声明为公有的常量值. 这可以避免一些不必要的 bug 的产生.

```java
Intent intent = new Intent(this, NewsDetailActivity.class);
intent.putExtra(NewsDetailActivity.UID, uid);
intent.putExtra(NewsDetailActivity.EXTRA_NEWS_DETAIL, newsDetail);
startActivity(intent);
```


## UI控件(widgets)
这里的UI控件命名, 指的是其变量命名. 一般的形式为 `功能/用途` + `控件缩写后缀`.

```java
private Button loginBtn;
private ImageView logoIv;
private TextView versionNameTv;

private ListView resultListView;
private GridView photoGridView;
```

名称 | 缩写 | 用例
:---|:---|:---
Button | Btn | loginBtn
TextView | Tv | nameTv
EditText | Et | passwordEt
ImageView | Iv | logoIv
ImageButton | imageBtn | delImageBtn
LinearLayout | Ll | navigationBarLl
RelativeLayout | Rl | bottomMenuRl
FrameLayout | Fl | photoFl
... | ... | ...

> 对于一些自定义的UI控件, 或者是类名比较长的UI控件的变量命名, 应该慎用缩写.
> 因为前者的缩写让一个新手去阅读代码时, 会不知所措. 而后者往往是因为缩写对各人有异.


## 资源文件(layout/drawable/anim/dimens/colors/strings)
不管是文件名还是资源ID名, 全部采用小写, 以下划线 `_` 分隔各单词.

- Animation 对应的资源文件, 命名为 `anim_xxx`

```
anim_scale_in.xml
anim_alpha_out.xml
anim.popup_enter.xml
```

- ListView/GridView Item 对应的资源文件, 命名为 `listview_item_xxxx`

```
listview_item_subtitle.xml
listview_item_choose_file.xml
gridview_item_subscription.xml
gridview_item_choose_picture.xml
```

- Activity对应的布局资源文件, 命名为 `模块名_类名简称_view`

```
account_login_view.xml
account_register_view.xml
```

- Dialog对话框对应的布局文件, 命名为 `dialog_描述.xml`

```
dialog_with_scroller_view.xml
dialog_with_checkbox_view.xml
```

- 用于某布局中的子布局资源文件, 比如通过 `include` 方式引用的子布局, 命名为 `content_layout_用途`

```
content_layout_empty_prompt.xml
content_layout_input_panel.xml
```

- 自定义的 `style` 名(__注意这里指的非文件名__), 命名为 `my_控件名/用途_style_风格名`

```xml
<style name="my_listview_style_default" parent="@android:style/Widget.ListView">
<style name="my_listview_style_card" parent="@android:style/Widget.ListView">

<style name="my_divide_line_style_horizontal">
<style name="my_divide_line_style_vertical">
```

<br/>

# 0x02 变量声明

> __一行只写一个变量声明__.
> __需要时才声明, 并尽快进行初始化__.

本节没有其它的了.

<br/>

# 0x03 注释

> 能不写注释时, 尽量不要写(写注释就像交朋友, 宁缺勿滥).
> 但必要的注释一定要添加, 像公开的API方法, 复杂算法的大体思路等(就像你不太可能独身地过完这一生).

说到写注释, 你应该更关注 `方法/变量` 的命名. 如果命名适当, 那就没注释什么事了.

以下注释的样式以代码的形式出现, 在进行相应地方的注释撰写时, 格式上参照样例代码的样式.


## 文件头注释

```java
/**
 * @author zhangshan
 * @ClassName: LoginActivity
 * @Description: 登录页面
 * @date Aug 24, 2016 11:22:33 PM
 */
```


## 方法注释
* __public 方法__

```java
/**
 * 文件容量单位转换为可识别的文本.
 * 根据传入的文件大小识别为 B 或 KB 或 MB 显示，型如："12B"、"23.45KB"、"34.56MB"
 *
 * @param  fileSize 要转换的大小，单位为Byte
 * @return String
 */
public static String getHumanSize(long fileSize)
{
    // implement code
}
```

> 另外, 接口 interface 中定义方法很有必要添加注释.

* __private 方法__

```java
// 初始化View
private void initView()
{
    // implement code
}
```
> 注意: 上面的注释其实是多余的!!! 上面的注释其实是多余的!!! 上面的注释其实是多余的!!!
> 方法名 `initView` 已经明示它的作用. 当方法名都无法说明它的作用时, 你应该先考虑一下换个方法命名, 再决定是否添加注释, 更何况这是一个私有的方法.

* __protected 方法__
自行掂量掂量吧


## 常量/变量注释
* __常量__
一般来说, 常量都有添加注释的必要, 特殊是全局变量.

```java
/**
 * 密码最短长度 6
 */
public static final int LIMIT_PASSWORD_MIN_SIZE = 6;
/**
 * 密码最长长度 20
 */
public static final int LIMIT_PASSWORD_MAX_SIZE = 20;
```

* __变量__
变量一般无须添加注释, 而是在命名上见名知义.
但有时当一些变量是作特殊用途时, 此时注释的内容应该是描述它起的作用, 而不是描述变量的字面意思.

```java
private Button loginBtn; // 登陆按钮 (多余的注释, 应该去掉)

private String password; // 密码，本地MD5加密
private String logo; // 用户头像的完整URL
```

> 在上面的样例代码中, loginBtn 由其命名已经知道这是一个登陆的 Button, 再添加注释是多余的, 这会加重代码阅读者的负担.
> 而对于 `password` 与 `logo`, 在某些情况下添加注释是需要的. 像如果这两个字段是属于模型类 `User` 中的字段, 为它们添加注释可以很明确了解后台返回的各字段含义.


## 代码块注释
对于代码块的注释说明, 最好是可以用一行文字在块的前面简要描述该块代码起到的作用.
对于复杂的算法, 可以多写几行, 描述算法的大体思路. 当有相应的资源链接时, 最好是在注释中添加上.

> 当某些代码不再使用到时, 最好的做法是及时删除掉, 而不是注释掉. 时刻保持项目代码的整洁是一样美德.


## TODO标释
当某个功能代码待实现, 或者某个方法有 bug, 或是某处的代码待优化时, 你应该添加上 `// TODO` 标注, 并简单说明还需要做的事情.

```java
private void doSomething()
{
    // TODO 还需要进行网络缓存的优化
    // implement code
}
```

简单起见, 在这里我们统一使用 `// TODO "the message about should do something"` 的标释.

> 对于调试的过程中, 临时性地注释掉某些代码时, 最好也加上 `TODO` 标释, 避免调试完后忘记了打开注释而产生一些奇怪的问题.

<br/>

# 0x04 括号

> 当无法保持简单时, 也不要自我制造复杂.


## 大括号
除了数组的初始化声明外, __对于全部的大括号, 另起一新行, 并且 `{` 与 `}` 应对齐显示.__
一些空白待实现的方法, 其两个大括号也应独立使用两行. (此时你需要在空白实现中添加上 `// TODO` 标释)

```java
private int[] a = new int[]{1, 2, 3};

if (x > 0)
{
    int someVariable = a ? x : y;
}
else if (x < 0)
{
    int someVariable = (y + z);
    someVariable = x = x + y;
}


public void bar()
{
    // TODO wait for implement
}
```

> 对于 `if/else` 语句, 即使只有一行代码, 也应在外层加上大括号. 因为谁也不知道哪天你要扩充这里.


## 小括号
在三目运算符 `? : `, 或者是逻辑运算符 `&&　||`　中, 当它们很长或者涉及其它方法调用时, 应该在其外层添加上小括号. 当然, 如果代码逻辑已经简洁到一目了然时, 你也可以不添加.

```java
if ((a && b) || (c && d))
{
    int someVariable = a ? x : y;
    x = (y >= 0 ? arr[y] : -1);
}
```

> 很多时候, 使用小括号去限定一组逻辑, 这都是你的本职所在.
> 我们最好假设没有一个人能记住整个 java 运算符优先级表.

<br/>

# 0x05 缩进与间隔

> 保持层次感与内聚性.
> 队形很重要.

## 列限制
在IDE中设置一行的列限制为 `130` 个字符长度(当然, 这个数字可根据实际情况适当加减. 但强烈建议在团队中各成员采用一致的数值).任何一行如果超出该限制, 必须自动换行.
__而以下情况时, 可保持为一行__:

1. 很长的URL字符串
2. 注释中那些可能需要被剪切粘贴到 shell 中的命令行
3. 很长的方法声明(若真的出现很长的方法声明, 此时你应该先考虑是否要修改方法的命名)

> 对于很长的链式调用, 可以不必每次调用时都换一行, 在将要超出列限制长度时换行即可.
> 若换行时, 方法调用前的 `.` 字符必须跟在方法的新行中.

```java
doAction1().doAction2().doAction()
           .doAction3().doAction4()
           ......
```


## 缩进
__缩进使用 4 个空格. 不要使用 tab 字符进行缩进. 可以在 IDE 中设置 tab 键为 4 个空格.__
__另外: 对于自行换行时, 新行至少要缩放 `8` 个空格.__

缩进样例:

```java
public class Foo
{
    public int[] X = new int[]{1, 3, 5, 7, 9, 11};


    public void foo(boolean a, int x, int y, int z)
    {
        label1:
        do
        {
            try
            {
                if (x > 0)
                {
                    int someVariable = a ? x : y;
                    int anotherVariable = a ? x : y;
                }
                else if (x < 0)
                {
                    int someVariable = (y + z);
                    someVariable = x = x + y;
                }
                else
                {
                    label2:
                    for (int i = 0; i < 5; i++)
                    {
                        doSomething(i);
                    }
                }

                switch (a)
                {
                    case 0:
                        doCase0();
                        break;

                    default:
                        doDefault();
                }
            }
            catch (Exception e)
            {
                processException(e.getMessage(), x + y, z, a);
            }
            finally
            {
                processFinally();
            }
        } while (true);

        if (2 < 3)
        {
            return;
        }
        if (3 < 4)
        {
            return;
        }

        do
        {
            x++;
        } while (x < 10000);

        while (x < 50000)
        {
            x++;
        }
        for (int i = 0; i < 5; i++)
        {
            System.out.println(i);
        }
    }


    private class InnerClass implements I1, I2
    {
        public void bar() throws E1, E2
        {
        }
    }
}
```

__对于XML资源的缩进__
XML里的元素内没有其他元素时, 应该使用自结束的标签

```xml
<TextView
    android:id="@+id/text_view_username"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" />
```

下面是一个不规范的样例:

```xml
<!-- 不要这样做 -->
<TextView
    android:id="@+id/text_view_profile"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" >
</TextView>
```


## 空格
此处的空格为行代码空格, 指的是一行代码中的各组成元素之间的空格.
包含 `变量/常量名` 与 `关键字`, `参数列表`, `运算符`, `括号`, `逗句` 等等元素.

```java
@Annotation(param1 = "value1", param2 = "value2")
@SuppressWarnings({"ALL"})
public class Foo<T extends Bar & Abba, U>
{
    int[] X = new int[]{1, 3, 5, 6, 7, 87, 1213, 2};
    int[] empty = new int[]{};


    public void foo(int x, int y)
    {
        Runnable r = () -> {};
        Runnable r1 = this::bar;
        for (int i = 0; i < x; i++)
        {
            y += (y ^ 0x123) << 2;
        }
        do
        {
            try (MyResource r1 = getResource(); MyResource r2 = null)
            {
                if (0 < x && x < 10)
                {
                    while (x != y)
                    {
                        x = f(x * 3 + 5);
                    }
                }
                else
                {
                    synchronized (this)
                    {
                        switch (e.getCode())
                        {
                            //...
                        }
                    }
                }
            }
            catch (MyException e)
            {
            }
            finally
            {
                int[] arr = (int[]) g(y);
                x = y >= 0 ? arr[y] : -1;
                Map<String, String> sMap = new HashMap<String, String>();
                Bar.<String, Integer>mess(null);
            }
        } while (true);
    }


    void bar()
    {
        {
            return;
        }
    }
}


class Bar
{
    static <U, T> U mess(T t)
    {
        return null;
    }
}


interface Abba
{
}
```


## 空白行

* 类/接口/方法之间相互空两行  2
* 相关的变量声明之间不空行  0
* 不相关的变量声明之间空一行  1
* 类/接口内的前后不空行  0
* 方法体内的前后不空行  0
* 在一个方法体内, 联系不紧密的逻辑之间空一行 0
* 可适当的在 return 语句前空一行  0/1
* 在XML布局文件中, 控件之间的声明空一行 1.
  但父控件的结束标签与最后的子控件之间不用空行

```java
package com.intellij.samples;

import com.intellij.idea.Main;

import javax.swing.*;

import java.util.Vector;


public class Foo
{
    private int field1;
    private int field2;

    {
        field1 = 2;
    }


    public void foo1()
    {
        new Runnable()
        {
            public void run()
            {
            }
        };
    }


    public int foo2()
    {
        field1 = 3;
        field2 = field1 * field2;

        return field2;
    }


    public class InnerClass
    {
    }
}


class AnotherClass
{
}


interface TestInterface
{
    int MAX = 10;
    int MIN = 1;

    void method1();

    void method2();
}
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    style="@style/my_layout_style_common"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical">

    <RelativeLayout style="@style/my_layout_navigationbar_style_default">

        <ImageView
            android:id="@+id/account_bind_phone_step_one_view_back_imageview"
            style="@style/my_navigationbar_item_back_menu_style"/>

        <TextView
            style="@style/my_navigationbar_title_text_style"
            android:layout_toRightOf="@id/account_bind_phone_step_one_view_back_imageview"/>
    </RelativeLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:gravity="center"/>

        <View style="@style/my_divide_line_style_vertical"/>

        <EditText
            android:id="@+id/account_bind_phone_step_one_view_phonenumber_edittext"
            style="@style/my_edittext_style_singleline_plain"
            android:hint="@string/please_input_your_mobilephone_number"/>
    </LinearLayout>

    <TextView
        android:id="@+id/account_bind_phone_step_one_view_prompt_textview"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:singleLine="false"
        android:textColor="@color/red"
        android:textSize="@dimen/text_fontsize_small"/>

    <Button
        android:id="@+id/account_bind_phone_step_one_view_verifycode_button"
        style="@style/my_button_style_primary"
        android:layout_width="match_parent"
        android:layout_height="@dimen/button_height"
        android:enabled="false"
        android:text="@string/get_verify_code"/>

</LinearLayout>
```

<br/>

# 0x06 顺序与位置

> 先公有, 后私有; 先重写, 后实现; 先一般, 后个性.

以 `Activity` 类文件为例, 按以下顺序进行文件组织:

```java
package 当前包名

import 包名;  // 具体根据IDE自动导入的排序

ClassName extends 父类 implements 接口1, 接口2, ...

public static final `fields`  // 公有类常量, 按使用的先后顺序
private static final `fields`  // 私有类常量, 按使用的先后顺序

UI控件变量  // 具体排序可按照UI的layout文件布局从上到下, 从左到右的顺序
普通成员变量  // 按使用的先后顺序

自定义handler

Lifecycle `methods`	// 按生命周期的先后顺序
Override `methods` ...

initData  // 初始化相关数据方法
initView  // 初始化相关UI方法

// 发起异步任务方法
loadDataByNetwork()
loadDatabaseData()

handleSomethingEvent()  // 事件响应方法
doSomething()  // 普通逻辑方法

listener  // UI控件监听器

custom adapter  // 自定义适配器
custom class  // 自定义内部类

interface 内部接口声明
```

其它的类文件参照该原则进行组织.

> 当一个类有多个构造方法时, 或是多个同名方法时, 这些方法应该按参数从少到多的顺序出现在一起, 切忌分开来写.

<br/>

# 0x07 更多

> 最好不要让这个文档有`更多`了.
> 实现无尽的需求已经够让我们喝一壶的了. 谁还希望在这时有更多的束缚?
