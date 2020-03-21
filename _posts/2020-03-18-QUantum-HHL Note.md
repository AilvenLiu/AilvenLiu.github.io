---
layout:     post
title:      Series Articles of Quantum Machine Learning  -- 04
subtitle:   Quantum Algorithm For Linear System, by Harrow etc.
date:       2020-03-18
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - En2Ch Translation
    - Quantum Machine Learning
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

> Re-understand HHL follow the [qutumist](https://www.qtumist.com/post/5372)   

## (I) Inputs And Outputs of HHL Algorithm   
A classical solving linear equation problem requires a $n\times n$ matrix and $n$-dimision vector $\vec{b}$ as inputs and outputs a $n$-dimision vector $\vec{x}$ satisfying $A\vec{x}=\vec{b}$ as the following picture **Figure-1** illustrating:   
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/Quantum-book-06.png" alt="Figure-1" width="550"/>    

The HHL itself has some restraints:  
1. **The requirements for inputs $A$ and $\vec{b}$**: Firstly it demands the $n\times n$-dimision matrix $A$ is a Hermitian matrix (i.e. the conjugate transpose of A is equiavlent to itself), and $\vec{b}$ is an unit vector. If $A$ is not Hermitian, the paper also provides method to construct Hermitian matrix. The part of algorithm's input is labeled by the red box in **Figure-1**, in which $\vec{b}$ is stored in the bottom register and $A$ exists as a component of unitary operator of phase estimation.    
2. **The form of output $\vec{x}$**: The part of algorithm's output is labeled by the blue box in **Figure-1**, and is still stored in the bottom register (i.e. output $\vec{x}$ is in the same register as input $\vec{b}$). What the bottom register stores is a quanutm state containing vector $\vec{x}$, where the word "contains" means we are unable to read out the concrete value of $\vec{x}$. However an overall feature of $\vec{x}$ is allowed to achieve, for example it is feasible to get an evaluation of the expectional value of $\vec{x}$: $\vec{x}^TM\vec{x}$ through a operator $M$. This is also a limitation of HHL algorithm. It is not necessary to extract the concrete value of $\vec{x}$ for many applications, and under this circumstance HHL is relative efficient.   

## (II) The Key Idea Of HHL Algorithm   
When reading the process of HHL algorithm, we can find the key idea of HHL, that is **extract the proportion**. In the following context we'll explore its specific meaning.  

As it illustrated in **Figure-2**, HHL and lots of its derivative algorithms can be divided as three subprocedures: **phase extimation**, **controlled rotation**, and **inverse phase estimation**. The inverse phase estimation is the inverse calculation of phase estimation (regarding the subroutine of phase estimation as matrix $U_{PE}$, the inverse phase estimation can be regarded as the inversion of matrix $U_{PE}$).  
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/Quantum-book-07.png" alt="Figure-2" width="550"/>   

When the phase estimaton has existed, then the key and fantastic site of HHL is in the middle part (labeled by blue box in **Figure-2**): controlled rotation, also named **extract the proportion**.   

Considering the Hermitian matrix $A$, which can be decomposed into the formula shown at the yellow bottom in **Figure-2**. $A$ has its eigenvalue $\lambda_j$ and corresponding eigenvector $u_j$. After phase estimation, a series of eigenvalues $\lambda_j$ will be stored into the basis state $\|\lambda_j\rangle$ in the middle register, while the input $\vec{b}$ stored in the bottom register will be decomposed in the feature space of $A$ and represented as $\|b\rangle=\sum_j\beta_j\|u_j\rangle$. Recall a skill used in the second stage of phase estimation that extract the values into basis state which are stored in probability amplitude originally. We here in the second stage of HHL intruduce the similar but inverse skill that extract the values stored originally in basis state into probability amplitude.  

Considering the controlled rotation (labeled by blue box in **Figure-2**), in HHL algorithm the controlled rotation achieves to extract the reciprocal of basis state value into the probability amplitude of corresponding basis state proportionally, through a ancilla qubit. The last sentance will be explained step by step below, taking putting an elephant in the refrigerator as example.  

1. **First, open the refrigerator**   
   Designing a function $f(\lambda_j)$ about the basis state $\|\lambda_j\rangle$ ($f(\lambda_j)=1/\lambda_j$ in HHL). For this example, regard the refrigerator as quantum register and stores a series of superopsition of basis state $\|\lambda_j\rangle$, Then regard the action of opening the refrigerator as "opening" the $\|\lambda_j\rangle$ in register and take the value $\lambda_j$ out as independent variable. Finally design a function $f(j)$ about this independent variable $\lambda_j$.   
 
2. **Second, put the elephant in**   
   Add an ancilla qubit initializted to $\|0\rangle$.  
   Design a mapping $R$ (a controlled rotation as shown in the blue box in **Figure-2**): it mapping the ancilla qubit from basis $\|0\rangle$ to superposition $\|0\rangle+\|1\rangle$, and extract the value of function $f(\lambda)$ into the probability amplitude of basis state $\|1\rangle$ in the meanwhile, shown as following:  
   $$
   R(|0\rangle)=\sqrt{1-f^2(\lambda_j)}|0\rangle+f(\lambda_j)|1\rangle
   $$   
   For this example, we regard the ancilla qubit as elephant, with $\|0\rangle$ as its initialized value; while the action of "put it in" as controlled rotation $R$. After putting elephant into refrigerator, the state of elephant $\|0\rangle$ is changed in the superposition $\|0\rangle+\|1\rangle$.   

3. **Third, close the refrigerator**  
   Measure the ancilla qubit, and when the meaturement result is $\|1\rangle$, the original register changes from the superposition of a series of $\|\lambda_j\rangle$ to the superposition of $f(\lambda_j)\times\|1\rangle$.   
   For this elephant example, the action of "close the refrigerator" is regard as measuring the ancilla qubit (the elephant). This action results in the collapse of ancilla qubit which collapses from the superposition $\|0\rangle+\|1\rangle$ to basis state $\|1\rangle$. The whole register (refrigerator) collapses from the superposition of a series of $\|0\rangle$ into superposition of a series of $f(\lambda_j\times\|1\rangle)$.   

After the above three steps, the values in basis state $\|\lambda_j\rangle$ is extracted into the probability amplitude of this basis state, in proportional. 

## (III) The Algorithm Procedure Of HHL  
As we have known that a HHL Algorithm consists of three subroutines that are separately the phase estimation (marked by the dotted line in **Figure-3**), controlled rotation, and the inverse phase estimation. The corresponding quantum circuit is given in **Figure-3**.  
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/Quantum-book-08.png" alt="Figure-3" width="550"/>.  

For the brevity of description, the ancilla qubit in the top row is marked as "first register"; the middle row is called "second register"; and the bottom's which accommodates the input $|b\rangle$ is called "third register".  The following series of figures illustrate the key step of HHL:  
1. **Step 1**
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/Quantum-book-10.png" alt="Figure-4" width="550"/>  

In the first step, we prepare $\|$