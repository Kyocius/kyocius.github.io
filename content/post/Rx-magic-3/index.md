---
title: Rx.NET 响应式编程指北 03
description: 创建可观察数据流
date: 2022-08-10
draft: true
slug: rx-magic-3
categories:
    - 编程
tags:
    - Csharp
    - Rx.NET
image: head.png
---
## 前言

之前我们见识了 Rx.NET 的基本用法，把 .NET 事件转化成了可观察数据流，并且补充了一些拓展知识（Ex），现在我们要接着深入 Rx.NET 的核心技术了。

## 事件流

还记得吗，`IObservable` 接口只有一个方法 `Subscribe`，是 Rx.NET 的基础。

我们试着来编写一个**即时通讯**系统

### 实现 `IObservable` 接口

在实际的开发中，非常不推荐直接实现 `IObservable` 接口，但是理解这个流程对我们的学习很有帮助。

```c#
using System;
using System.Reactive.Disposables;

public class NumbersObservable : IObservable<int>
{
    private readonly int _amount;
    
    public NumbersObservable(int amount) 
    {
         _amount = amount;
    }
    public IDisposable Subscribe(IObserver<int> observer)
    {
        for (int i = 0; i < _amount; i++) 
        {
            observer.OnNext(i); 
        } 
        observer.OnCompleted(); 
        return Disposable.Empty; 
    }
}
```

再实现个观察者：

```c#
public class ConsoleObserver<T> : IObserver<T> 
{
 private readonly string _name;
 public ConsoleObserver(string name="") 
 {
 _name = name;
 }
 public void OnNext(T value) 
 { 
 Console.WriteLine("{0} - OnNext({1})",_name,value); 
 } 
 public void OnError(Exception error) 
 { 
 Console.WriteLine("{0} - OnError:", _name); 
 Console.WriteLine("\t {0}", error); 
 } 
 public void OnCompleted() 
 { 
 Console.WriteLine("{0} - OnCompleted()", _name); 
 } 
}
```

