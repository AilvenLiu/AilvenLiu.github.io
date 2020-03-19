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
解线性系统方程是一个普遍存在的问题，它既可以是一个独立的问题也可以作为其他更复杂问题的子问题，该问题可以概括为为：给定一个矩阵$A$和一个向量$\vec{b}$, 寻找一个向量$\vec{x}$满足 $A\vec{x} = \vec{b}$. 现在我们考虑这么一种情况，有一类问题不需要知道确切的$\vec{x}$的值，只需要知道其在一些与之有关联的操作(如有关$M$的矩阵方程$\vec{x}^\dagger M\vec{x}$)中的大致期望值。在上述情况，当$A$是$N\times N$的条件数为$\kappa$稀疏矩阵，经典算法可以在 $\tilde{O}(N\sqrt{\kappa})$ 的时间复杂度内计算出$\vec{x}$并估计$\vec{x}^\dagger M\vec{x}$。这篇文章将展示一种可以在多项式时间$poly(log N, \kappa)$内完成该任务的量子算法，相比于经典算法这是一种指数级加速。

## (I) INTRODUCTION       
量子计算机是一种以经典计算机所不能的方式实现将量子力学应用于计算的硬件。在某些问题上，量子算法可以达到相比于其在经典计算机中实现的对应算法的指数级加速，比如Shor的因式分解算法[1]。但是这种指数级的加速依然少被发现，那些实现指数级加速的算法(比如使用量子计算机模拟量子系统)也大多局限于处理量子力学领域的问题。这篇文章提出一种量子算法去估计一组线性方程的解的特征值。与相同任务上应用的经典算法相比，这个算法是指数级加速的。   
线性方程在几乎所有的科学和工程学领域扮演着极为重要的角色。决定了方程的数据集规模随时间急速增加，以至于我们面临着T级别甚至P级的数据量的处理求解问题。在另一方面，比如在离散化(discretize)偏微分方程问题时，线性方程或许是隐式定义的，从而其规模远超过问题所原始描述的。对于经典计算机而言，仅仅大概地估计有N个未知数的、由N个线性方程组成的方程组的解就需要至少N阶的时间消耗。事实上，仅仅是写出结果就需要N阶的时间了。然而更常见的情况是，相对于方程组的完整解，人们往往对于这个解所执行的计算，比如对确定某些下标子集的总权重更感兴趣。我们将通过一些例子展示，量子计算机可以在N的对数时间内得到近似的函数值、在N的多项式时间内得到条件数(条件数在下面章节定义)和所需精度。在对于N的依赖方面，量子计算机可以达到相对于经典架构的指数级别的优化；在条件数方面与经典架构差不多；在误差方面量子机表现得差一些。因此我们的算法在广泛的有较大N值和较小条件数的设置下可以实现非常有效甚至指数级的加速效应。    

