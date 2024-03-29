---
title: "[胡说翻译]写给程序员的范畴论·分则能成（Category: The Essence of Composition）"
date: 2024-03-08 13:17:57
tags:
  - 范畴论
  - C++
  - Haskell
category: 技术笔记
cover: https://bartoszmilewski.files.wordpress.com/2014/10/img_1330.jpg?w=768
katex: true
---
[原文链接：Category Theory for Programmers: Category: The Essence of Composition](https://bartoszmilewski.com/2014/11/04/category-the-essence-of-composition/)

> 我写的[前言](https://fa1se-promise.cc/2024/02/26/category-preface/)好评如潮。但这也吓到我了：大家给了我太高的期待。我怕我无论如何写，总有一堆读者向我表达不满。有些人不喜欢C++，想看Haskell；有的人又不喜欢Haskell想看Java。阐述的节奏对有些人而言太慢对另一些人而言又太快。这本书无法变得完美而只能成为一种妥协。我只希望我能更多地和大家分享我的“尤里卡！”时刻。让我们从最基础的部分开始吧。

范畴（Category，其实就是分类，但在后文中我们统一称为范畴）是一个极其简单的概念。范畴由对象和它们之间的“箭头”组成。这就是为什么它们很容易用图画来表示。一个对象可以用圆圈或者点表示，而箭头——就是箭头。（为了让文章好看一些，我偶尔使用小猪来表示对象，用烟火表示箭头）。*范畴的本质就是组合*，或者也可以说，组合的本质就是一个范畴。箭头之间就是可以组合的：如果你有一个从对象A到对象B的箭头，以及一个对象B到对象C的箭头，那么它们的组合，即对象A到对象C的箭头，一定存在。

![在一个范畴里，如果有一个箭头从A到B，有一个箭头从B到C，那么它们的组合，即从A到C的箭头一定存在。这张图所显示的还不是一个完整的范畴，它缺少了恒等态射（identity morphism），我们稍后会提到。](https://bartoszmilewski.files.wordpress.com/2014/10/img_1330.jpg?w=768)

## 箭头就是函数

到现在为止是不是有些太抽象了？别急。我们来看看具体的范畴。我们可以将这些箭头——也叫做态射（morphisms）——视为函数。你有一个函数 $f$，参数类型为A，返回类型为B。你还有另一个函数 $g$，参数类型为B，返回类型为C。你可以通过把 $f$ 的返回值传递给 $g$ 来组合它们。这样你就定义了一个新的函数，它的参数类型为A，返回类型为C。

在数学里，我们一般使用一个小圆圈表示函数组合（复合函数）：$g \circ f$。注意，组合是从右到左的。这可能有些让人迷惑。你应该对Unix的管道操作符比较熟悉：
```shell
lsof | grep Chrome
```
或者是F#中的`>>`操作符。这些操作符都是从左到右的。但是在数学和Haskell中，函数组合是从右向左的。你可以把它读作“g在f后”（g after f）。

为了更清晰的说明，我们直接进行一个C语言的写。我们有一个函数 $f$，参数类型为A，返回类型为B：
```c
B f(A a);
```

此外：
```c
C g(B b);
```

我们可以组合这两个函数：
```c
C g_after_f(A a) {
  return g(f(a));
}
```

你看，在C语言里，组合也是从右向左的： $g(f(a))$。

我希望我可以告诉你“C++标准库里有一个模板函数可以将两个函数组合在一起”。很可惜，并没有。所以我们还是看看远处的Haskell吧。以下是从A到B的函数的声明：
```haskell
f :: A -> B
```

类似地：
```haskell
g :: B -> C
```

复合函数：
```haskell
g . f
```

看过Haskell之后你便能体会到它的眉清目秀。事实上，Haskell还允许你使用Unicode，这样你就可以把代码写成：
```haskell
g ∘ f
```

你甚至可以用Unicode的双冒号和箭头：
```haskell
f ∷ A → B
```

这是你的第一节Haskell课：双冒号表示“有...的类型”，在两个类型之间插入一个箭头来表示函数类型，用一个点来表示复合函数（或者用Unicode的“∘”）。

## 组合的性质
组合有两个非常重要的性质，无论在什么范畴中，它们都必须满足。

- 组合满足结合律。假定你有三个态射$f$，$g$和$h$，且你可以把它们组合起来（也就是说，它们返回的对象可以传递给下一个态射），你不需要加任何的括号。在数学中，这可以表示为

$$h\circ(g\circ f) = (h\circ g)\circ f = h\circ g\circ f$$

使用类Haskell的伪代码写出来是这样的：
```haskell
f :: A -> B
g :: B -> C
h :: C -> D

h . (g . f) == (h . g) . f == h . g . f
```

（请注意，这是**伪代码**，因为函数的相等并没有定义。）

结合律对函数来说显而易见，但对于其它范畴就不好说了。

- 对于每个对象A，总存在一个恒等组合。这个“箭头”总是指回原本的对象。“恒等组合”意味着：如果我们将其与另一个箭头——始于A或者终于A——相结合，我们总能得到原本的箭头。对象A的恒等箭头记作$id_A$（identity on A）。在数学中，如果$f$接受A且返回B，我们有：

$$f \circ id_A = f$$

以及

$$ id_B \circ f = f $$

当对象是函数时，恒等箭头可以用恒等函数——直接返回参数的函数——实现。这个实现对于所有类型都是一样的，也就是说它是多态的。在C++中我们可以使用模板：
```c++
template <typename T> T id(T x) {
  return x;
}
```

当然了，实际的C++中才没有那么简单。你不仅要考虑传进来了什么，还要考虑怎么传进来（传值，传引用，常引用，移动语义……）。

在Haskell中，恒等函数是标准库的一部分（叫做Prelude）。以下是它的声明和定义：
```haskell
id :: a -> a
id x = x
```

你可以看到在Haskell中，多态就像喝汤一样简单。在声明中，你只要把具体的类型换成类型变量即可。冷知识：Haskell中的具体类型名称都以大写字母开头，类型变量的名称则以小写字母开头。因此这里的`a`可以指代所有类型 。

Haskell函数的定义由函数名和一串参数名组成——这里只有`x`一个参数。函数体放在`=`后面。这种简洁表达很容易劝退不少人，不过看多了你就习惯了。函数的定义和调用就是函数式编程的馒头和腐乳，因此语法上越简单越好。我们不仅没有括号，甚至连分割参数的逗号都没有（后面定义多参数函数的时候你就能看到了）。

函数体总是一个表达式——没有语句（statement）。函数的结果就是这个表达式——这里就是`x`。

这就是我们的第二节Haskell小灶。

恒等函数的性质可以被写为（也是类Haskell伪代码）：
```haskell
f . id == f
id . f == f
```

你可能会问自己一个问题，要这样一个啥都不做的函数影响在哪？那么不妨问这样一个问题：数字$0$的意义是什么？$0$也是空无一物的化身。古罗马人的数字系统里就没有$0$，但是他们还是可以建造道路，修建水渠，有的甚至留存至今。

当我们在和符号变量打交道的时候，$0$和`id`这样的中性值（Neutral values）就显得非常有用。这就是为什么罗马人的代数水平感觉不如阿拉伯人和波斯人，他们很懂$0$。恒等函数作为告诫函数的参数或返回值非常方便。高阶函数允许我们操作函数符号（译注：即将函数当成变量传递），这就是关于函数的代数。

总结一下：范畴由对象和箭头（态射）组成。态射之间可以相互组合，且组合满足结合律。每个对象都有一个恒等箭头，可以用作组合时的单位值。

## 组合：编程的本质
函数式程序员有特别的处理问题技巧。例如在程序开头，<del>先望下周围的风景</del>提出禅宗般的问题。例如在设计一个交互式应用的时候，他们会问：交互是啥？当他们实现[康威的生命游戏](https://www.bilibili.com/video/BV1HK4y1Q7kj/?share_source=copy_web&vd_source=757ef63d5d55f80a97668a0be5f34b0c)，他们会问：生命的意义是什么？本着这种精神，我们问：编程是什么？从最底层的角度来看，编程就是告诉计算机应该做什么：“把地址x处的值取出来加到寄存器EAX上”。但是即便我们在写汇编，我们告诉计算机的表达式或其它什么东西也比看上去更有意义。我们在解决一个并不显然的问题（要是显然的话计算机自己会做的）。我们怎么解决这个问题的？我们将大的问题分解为小问题。如果小问题还是太大，我们就继续分解。最终，我们编写代码，解决所有这些小问题。这揭示了**编程的本质**：我们将这些代码组合在一起以解决更大更复杂的问题。如果组合不成立，那么分解也没有意义。

这种层次分解和重组的过程并不是计算机强加给我们的。这是人类的极限<del>（除非超越人类）</del>。我们的大脑一次性只能处理一小部分概念。一篇著名的心理学论文[《神奇的数字：7±2：我们信息加工能力的局限》（The Magical Number Seven, Plus or Minus Two: Some Limits on Our Capacity for Processing Information）](https://www.psychspace.com/psych/viewnews-12308)假定我们的大脑只能保存$7 \pm 2$块数据。我们对人类短期记忆的理解细节可能正在发生变化，但我们确信它是有限的。最重要的是，我们无法处理大量的对象或意大利面条般的代码。我们需要各种结构，并不是因为结构化的代码看起来很好看，而是因为这样方便我们大脑进行有效处理。我们通常会形容一段代码优雅绝赞，但其实我们是在说它很容易被我们的大脑处理。优雅的代码会创建恰好合适的数据块，正好塞进我们的脑子并最终被消化吸收。

那么什么样的数据块适合程序组合呢？它们的表面积增长应该比体积增长更慢（我喜欢这个类比：几何物体的表面积增长的数量级是平方，而体积增长则是立方，因而表面积增长比体积慢）。表面积指的是我们组合不同块所需要的信息量，而体积则是实现块的所需要的信息量。一旦一个块被实现，我们就可以把实现细节丢到一边，专注于它如何与其它块交互。在面向对象语言中，物体表面就是类的声明，或者是抽象接口。在函数式编程中，表面是函数的声明（我在此稍做简化，但大致就是这样的）。

范畴论算是一个阻止我们观察对象内部的极端分子。范畴论中的对象是一个模糊的实体：你只知道它们和其它对象之间有什么关系——如何用箭头联系起来。这就是互联网搜索引擎通过分析传入和传出链接对网站进行排名的方式（除了某度的竞价排名）。在面向对象里，一个理想化对象的所有可见元素都应该由它的抽象接口提供（只有表面积，没有体积），此时方法（methods）充当着箭头的作用。如果你不得不深入查看对象的实现才能理解如何将其与其它对象组合，你就失去了编程范式所提供的优势。
