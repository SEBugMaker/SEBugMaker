---
title: 编译原理04_automata
date: 2023-03-15 19:09:45
tags:
- 编译原理
categories:
- 编译原理
---

# 四、自动机理论与词法分析器生成器

## 自动机-Automation/Automata

两大要素：状态集S 和 状态转移函数$\delta$

### 元胞自动机-Cellular Automation/CA

根据表达/计算能力的强弱，自动机可以分为不同的层次

![compilers-04-01](/pic/compilers-04-01.png)

### 目标

正则表达式RE => 词法分析器

![compilers-04-01](/pic/compilers-04-02.png)



## 非自动性有穷自动机-NFA/Nondeteministic Finite Automaton

$A$是一个五元组 $A = (\Sigma , S, s_{0},\delta,F)$:

1.字母表$\Sigma$$(\epsilon \notin \Sigma)$

2.有穷的状态集合$S$

3.唯一的初始状态$s_{0} \in S$

4.状态转移函数$ \delta $
$$
\delta : S \times (\Sigma \cup \lbrace \epsilon \rbrace) \to 2^S
$$
5.接受状态集合$F \subseteq S$

约定：所有没有对应出边的字符默认指向一个不存在的空状态$\varnothing$



![compilers-04-01](/pic/compilers-04-03.png)

（非确定性）有穷自动机是一类极其简单的计算装置

它可以识别（接受/拒绝）$\Sigma $上的字符串
$$
Definition(接受-Accept) \\\\
(非确定性)有穷自动机A接受字符串x，当且仅当存在一条从开始状态s_{0}到某个状态
f \in F ,标号为x的路径
$$
因此, $A$ 定义了一种语言 $L(A)$: 它能接受的所有字符串构成的集合

#### Example

![compilers-04-01](/pic/compilers-04-04.png)

## 自动机的两个基本问题

### Membership问题

给定x，$x \in L(A)$?

### $L(A)$究竟是什么

## 确定性有穷自动机-DFA /Deterministic Finite Automaton

### Definition

确定性有穷自动机$A$是一个五元组$A=(\Sigma ,S, s_{0}, \delta ,F)$

1.字母表$\Sigma$$(\epsilon \notin \Sigma)$

2.有穷的状态集合$S$

3.唯一的初始状态$s_{0} \in S$

4.状态转移函数$ \delta $
$$
\delta : S \times \Sigma \to S
$$
5.接受状态集合$F \subseteq S$

约定：所有没有对应出边的字符默认指向一个死状态

## NFA与DFA

NFA 简洁易于理解, 便于描述语言$L(A)$

DFA 易于判断 $x \in L(A)$, 适合产生词法分析器

用 NFA 描述语言, 用 DFA 实现词法分析器
$$
RE \to NFA \to DFA \to 词法分析器
$$
![compilers-04-01](/pic/compilers-04-05.png)

## RE到NFA：Thompson构造法

### Thompson构造法的基本思想

#### Definition

给定字母表$\Sigma$,$ \Sigma$上的正则表达式有且仅有以下规则定义：

1.$ \epsilon $是正则表达式

2.$\forall a \in \Sigma$,$a$是正则表达式

3.如果$s$是正则表达式，则$(s)$是正则表达式

4.如果$s$与$t$是正则表达式，则$s \mid t, st, s^*$也是正则表达式

### $N(r)$的性质以及Thompson构造法复杂度分析

1.$N( r)$的开始状态与接受状态均唯一

2.开始状态没有入边，接受状态没有出边

3.$N(r)$的状态数$\mid S \mid \leq 2 \times \mid r \mid$（$ \mid r \mid $：r中运算符与运算分量的总和）

4.每个状态最多有两个$\epsilon -$入边与两个出边$\epsilon -$

出边

5.$\forall a \in \Sigma $，每个状态最多有一个a-入边与一个a-出边

### Kleene构造法

![compilers-04-01](/pic/compilers-04-06.png)

## 从NFA到DFA的转换：子集构造法（Subset/Powerset Construction）

思想：用DFA模拟NFA

![compilers-04-01](/pic/compilers-04-07.png)

从状态$s$开始，只通过$\epsilon$-转移可达的状态集合
$$
\epsilon -closure(s) = \lbrace t \in S_{N} \mid s \stackrel{\mathrm{\epsilon^*}}{\to} t \rbrace \\\\
\epsilon-closure(T) = \underset{s \in T}{\bigcup} \epsilon-closure(s) \\\\
move(T,a)=\underset{s \in T}{\bigcup} \delta(s,a)
$$

### 子集构造法$(N \to D)$的原理

$$
N:(\Sigma_{N},S_{N},n_{0},\delta_{N},F_{N}) \\\\
D:(\Sigma_{D},S_{D},d_{0},\delta_{D},F_{D}) \\\\
\Sigma_{D} = \Sigma_{N} \\\\
S_{D} \subseteq 2^{S_{N}} (\forall s_{D} \in S_{D}:s_{D} \subseteq S_{N}) \\\\
初始状态:d_0 = \epsilon-closure(n_0) \\\\
转移函数： \forall a \in \Sigma_{D}: \delta_{D}(s_{D},a) = \epsilon - closure(move(s_D,a)) \\\\
接受状态集:F_{\mathcal{D}} = \lbrace s_{D} \in S_{\mathcal{D}} \mid \exists \mathcal{f} \in F_N . \mathcal{f} \in S_D \rbrace
$$

### 闭包（closure）：$\mathcal{f} - closure(T)$

$$
\epsilon-closure(T) \\\\
T \to f(T)\to f((T)) \to ...
$$

直到找到x使得f(x) = x(x称为f的不动点)

### DFA最小化

![compilers-04-01](/pic/compilers-04-08.png)

### Example

![compilers-04-01](/pic/compilers-04-09.png)

## DFA最小化算法基本思想：等价的状态可以合并

### 如何定义等价状态

$$
s \sim t \Leftrightarrow \forall a \in \Sigma.((s \stackrel{\mathrm{a}}{\to} s')\wedge (t\stackrel{\mathrm{a}}{\to} t'))
\Rightarrow (s'= t')
$$

但是这个定义是错误的

![compilers-04-01](/pic/compilers-04-10.png)

但是接受状态与非接受状态必定不等价，空串$\epsilon $区分了这两类状态
