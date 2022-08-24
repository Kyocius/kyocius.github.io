---
title: Rx.NET 响应式编程指北 Ex - 函数式思想 & 流式接口
date: 2022-08-09
draft: false
slug: rx-magic-ex
categories:
    - 编程
tags:
    - Rx.NET
    - CSharp
    - Functional
image: head.png

---

## 前言

这一章是独立于正式章节的番外，补充一下 C# **函数式编程**（Functional Programming）以及**流式接口**（Fluent API）的知识。

等等，我们不是在学 Rx.NET 吗，怎么又跑到函数式了？ (＃°Д°)

你 先 别 急，二者并不冲突。

Rx.NET 提供的方法大部分都是函数式的，比如我们上一章学到的 `FromEventPattern` 方法，就是一个**高阶函数**（High-Order Function），因为它的参数也是函数。

有些老古董 Java 程序员认为，C# 和 Java 一样都是纯面向对象语言（Pure OOP），这是完全错误的。经过二十年的发展，C# 早已进化成一门多范式语言了。

## 函数式编程

那么如何用 C# 编写函数式代码呢？

回顾一下基础知识吧。

### 委托 Delegate

为了传递函数，C# 老早就加入了委托特性：

```c#
public delegate bool MyDelegateType (string first, string second);
```

在上面的代码中，我们创建了一个名为 `MyDelegateType` 的委托类型。

我们这样为委托赋值：

```c#
MyDelegateType myDel;
myDel += <一些函数>;

myDel("first", "second"); // 这样调用
```

### 匿名方法 Anonymous methods

```c#
myDel = delegate (string first, string second)
{
    return first.Length == second.Length;
}
```

### Lambda 表达式

```c#
// 👇 这就是 lambda
MyDelegateType x = (s1,s2) => s1 == s2; 

// 👇 匿名函数
MyDelegateType y = delegate (string s1,string s2) { return s1 == s2; }; 
```

### Func 和 Action

这俩都是 .NET 预定义好的标准委托类型。

也就是说别傻乎乎地自己定义委托类型啦，用它俩就好了。

.NET 预定义了 17 种 Func 和 Action 委托，全放在 `System` 名称空间下。

#### Action

Action 用于**无返回值**的函数委托。

看看定义：

```c#
// 👇 无参
public delegate void Action();
// 👇 两个参数
public delegate void Action<in T1, in T2>(T1 arg1, T2 arg2);
```

简单使用：

```c#
public static void ForEach<T>(IEnumerable<T> collection, Action<T> action)
{
    foreach (var item in collection)
    {
        action(item);
    }
}
// 👇 完整使用
ForEach(new[] { "a", "b", "c" }, n => Console.WriteLine(n));
// 👇 省略形式
ForEach(new[] { "a", "b", "c" }, Console.WriteLine);
```

#### Func

Func 用于**有返回值**的函数委托。

看看定义：

```c#
public delegate TResult Func<out TResult>(); 
public delegate TResult Func<in T1,…,T16, out TResult>(T1 arg,…,T16 arg16);
```

简单使用：

```c#
public static void ForEach<T>(IEnumerable<T> collection, Action<T> action,
 Func<T, bool> predicate) 
{
     foreach (var item in collection)
     {
         if (predicate(item)) 
         {
             action(item);
         }
     }
}
```

### 懒加载 `Lazy<T>`

我们来看看 Func 作为工厂函数的作用吧。

你有一个很耗时的类：

```c#
class HeavyClass 
{
    // 初始化非常耗时
}
```

你还有个类：

```c#
class ThinClass
{
    private HeavyClass _heavy;
    public HeavyClass TheHeavy
    {
        get 
        {
            if (_heavy == null) 
            { 
                _heavy = new HeavyClass(); // 延迟加载时间
            } 
            return _heavy;
        }
    }
    public void SomeMethod()
    {
        var myHeavy = TheHeavy; 
        // 省略后面的使用
    }
} 
```

我们可以看到这就是在 Java 中常见的构建模式。

那如果你有 10 个需要延迟加载的属性呢？像这样一一手写 `get` 方法未免也太折磨了。

太笨了，在 C# 中才不这么做！

我们来使用 `System` 空间下的懒加载类 `Lazy<T>`：

```c#
class ClassWithLazy
{
    Lazy<HeavyClass> _lazyHeavyClass = new Lazy<HeavyClass>();

    public void SomeMethod()
    {
        var myHeavy = _lazyHeavyClass.Value;
       //使用 myHeavy
    }
}
```

那如果 `HeavyClass` 类的构造方法需要参数咋办？

传递个 `Func` 给它：

```c#
Lazy<HeavyClass> _lazyHeavyClass = new Lazy<HeavyClass>(() =>
{
    var heavy = new HeavyClass(...); 
    ... 
    return heavy;
});
```

---

## 流式接口 Fluent API

流式接口（Fluent API）是一种方法调用风格：

```c#
StringBuilder sbuilder = new StringBuilder();
var result = sbuilder
    .AppendLine("Fluent")
    .AppendLine("Interfaces")
    .AppendLine("Are")
    .AppendLine("Awesome")
    .ToString();
```

看看破晓的 [ModuleLauncher.Re](https://github.com/SinoAHpx/ModuleLauncher.Re/blob/main/README_ZH.md) 是怎么写的吧：~~破晓打钱！~~

```c#
var process = await minecraft.WithAuthentication("<player>")
    .WithJava(@"<java>")
    .LaunchAsync();
```

接下来我们动手写一个流式接口。

我们平常为 `List` 添加元素：

```c#
var words = new List<string>();
words.Add("This");
words.Add("Feels");
words.Add("Weird");
```

不够优雅！用流式接口写个扩展方法：

```c#
public static class ListExtensions
{
    public static List<T> AddItem<T>(this List<T> list, T item)
    {
        list.Add(item);
        return list;
    }
}
```

现在我们就可以使用方法链了：

```c#
var words = new List<string>();
words.AddItem("This")
     .AddItem("Feels")
     .AddItem("Weird");
```

Awesome！瞬间就简洁多了。
