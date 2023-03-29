---
title: 编译原理05-parser-antlr
date: 2023-03-24 15:09:01
tags:
- 编译原理
- 软件工程专业课
categories:
- 编译原理
---

# 五、ANTLR 4 语法分析器

## 语法分析

### 二异性文法

若文法G对同一句子产生不止一棵分析树，则称G是二义的。

#### If-Then-Else的二异性

![](/pic/compilers-05-01.png)

```
//IfStat.g4
stat : 'if' expr 'then' stat
		 | 'if' expr 'then' stat 'else' stat
		 | expr
		 ;
->
//IfStatOpenMatched.g4
stat : matched_stat | open_stat
matched_stat : 'if' expr 'then' matched_stat 'else' matched_stat
						 | expr
						 ;
open_stat : 'if' expr 'then' stat
				  | 'if' expr 'then' matched_stat 'else' open_stat
				  ;
```

#### 运算符结合性与优先级带来的二异性

```
//Expr.g4
expr :
		 | expr '*' expr
		 | expr '-' expr
		 ;
		 
//ExprAssoc.g4
expr :
		 | '!' expr
		 | <assoc = right> expr '^' expr
		 | DIGIT
		 ;
//右结合运算符，前缀运算符与后缀运算符的结合性
```

##### 左递归（左结合）

```
expr : expr '-' term
     | term
     ;
     
term : term '*' factor
     | factor
     ;
     
factor : DIGIT ;
```

##### 右递归（右结合）

```
expr : term expr_prime ;

expr_prime : '-' term expr_prime
           | 
           ;
         
term : factor term_prime ;

term_prime : '*' factor term_prime
           |
           ;
           
factor : DIGIT
```

