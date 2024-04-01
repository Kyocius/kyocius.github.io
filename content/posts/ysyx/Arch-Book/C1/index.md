---
title: 数电与计算机体系结构 01：入门
date: 2024-02-02T11:25:05-04:00
draft: false
slug: arch-book-1
description: Digital Design and Computer Architecture 01
categories:
    - 编程
tags:
    - Arch
    - ysyx
image: header.png
---
## 前言

此系列文章是《Digital Design and Computer Architecture: RISC-V Edition》的读书笔记。

## 数字抽象的深层含义

数字电路使用离散值，而物理值一般是连续的，所以电路设计者要想办法把离散值和连续值对应起来。

### 供电电压

零伏特电压一般指的是接地（GND）。从上世纪 70 年代起，供电电压从 5V 降到了 1.2V 以下。

### 逻辑电平

逻辑电平是指数字电路中电信号的电压水平，用于表示逻辑状态。在数字电路中，通常将电压分为两个离散的状态，分别代表逻辑值 0 和逻辑值 1。

### 噪声裕度

表示可以容忍的电压差值：

$$ NM_{L} = V_{IL} - V_{OL}$$
$$ NM_{H} = V_{OH} - V_{IH}$$

### 直流传输特性

将输入电压作为自变量，将输出电压作为因变量，抽象出一个函数。其形成的图像很有特点，叫作直流传输特性（DC Transfer Characteristics）。图像请参阅原书。

### 静态电路法则

静态电路法则要求，在给定逻辑上有效的输入情况下，每个电路元件都将产生逻辑上有效的输出。

为了提高生产力，诞生了四个逻辑家族：

- TTL
- CMOS
- LVTTL
- LVCMOS