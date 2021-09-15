# Markdown

## 文本语法
1. 标题  
在开头插入1~6个`#`,需要间隔一个空格  
```
# this is H1
## this is H2
###### this is H6
```

2. 字体
用`*`或者`_`作为标识符。如果你的 `*` 和 `_` 两边都有空白的话，它们就只会被当成普通的符号。如果要在文字前后直接插入普通的星号或底线，你可以用反斜线 `\` 。

```
**这是加粗**
__这也是加粗__
*这是倾斜*
_这也是倾斜_
***这是加粗倾斜***
~~这是加删除线~~
```  

3. 分割线  
可以在一行中用三个以上的`*`、`-`、`_`来建立一个分隔线，行内不能有其他东西。也可以在星号或是减号中间插入空格。
```
* * *
***
**********
- - -
_________________
```

4. 引用  
在引用的文字前加 `>` 即可。可以在每行前面加，也可以在每段第一句话前面加。引用块内也可以使用语法。层次性的引用嵌套可以通过不同数量的 `>` 实现。
```
> This is the first level of quoting.
>
> > This is nested blockquote.
>
> > > > Back to the first level.
```

5. 列表  
无序列表使用`*`、`+`或是`-`作为列表标记。
```
- 列表内容
+ 列表内容
* 列表内容
```
有序列表则使用数字接着一个英文句点作为标记。
```
1. 列表内容
2. 列表内容
3. 列表内容
```
列表可以嵌套，上一级和下一级之间敲三个空格即可。
```
* 一级无序列表内容

   * 二级无序列表内容
   * 二级无序列表内容
   * 二级无序列表内容
```
列表项目可以包含多个段落，每个项目下的段落都必须缩进 4 个空格或是 1 个制表符：
```
1.  This is a list item with two paragraphs. Lorem ipsum dolor
    sit amet, consectetuer adipiscing elit. Aliquam hendrerit
    mi posuere lectus.

    Vestibulum enim wisi, viverra nec, fringilla in, laoreet
    vitae, risus. Donec sit amet nisl. Aliquam semper ipsum
    sit amet velit.

2.  Suspendisse id sem consectetuer libero luctus adipiscing.
```

6. 表格  
表头和内容两边都要用`|`包围，整体需要缩进一个`Tab`
```
    |表头|表头|表头|
    :-|:-:|-:
    |内容|内容|内容|
    |内容|内容|内容|
```
第二行分割表头和内容。  
`-`有一个就行,文字默认居左。`-`两边加`：`表示文字居中,`-`右边加`：`表示文字居右。

7. 代码
只要简单地缩进 4 个空格或是 1 个制表符就可以。
```
这是一个普通段落：

    这是一个代码区块。

(当然，前面要有一个空行和前面的文字分隔开)
```
对于单行代码，使用一个`\``将代码块包围。
```
这里有一句代码`代码内容`。
```
对于代码块可以使用三个`\``包围，且前后三个单独各占一行。
```
\```
代码
\```
```
在前三个反引号后注明语言，可以得到相应的代码高亮。

8. 段落  
不同文字块要用空行以示区分。  
换行可以通过`<br>`或者两个空格+回车。

9. 上下标  
使用html，`<sub>,</sub> & <sup>,</sup>`

10. 目录  
加入`[TOC]`自动生成全文目录

## 额外内容
1. 超链接  
要建立一个行内式的链接，只要在方块括号后面紧接着圆括号并插入网址链接即可，注意方括号和圆括号之间一定不能有空格，如果你还想要加上链接的 title 文字，只要在网址后面，用双引号把 title 文字包起来即可，例如：
```
This is [an example](http://example.com/ "Title") inline link.
[This link](http://example.net/) has no title attribute.
```
除了上面的超链接方式，Markdown 还支持以比较简短的自动链接形式来处理网址和电子邮件信箱，只要是用尖括号包起来， Markdown 就会自动把它转成链接。 语法：
```
<http://example.com/>
```

