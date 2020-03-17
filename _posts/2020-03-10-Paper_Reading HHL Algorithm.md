---
layout:     post
title:      Series Articles of Paper Reading & Translation -- 02
subtitle:   Quantum Algorithm For Linear System, by Harrow etc.
date:       2020-03-10
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Paper Reading
    - Quantum Machine Learning
    - Quantum 
    - HHL Algorithm
---

<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>

> Translation and notes of classical quantum calculation paper [**Quantum Algorithm For Linear System (HHL algorithm)**](https://github.com/OUCliuxiang/PaperReading/blob/master/QML//QuantumAlgotithmForLinearSystemOfEquations.pdf)

# 一种用于线性方程系统的量子算法     
解线性系统方程是一个普遍存在的问题，它既可以是一个独立的问题也可以作为其他更复杂问题的子问题，该问题可以概括为为：给定一个矩阵$A$和一个向量$\vec{b}$, 寻找一个向量$\vec{x}$满足 $A\vec{x} = \vec{b}$. 现在我们考虑这么一种情况，有一类问题不需要知道确切的$\vec{x}$的值，只需要知道其在一些与之有关联的操作(如有关$M$的矩阵方程$\vec{x}\dagger M\vec{x}$)中的大致期望值。在上述情况，当$A$是$N\timesN$的条件数为$\kappa$稀疏矩阵，经典算法可以在 $\tilde{O}(N\sqrt{\kappa})$ 的时间复杂度内计算出$\vec{x}$并估计$\vec{x}\dagger M\vec{x}$。这篇文章将展示一种可以在多项式时间$poly(log N, \kappa)$内完成该任务的量子算法，相比于经典算法这是一种指数级加速。

## 引言     
量子计算机是一种以经典计算机所不能的方式实现将量子力学应用于计算的硬件。在某些问题上，量子算法可以达到相比于其在经典计算机中实现的对应算法的指数级加速，比如Shor的因式分解算法[1]。但是这种指数级的加速依然少被发现，那些实现指数级加速的算法(比如使用量子计算机模拟量子系统)也大多局限于处理量子力学领域的问题。这篇文章提出一种量子算法去估计一组线性方程的解的特征值。与相同任务上应用的经典算法相比，这个算法是指数级加速的。   
线性方程在几乎所有的科学和工程学领域扮演着极为重要的角色。决定了方程的数据集规模随时间急速增加，以至于我们面临着T级别甚至P级的数据量的处理求解问题。在另一方面，比如在离散化(discretize)偏微分方程问题时，线性方程或许是隐式定义的，从而其规模远超过问题所原始描述的。对于经典计算机而言，仅仅大概地估计有N个未知数的、由N个线性方程组成的方程组的解就需要至少N阶的时间消耗。事实上，仅仅是写出结果就需要N阶的时间了。然而更常见的情况是，相对于方程组的完整解，人们往往对于这个解所执行的计算，比如对确定某些下标子集的总权重更感兴趣。我们将通过一些例子展示，量子计算机可以在N的对数时间内得到近似的函数值、在N的多项式时间内得到条件数(条件数在下面章节定义)和所需精度。在对于N的依赖方面，量子计算机可以达到相对于经典架构的指数级别的优化；在条件数方面与经典架构差不多；在误差方面量子机表现得差一些。因此我们的算法在广泛的有较大N值和较小条件数的设置下可以实现非常有效甚至指数级的加速效应。    
此处我们先概括(sketch)算法的基本思想，在接下来的章节中会更详细的展开讨论。给定$N\times N$的厄密特矩阵$A$和单位(unit)向量$\vec{b}$，假设我们现在要寻找一个$\vec{x}$满足$A\vec{x}=\vec{b}$(在后面的问题中我们会讨论效率的问题以及我们对于A和$\vec{b}$的假设应当怎么放松)。首先，算法将向量$\vec{b}$表示为量子态$|b\rangle=\sum_{i=1}^N b_i|i\rangle$ (这儿是第一个坑，记作“坑-1”)。接下来我们用厄密特模拟(Hamiltonion simulation)[3,4]把$e^{iAt}$部署到$|b\rangle$构造出关于不同时间$t$的叠加态。(这儿是第二个坑，怎么apply $e^{iAt}$ to $|b\rangle$??又怎么 for a superposition???记作“坑-2”)。这个求$A$的幂(exponentiate, 指数化，求幂)的能力经由一个广为人知的相位估计技术[5,6,7]转化为了在A的本征基(eigenbasis)上分解(decompose)$|b\rangle$并寻找相应的特征值$\lambda_i$(第三个大坑：求$A$的幂的能力，那儿就冒出来这么一个能力？？记作“坑-3”)。非正式地讲，经过这一步后系统的状态非常接近于$\sum_{j=0}^N\beta_j|u_j\rangle|\lambda\rangle$(第四个大坑：系统的状态，怎么讲？？怎么定义和描述the state of the system？记作“坑-4”)，此处$u_j$是A的特征向量基，$|b\rangle=\sum_{j=1}^N\beta_j|u_j\rangle$(此处又是一个大坑：如果$|b\rangle$可以使用$A$的特征值表示，是不是说明$|b\rangle$并是不独立于$A$存在的？记作“坑-5”)。接下来做一个$\|\lambda_j\rangle \rightarrow C\lambda_j^{-1}\|\lambda_j\rangle$的线性映射，$C$是正则化常数。由于该操作非幺正，有失败的可能性，下文对运行时的讨论里包含这种情况，此处以操作成功讨论。映射成功后，解运算$\|\lambda_j\rangle$寄存器，则剩下的状态正比于$\sum_{j=1}^{N}\beta_j\lambda_j^{-1}\|u_j\rangle=A^{-1}\|b\rangle=\|x\rangle$。