此处我们先概括(sketch)算法的基本思想，在接下来的章节中会更详细的展开讨论。给定$N\times N$的厄密特矩阵$A$和单位(unit)向量$\vec{b}$，假设我们现在要寻找一个$\vec{x}$满足$A\vec{x}=\vec{b}$(在后面的问题中我们会讨论效率的问题以及我们对于A和$\vec{b}$的假设应当怎么放松)。首先，算法将向量$\vec{b}$表示为量子态$|b\rangle=\sum_{i=1}^N b_i|i\rangle$ (这儿是第一个坑，记作“坑-1”)。接下来我们用厄密特模拟(Hamiltonion simulation)[3,4]把$e^{iAt}$部署到$|b\rangle$构造出关于不同时间$t$的叠加态。(这儿是第二个坑，怎么apply $e^{iAt}$ to $|b\rangle$??又怎么 for a superposition???记作“坑-2”)。这个求$A$的幂(exponentiate, 指数化，求幂)的能力经由一个广为人知的相位估计技术[5,6,7]转化为了在A的本征基(eigenbasis)上分解(decompose)$|b\rangle$并寻找相应的特征值$\lambda_i$(第三个大坑：求$A$的幂的能力，那儿就冒出来这么一个能力？？记作“坑-3”)。非正式地讲，经过这一步后系统的状态非常接近于$\sum_{j=0}^N\beta_j|u_j\rangle|\lambda\rangle$(第四个大坑：系统的状态，怎么讲？？怎么定义和描述the state of the system？记作“坑-4”)，此处$u_j$是A的特征向量基，$|b\rangle=\sum_{j=1}^N\beta_j|u_j\rangle$(此处又是一个大坑：如果$|b\rangle$可以使用$A$的特征值表示，是不是说明$|b\rangle$并是不独立于$A$存在的？记作“坑-5”)。接下来做一个$\|\lambda_j\rangle \rightarrow C\lambda_j^{-1}\|\lambda_j\rangle$的线性映射，$C$是正则化常数。由于该操作非幺正，有失败的可能性，下文对运行时的讨论里包含这种情况，此处以操作成功讨论。映射成功后，解运算$\|\lambda_j\rangle$寄存器，则剩下的状态正比于$\sum_{j=1}^{N}\beta_j\lambda_j^{-1}\|u_j\rangle=A^{-1}\|b\rangle=\|x\rangle$。    
* 译注，尝试对本段所述过程用人话总结：  
0. 要使用量子算法找到满足$A\vec{x}=\vec{b}$的$\vec{x}$；
1. 把单位向量$\vec{b}$转化为量子态$\|b\rangle=\sum_{i=1}^Nb_j\|i\rangle$，显然$\|i\rangle$应当以二进制串表示；
2. 对$\|b\rangle$执行Hamiltonian模拟，将$e^{iAt}$添加到$\|b\rangle$以构建关于不同时间$t$叠加。此处关于Hamiltonian的具体形式、添加$e^{iAt}$的具体方法、和$t$的出处存疑。
3. 基于**相位估计算法**，分解decompose$\|b\rangle$寻得A的特征值$\lambda_j$；
4. 依据特征值和特征向量，线性方程系统状态就可以表示为$\sum_{j=1}^N=\beta_j\|u_j\rangle\|\lambda_j\rangle$，显然此处$\beta_j$作系数；
5. 经过第2)步，分解$\|b\rangle$可得$A$的特征值，那么$\|b\rangle$就可以通过$A$的特征向量表示：$\|b\rangle=\sum_{j=1}^N\beta_j\|u_j\rangle$，此处$\beta_j$正是第4)步表示系统状态所需的；
6. 构建一个线性映射$\|\lambda_j\rangle\rightarrow C\lambda_j^{-1}\|\lambda_j\rangle$，$C$是正则化系数；
7. 解运算特征值寄存器$\|\lambda_j\rangle$；
8. 回顾系统状态第4)步，就变成了与$\sum_{j=1}^{N}\beta_j\lambda_j^{-1}\|u_j\rangle=A^{-1}\|b\rangle=\|x\rangle$成正比的值。     

矩阵求逆算法的执行中一个重要的因子是矩阵$A$的条件数$\kappa$，也就是$A$的最大最小特征值的比值。条件数越大，矩阵就越接近无法求逆，解$A^{-1}\|b\rangle=\|x\rangle$也就变得越不稳定。这样的矩阵被认为是病态的ill-conditionde。我们的算法一般会把$A$的奇异值singular values设置在$1/\kappa$到1之间，与之相等的有$\kappa^{-2}I\leq A\dagger A\geq I$。这种情况下，算法的运行时间就在$\kappa^2log(N)/\epsilon$这个数量级，此处$\epsilon$是由输出状态$\|x\rangle$而得的加性误差(additive error存疑，记“坑-6”)。因此，当参数$\kappa$和$1/\epsilon$都是N的多元对数poly-$log(N)$时，我们的算法拥有相比于经典算法的最大优势，达到指数级别加速。接下来我们还会就病态矩阵(ill-conditioned)讨论的处理技术做出一些讨论。   

该过程先对应的向量$\vec{x}$得出量子力学表示形式$\|x\rangle$，显然为了读出$\vec{x}$的全部组成需要执行该程序至少N次。事实上更常见的情况是相对于$\vec{x}$本身，我们对$\vec{x}^TM\vec{x}$的期望值更感兴趣，此处$M$是某些线性操作符(下面的内容会说明该过程同样允许(accomodate)非线性操作)。将$M$应受为量子力学操作符在执行对应于$M$的量子测量，就得到了对期望值的估计$\langle x\|M\|x\rangle=\vec{x}^TM\vec{x}$，正如我们所期望的(as desired)。这种方法可以提取(extract)向量$\vec{x}$包括正则化、状态空间不同部分的权重、矩(moments)等在内的许多(a wide variety of)特征。     

