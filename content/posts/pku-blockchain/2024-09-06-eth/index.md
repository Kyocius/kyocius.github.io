---
title: "潦草 PKU 区块链备忘录（下）"
date: 2024-09-06T00:28:00+08:00
draft: true
slug: 2024-09-06-pku-eth
enableLatex: true
description: 北大肖臻老师的区块链公开课。以太坊部分。
categories:
    - 编程
tags:
    - 区块链
featured_image: "bitcoin.jpg"
---
# 状态树

## 概念

- **Merkle Patricia Tree**：结合了 Merkle Tree 和 Patricia 前缀树。
- **Patricia 前缀树**：压缩前缀树，可以高效存储和检索键值对。
> 例如，如果两个字符串 "apple" 和 "apricot" 的前缀 "app" 相同，它们就会被压缩到同一个节点中，该节点只存储 "app"，然后分别指向 "le" 和 "ricot" 的子节点。

# GHOST

**GHOST**（Greedy Heaviest Observed Subtree） 协议是对传统最长链规则的改进。它决定主链的方式不同，不仅仅基于链的长度，还要基于链的“重量”（即考虑链上包含的所有块的总和，包括叔块）。

## 以太坊中的实现

### 叔块奖励
如果叔块被包含在主链中，它们的创建者可以获得较少于正常区块的奖励。此外，包含叔块的区块创建者也会得到奖励。
### 叔块的要求
叔块必须是与主链具有共同祖先的块，并且必须在一定深度内（即与当前区块不超过 7 个区块距离）。

# 智能合约

## 概念

智能合约保存了合约当前的运行状态：

- balance：当前余额
- nonce：交易次数
- code：合约代码
- storage：存储，使用 MPT 数据结构

Solidity 是最常用的语言，类似 JavaScript。其脚本一般具备以下结构：

```solidity
pragma solidity ^0.4.xx;
```

```solidity
contract SimpleAuction {
	address public beneficiary;
	unit public auctionEnd;
	address public highestBidder;
	mapping(address => uint) bids;
	address[] bidders;
}
```

