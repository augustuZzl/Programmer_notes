### 函数与极限

#### 映射与函数

##### 集合

集合：具有某种特定性质的事物的总体,组成这个集合的事物称为该集合的元素(元).

通常用大写拉丁字母 $A, B, C, ...$ 表示集合,用小写拉丁字母 $a, b, c, ...$ 表示集合的元素.

如果 $a$ 是 $A$ 的元素,那么就说 $a$ 属于 $A$ ,记做 $a \in A$ ;如果 $a$ 不是 $A$ 的元素,就说 $a$ 不属于 $A$ ,记做 $a \notin A$. 

集合的表示方法有两种:列举法和描述法(记得高中有好几种，例如绘图法什么的...)：

- 列举法：把集合的全部元素列出来：$A=\{a_1,a_2,...,a_n\}$
- 描述法：描述元素的性质：$M=\{x|x具有性质P\}$,例如集合 $B$ 是方程 $x^2-1=0$ 的解集，那么就可以表示成：$B=\{x|x^2-1=0\}$

在表示数集的字母的右上角标上 $*$  来表示该数集内排除 0 ,标上 + 表示排除 0 和负数.

习惯上，用特殊的字母表示特殊的集合：

- 全体非负整数即自然数的集合记做 $N$ ,即 $N=\{0,1,2,...,n,...\}$
- 全体正整数的集合记做 $N^+$ ,即 $N^+=\{1,2,...,n,...\}$
- 全体整数的集合记做 $Z$ ,即 $Z=\{...,-n,...,-1,0,1,...n,...\}$
- 全体有理数的集合记做 $Q$ ,即 $Q=\{\frac p q|p \in Z,q \in N^+ 且p与q互质 \}$
- 全体实数的集合记做 $R$ , $R^*$ 为排除 0 的实数集, $R^+$ 为全体正实数集

设 $A、B$ 是两个集合,如果集合 $A$ 的元素都是集合 $B$ 的元素,则称 $A$ 是 $B$ 的子集,记做 $A \subset B$($A$ 包含于 $B$)或 $B \supset A$($B$ 包含 $A$).

若 $A \subset B$ 且 $A \neq B$ ,则称 $A$ 是 $B$ 的真子集,记做 $A \subseteq B$.

不包含任何元素元素的集合称为空集,记做 $\emptyset$ ,规定空集是任何集合的子集,即 $\emptyset \subset A$

集合的基本运算有这几种:交、并、补

设 $A、B$ 是两个集合:

- 所有属于 $A$ 或者属于 $B$ 的元素组成的集合,称为 $A$ 与 $B$ 的并集(简称并),记做 $A \bigcup B$

  $A \bigcup B = \{x|x \in A 或 x \in B \}$

- 即属于 $A$ 又属于 $B$ 的元素组成的集合,称为 $A$ 与 $B$ 的交集(简称交),记做 $A \bigcap B$

  $A \bigcap B = \{x|x \in A 且 x \in B \}$

- 所有属于 $A$ 而不属于 $B$ 的元素组成的集合,称为 $A$ 与 $B$ 的差集(简称差),记做 $A  \backslash  B$

  $A  \backslash  B=\{x|x \in A 且x \notin B\}$

设集合 $I$ ,集合 $A$ 是 $I$ 的子集,我们就称集合 $I$ 为全集或基本集,称 $I \backslash A$ 为 $A$ 的余集或补集,记做 $A^C$ .例如在是实数集 $R$ 中,集合 $A=\{x|0<x\leq1\}$ 的余集就是 $A^C=\{x|x\leq0或x>1\}$

设 $A、B、C$ 为任意 3 个集合,则有以下法则:

1. 交换律：$A\bigcup B=B\bigcup A$，$A\bigcap B=B\bigcap A$
2. 结合律：$(A\bigcup B)\bigcup C=A\bigcup (B\bigcup C)$，$(A\bigcap B)\bigcap C=A\bigcap (B\bigcap C)$
3. 分配律：$(A\bigcup B)\bigcap C=(A\bigcap C)\bigcup (B\bigcap C)$，$(A\bigcap B)\bigcup C=(A\bigcup C)\bigcap (B\bigcup C)$
4. 对偶律：$(A\bigcup B)^C=A^C\bigcap B^C$，$(A\bigcap B)^C=A^C\bigcup B^C$