举一个简单例子，该算法可以用以查看两个不同的随机过程(stochastic process)是否拥有相似的稳定状态[8]。考虑一个随机过程
$\mathop{\vec{x}_t}=A\mathop{\vec{x}_{t-1}}+\vec{b}$，向量$\vec{x}_t$中的第$i$个坐标(coordinate)表示$t$时刻第$i$项的丰度(abandance)。该分布的稳定状态就表示成$\|x\rangle=(I-A)^{-1}\|b\rangle$(稳定状态：$x_t\equiv x_{t-1}$)。考虑另一个随机过程$\vec{x}_t^{'}=A^{'}\vec{x}_{t-1}^{'}+\vec{b^{'}}$，对应的稳定状态$\|x^{'}\rangle=(I-A^{'})^{-1}\|b^{'}\rangle$。为确定$\|x\rangle$和$\|x^{'}\rangle$是否相似，我们对它们执行SWAP测试。注意到(note that)找出两个概率分布是否相似需要至少$O(\sqrt{N})$个样本[10]。     
  
该算法的优势(strength)就在于其仅需要$O(log\,N)$的昆比特寄存器就可以运行，且不必写出全部的$A,\,\vec{b},$或$\vec{x}$。在Hamiltonian模拟和非幺正步骤仅需(incur)最高(overhead)N的多对数ploy-$log\,N$的情况下(接下来有详细描述)，相比经典计算机我们的算法可以指数级别地节省时间，甚至只需要经典计算机写出这些输出的时间。某种意义而言(in that sense)，我们的算法和经典蒙特卡洛算法(Monte Carlo)相关，后者通过在来自N个目标的概率分布的样本、而非分布的全部N个组分上运行，获得极大的(dramatic)加速。然而尽管这些经典采样算法很强力，我们依然会证明一般来说(in gerneral)任何经典算法执行相同的矩阵求逆任务需要的时间都指数倍多于量子算法。   

***概述***：本篇文章剩余部分组织如下：首先详细描述算法，分析其运行时间并与已知最好的经典算法对比。然后证明(对一些复杂性理论的complexity-theoretic假设取模modulo)矩阵求逆的困难，这意味着我们的算法运行时间是几乎最优的，比任何经典算法都有指数倍的提速。最后对其应用、一般化generalizations和扩展的讨论进行总结。    
***相关工作***：此前已经有论文给出了在受限环境执行线性代数(linear algebraic)操作的量子算法，我们的工作将之扩展到解决非线性微分(differential)方程。    

## (II) ALGORITHM   
现在我们给出该算法更详细的解释。首先将一个给定的Hermitian矩阵$A$转化为可以在后面步骤操作的幺正操作符$e^{iAt}$。有可能给(比如)A是一个$s$-稀疏的行可计算(row computable)矩阵，这就意味着其每行有$s$个非零元素(entries)且给定行标这些元素都可以在$O(s)$时间内被计算。基于这种假设，引文[3]展示了怎样在时间$\tilde{O}(log(N)s^2t)$模拟$e^{iAt}$，此处$\tilde{O}$抑制了更缓慢增长的项([13]中有描述)。如果$A$非Hermitian矩阵，定义   
$$
C=\begin{pmatrix}0&A \\ A^\dagger&0\end{pmatrix}\tag{1}   
$$   
由于$C$是Hermitian矩阵，我们可以解方程$C\vec{y}=\begin{pmatrix}\vec{b}\\\\0\end{pmatrix}$以得到$y=\begin{pmatrix}0\\\\ \vec{x}\end{pmatrix}$。当有必要时可以使用这种reduction，本篇的剩余部分均假定$A$是Hermitian矩阵。   