2. 图片  
行内式的图片语法看起来像是：
```
![Alt pic](/path/to/img.jpg)
![Alt pic](/path/to/img.jpg "Optional title")
```
一个` ! `接着一个方括号，里面放上图片的替代文字 `*` 接着一个普通括号，里面放上图片的网址或本地路径（可以是相对路径或绝对路径），最后还可以用引号包住并加上 选择性的 '标题' 文字。  
利用markdown在编写文档时插入图片是默认靠左，有些时候将图片设置为居中时可以更加的美观，这时就需要在图片的信息前边添加如下语句：
```
<div align=center>![Alt pic] (http:...)
如果想将图片位于右侧，只需要将center改为right
```
设置图片大小
```
<img src="http:..." width = "100" height = "100" div align=right />
```

## 公式
1. 基本用法  
行内公式使用`$text$`，行间公式使用`$$text$$`

2. 字母  
    |名称|大写|code|小写|code|
    :-:|:-:|:-:|:-:|:-:
    alpha|A|A|α|\alpha
    beta|B|B|β|\beta
    gamma|Γ|\Gamma|γ|\gamma
    delta|Δ	\Delta|δ|\delta
    epsilon|E|E|ϵ|\epsilon
    zeta|Z|Z|ζ|\zeta
    eta|H|H|η|\eta
    theta|Θ|\Theta|θ|\theta
    iota|I|I|ι|\iota
    kappa|K|K|κ|\kappa
    lambda|Λ|\Lambda|λ|\lambda
    mu|M|M|μ|\mu
    nu|N|N|ν|\nu
    xi|Ξ|\Xi|ξ|\xi
    omicron|O|O|ο|\omicron
    pi|Π|\Pi|π|\pi
    rho|P|P|ρ|\rho
    sigma|Σ|\Sigma|σ|\sigma
    tau|T|T|τ|\tau
    upsilon|Υ|υ|\upsilon
    phi|Φ|\Phi|ϕ|\phi
    chi|X|X|χ|\chi
    psi|Ψ|\Psi|ψ|\psi
    omega|Ω|\Omega|ω|\omega

3. 上标与下标  
上标使用`^`，下标使用`_`。默认仅对单个字符有用，如果需要对字符组起作用，需要用`{}`界定结合性。

4. 括号  
使用原始括号即可。如果需要使括号与临近字符对应，需要使用`\left`和`\right`放在括号前。  
大括号前需要使用转义字符。
尖括号为`\langle, \rangle`  
上取整为`\lceil,\rceil`  
下取整为`\lfloor, \rfloor`

5. 求和与积分  
`\sum`表示求和，上下标分别为上下限。`\int`为积分号，多重积分可以重复字母i。  
`\prod` $\prod$  
`$\bigcup$` $\bigcup$  
`$\bigcap$` $\bigcap$  
`$arg\,\max_{c_k}$` $arg\,\max_{c_k}$  
`$arg\,\min_{c_k}$` $arg\,\min_{c_k}$  
`$\mathop {argmin}_{c_k}$` $\mathop {argmin}_{c_k}$  
`$\mathop {argmax}_{c_k}$` $\mathop {argmax}_{c_k}$  
`$\max_{c_k}$` $\max_{c_k}$  
`$\min_{c_k}$` $\min_{c_k}$  

6. 分式  
使用`\frac ab`，`\frac`作用于后两个组ab。
也可以使用`a \over b`来分隔一个组的两个部分

5. 根式  
`\sqrt [n]`