我们还需要一个高效的过程准备$\|b\rangle$。比如如果$b_i$和$\sum_{i=i_1}^{i_2}\|b_i\|^2$都是有效可算的，我们可以使用引文[14]中的过程去准备$\|b\rangle$，或者(Alternatively)，我们的算法可以作为一个更大的量子算法的子程，该主程中的其他组分component负责产生$\|b\rangle$。   

下一步是在特征向量基上分解$\|b\rangle$，可以使用[相位估计算法](https://oucliuxiang.github.io/2020/03/11/Paper_Reading-BOOK-Quantum-Computation-and-Quantum-Information/#v-2-phase-estimation)[5,6,7]。$u_j$表示$A$(也就是$e^{iAt}$)的特征向量，$\lambda_j$表示相应的特征值。对于较大的$T$，令  
$$
\Phi_0\rangle := \sqrt{\frac{2}{T}}\sum_{\tau=0}^{T-1}\sin \frac{\pi(\tau+\frac12)}{T}|\tau\rangle   
\tag{2}
$$   
(此处注意上式所有的包括$T,\tau, \Phi_0$都是第一次出现，与此前包括分解$\|b\rangle$在内的分析均无联系，则其意义是？)   

$\|\Phi_0\rangle$系数的选择应最小化误差分析部分的一个确定的二次损失函数(引文[13]有详细介绍)。    

接下来我们应用条件哈密顿演化conditional Hamiltonian evolution $\sum_{\tau=0}^{T-1}\|\tau\rangle\langle\tau\|^C\otimes e^{iA\tau t_0/T}$于$\|\Phi_0\rangle^C\otimes\|b\rangle$，此处$t_0=O(\kappa\epsilon)$。对第一寄存器进行[傅里叶变换](https://oucliuxiang.github.io/2020/03/11/Paper_Reading-BOOK-Quantum-Computation-and-Quantum-Information/#v-1-the-quantum-fourier-transform)得到状态   
$$
\sum_{j=1}^{N}\sum_{k=0}^{T-1}\alpha_{k|j}|k\rangle|u_j\rangle,  \tag{3}
$$   
此处$\|k\rangle$是傅里叶基态，当且仅当$\lambda_j\approx\frac{2\pi k}{t_0}$时$\|\alpha_{k|j}\|$会很大。定义$\tilde{\lambda}_k:=\frac{2\pi k}{t_0}$，可以重标记寄存器$\|k\rangle$以获得：   
$$
\sum_{j=1}^N\sum_{k=0}^{T-1}\alpha_{k|j}\beta_j|\tilde{\lambda}_k\rangle|u_j\rangle   
$$   
添加辅助(ancilla)昆比特并以$\|\tilde{\lambda}_k$为条件旋转，得到：   
$$
\sum_{j=1}^N\sum_{k=0}^{T-1}\alpha_{k|j}\beta_j|\tilde{\lambda}_k\rangle|u_j\rangle\left(\sqrt{1-\frac{C^2}{\tilde{\lambda_k^2}}}|0\rangle+\frac{C}{\tilde{\lambda_k^2}}|1\rangle\right),   
$$  
此处$C=O(1/\kappa)$。现在撤销相位估计解运算$\|\tilde{\lambda}_k\rangle$。如果相位估计算法足够优秀，会有当$\tilde{\lambda}_k=\lambda_j$时$a_{k|j}=0$，其余情况等于零。那么现在就能得到：  
$$
\sum_{j=1}^N\beta_j|u_j\rangle\left(\sqrt{1-\frac{C^2}{\lambda_j^2}|0\rangle+\frac{C}{\lambda_j}|1\rangle}\right)
$$  
为完成取逆操作我们测量最后一个昆比特，设观测到1，则得到状态：   
$$
\sqrt{\frac{1}{\sum_{j=1}^NC^2|\beta_j|^2/|\lambda_j|^2}}
\sum_{j=1}^N\beta_j\frac{C}{\lambda_j}|u\rangle
$$   
对应于correspond to $\|x\rangle=\sum_{j=1}^n\beta_j\lambda_j^{-1}|u_j\rangle$的正则化。正则化因子可以由观测到1的概率确定。最后对$M$进行测量，其期望值$\langlex\|M\|x\rangle$对应于我们希望评估的$\vec{x}$的特征。   