6. 分类表达  
 定义函数的时候经常需要分情况给出表达式，使用`\begin{cases}…\end{cases}` 。其中：

  使用`\\` 来分类，
  使用`& `指示需要对齐的位置，
  使用`\` +空格表示空格。
```
$$
f(n)
\begin{cases}
\frac n2, &if\ n\ is\ even\\
3n + 1, &if\  n\ is\ odd
\end{cases}
$$
```
$$
f(n)
\begin{cases}
\frac n2, &if\ n\ is\ even\\
3n + 1, &if\  n\ is\ odd
\end{cases}
$$
如果想分类之间的垂直间隔变大，可以使用`\\[2ex] `代替`\\` 来分隔不同的情况。(3ex,4ex 也可以用，1ex 相当于原始距离）。

7. 多行公式  
有时候需要将一行公式分多行进行显示。
```
$$
\begin{equation}\begin{split} 
a&=b+c-d \\ 
&\quad +e-f\\ 
&=g+h\\ 
& =i 
\end{split}\end{equation}
$$
```
$$
\begin{equation}\begin{split} 
a&=b+c-d \\ 
&\quad +e-f\\ 
&=g+h\\ 
& =i 
\end{split}\end{equation}
$$
其中`begin{equation}` 表示开始方程，`end{equation}` 表示方程结束；`begin{split}` 表示开始多行公式，`end{split}` 表示结束；公式中用`\\` 表示回车到下一行，`&` 表示对齐的位置。

8. 方程组  
使用`\begin{array}...\end{array}` 与`\left \{` 与`\right`. 配合表示方程组:
```
$$
\left \{ 
\begin{array}{c}
a_1x+b_1y+c_1z=d_1 \\ 
a_2x+b_2y+c_2z=d_2 \\ 
a_3x+b_3y+c_3z=d_3
\end{array}
\right.
$$
```
$$
\left \{ 
\begin{array}{c}
a_1x+b_1y+c_1z=d_1 \\ 
a_2x+b_2y+c_2z=d_2 \\ 
a_3x+b_3y+c_3z=d_3
\end{array}
\right.
$$
注意：通常MathJax通过内部策略自己管理公式内部的空间，因此`a…b` 与`a…….b` （.表示空格）都会显示为ab 。可以通过在ab 间加入`\` ,增加些许间隙，`\;` 增加较宽的间隙，`\quad` 与`\qquad` 会增加更大的间隙。

## 特殊符号
### 比较运算
  小于(`\lt` )：$\lt$  
  大于(`\gt` )：$\gt$  
  小于等于(`\le` )：$\le$  
  大于等于(`\ge` )：$\ge$  
  不等于(`\ne` ) : $\ne$  
  可以在这些运算符前面加上`\not` ，如`\not\lt` : $\not\lt$

### 集合运算  

  并集(`\cup` ): $\cup$  
  交集(`\cap` ): $\cap$   
  差集(`\setminus` ): $\setminus$   
  子集(`\subset` ):$\subset$   
  子集(`\subseteq` ):$\subseteq$   
  非子集(`\subsetneq` ):$\subsetneq$   
  父集(`\supset` ):$\supset$   
  属于(`\in` ):$\in$   
  不属于(`\notin` ):$\notin$   
  空集(`\emptyset` ):$\emptyset$   
  空(`\varnothing` ):$\varnothing$   

### 排列
`\binom{n+1}{2k}` : $\binom{n+1}{2k}$  
`{n+1 \choose 2k}` : ${n+1 \choose 2k}$

### 箭头
  (`\to` ):$\to$
  (`\rightarrow` ): $\rightarrow$
  (`\leftarrow` ): $\leftarrow$
  (`\Rightarrow` ): $\Rightarrow$
  (`\Leftarrow` ): $\Leftarrow$
  (`\mapsto` ): $\mapsto$

### 逻辑运算
  (`\land` ): $\land$
  (`\lor` ): $\lor$
  (`\lnot` ): $\lnot$
  (`\forall` ): $\forall$
  (`\exists` ): $\exists$
  (`\top` ): $\top$
  (`\bot` ): $\bot$
  (`\vdash` ): $\vdash$
  (`\vDash` ): $\vDash$

[To be continued](https://www.jianshu.com/p/25f0139637b7)

 