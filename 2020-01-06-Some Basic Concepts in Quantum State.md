---
layout:     post
title:      Some Basic Concepts in Quantum State
subtitle:   Series Articles of Quantum Coding in Python -- 02
date:       2020-01-05
author:     OUC_LiuX
header-img: img/quantum_IBM.png
catalog: true
tags:
    - Quantum Researching
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

> During writing my first passage of the series, I am hardly to understand its code and tutorial text, due to some concepts are unacquinted to me. Thus I start this passage to record these quantum concepts and try to explain them.   
> All explaination are from wiki&zhihu originally.

# Qubit (*"量子位"* in Chinese)
From [Zhihu](https://zhuanlan.zhihu.com/p/22407308).<br>
A binary bit is normally presented as 0 or 1, wheares a quantum bit (quibt) can be either 0/1 or their superposition: **$\alpha_1|0\rangle + \alpha_2|1\rangle$**, where $\alpha_1$ and $\alpha_2$ are different plurals.<br>
## Represent a qubit string?
For binary (or ternary, quaternary, ..., decimal...anyway, for all traditional system), representing N bit string needs very N bit space in memory. It does **not** work in quantum system, the latter requires $2^N$ plurals to represent:<br>
$$\alpha_1|0...0\rangle + \alpha_2|0...1\rangle + \cdots + \alpha_{2^N}|1...1\rangle$$ 
## Operators to manipulating qubits
The form of manipulation that receive a value (state) and then return a new value (state) is called "operator", when its target data are qubits, Representing with 'U'.
Based upon the dispersed time, a procedure on qubits string is shown below:<br>
$$S_1\stackrel{U_1}{\rarr}S_2\stackrel{U_2}{\rarr} \cdots \stackrel{U_k}{\rarr}S_{k+1}$$
Nature allows us within three limition to achieve operators on a independant physical system:
- **Linear**  
  That means, for each component of new quantum state $S_{n+1}$, it can be written as a linear superposition of all old quantum state S' components. For the example of a qubits string comprised of 2 qubit, considering the two states below:  
  $$S_t = \alpha_{t,1}|00\rangle + \alpha_{t,2}|01\rangle+ \alpha_{t,3}|10\rangle + \alpha_{t,4}|11\rangle$$
  $$S_{t+1} = \alpha_{t+1,1}|00\rangle + \alpha_{t+1,2}|01\rangle+ \alpha_{t+1,3}|10\rangle + \alpha_{t+1,4}|11\rangle$$
  At the time of __t+1__, its components are correlated with these from time **t** as the following relative:<br>
  $$\alpha_{t+1, 1} = \beta_1\alpha_{t, 1} + \beta_2\alpha_{t,2} + \beta_3\alpha_{t,3} + \beta_4\alpha_{t, 4}$$ 
  $$\alpha_{t+1, 2} = \gamma_1\alpha_{t,1}+ \gamma_2\alpha_{t,2}+ \gamma_3\alpha_{t,3}+ \gamma_4\alpha_{t, 4}$$
  $$\alpha_{t+1, 3} = \delta_1\alpha_{t,1}+ \delta_2\alpha_{t,2}+ \delta_3\alpha_{t,3}+ \delta_4\alpha_{t, 4}$$
  $$\alpha_{t+1，4} = \epsilon_1\alpha_{t,1} + \epsilon_2\alpha_{t, 2} + \epsilon_3\alpha_{t, 3} + \epsilon_4\alpha_{t,4}$$
  The linear relative can also be represented in a more simple matrix form:<br>
  $$
  \left[\begin{matrix}
      \alpha_{t+1, 1} \\  
      \alpha_{t+1, 2} \\
      \alpha_{t+1, 3} \\
      \alpha_{t+1, 4}
  \end{matrix}\right] = 
  \left[\begin{matrix}
      \beta_1 & \beta_2 & \beta_3 & \beta_4 \\
      \gamma_1& \gamma_2& \gamma_3& \gamma_4\\
      \delta_1& \delta_2& \delta_3& \delta_4\\
      \epsilon_1 & \epsilon_2 & \epsilon_3 & \epsilon_4
  \end{matrix}\right]
  \cdot
  \left[\begin{matrix}
      \alpha_{t, 1} \\  
      \alpha_{t, 2} \\
      \alpha_{t, 3} \\
      \alpha_{t, 4}
  \end{matrix}\right]
  $$
- **Reversibility**
  That means each quantum operator __U__ is reversible. when marking it as __V__:<br>
  $$ U(V(S)) = V(U(S)) = S$$
- **Keep inner product**  
  From [Schrodinger wave equation](https://en.wikipedia.org/wiki/Schr%C3%B6dinger_equation)<br>
  TO DO

## Measurement  
For an N-bits state S: $\alpha_1|00...0\rangle + \alpha_2|00...1\rangle+\cdots+\alpha_{2^N}|11...1\rangle$, now we add a qubit $|0\rangle$ to it, the state then be: 
$$
\alpha_1|\dot000...0\rangle + \alpha_2|\dot000...1\rangle+\cdots+\alpha_{2^N}|\dot011...1\rangle
$$
add a qubit $|1\rangle$ to it, the state then be:
$$
\alpha_1|\dot100...0\rangle + \alpha_2|\dot100...1\rangle+\cdots+\alpha_{2^N}|\dot111...1\rangle
$$
add a gernal type $\alpha_a|0\rangle+\alpha_b|1\rangle$, the state then be:
$$
(\alpha_a\alpha_1|\dot000...0\rangle+\alpha_b\alpha_1|\dot100...0\rangle) + 
(\alpha_a\alpha_2|\dot000...1\rangle+\alpha_b\alpha_1|\dot100...1\rangle) + \cdots + 
(\alpha_1\alpha_n|\dot011...1\rangle+\alpha_b\alpha_{2^N}|\dot111...1\rangle)
$$
The last form above can be written simple as: 
$(\alpha_a|0\rangle+\alpha_b|1\rangle)\bigotimes(\alpha_1|00...0\rangle + \alpha_2|00...1\rangle+\cdots+\alpha_{2^N}|11...1\rangle)$ 

## Hadamard gate


# Bell State 
From [wiki: Bell_State](https://en.wikipedia.org/wiki/Bell_state).
## Abstract

**Bell State** is comprised of two qubits that represent the simplest examples of Quantum Entanglement. Bell State is a form of entangled and normalized basis vector. Normalization implies the overall probility of particles in state is 1: $\langle\Psi|\Psi\rangle = 1$ Entanglement is a bisis-independent result of superposition. Due to the superposition, measurement of the qubits will collapse it into one of its basis states with a given probility. Because the Entanglement, measurement of one qubit will assign one of two possible values to the others instinctly, where the value assigned depends on which Bell state the two qubits are in. Bell state can be generalized to represent spectific quantum states of multi-qubit system, such as the [GHZ state](https://en.wikipedia.org/wiki/Greenberger%E2%80%93Horne%E2%80%93Zeilinger_state) for 3 sub-systems.

## Bell State
The Bell States are four specific maximally entangaled quantum states of two qubits. They are in a superposition of 0 and, i.e., a linear combination of the two states. Their entanglements means the following:<br>
The qubit held by A (subscript 'A' below) can be 0 as well as 1. If A measured her qubit in the standard basis, the outcome would be perfectly random, either possibilty 0 & 1 having probility 1/2. **BUT**, if B (subscript 'B' below) then measure his qubit, the outcome would be the same as the one A got. So that, if B measured, he would also get a random outcome on the first sight, but if A and B communicated, they would find out that althought their outcomes seemed random, they are perfectly correlated. <br>
The perfect correlation at a distance is special, i.e., may be the two particles agreed in advance when the pair was created( before the qubits were seperated), that outcomes they would show in case of a measurement.

## Bell basis
Four specific two-qubit state with the maximal of $2\sqrt2$ are designated as "Bell States". They are known as four maximally entangled two-qubit Bell states, forming a maximally entangled basis, known as the Bell basis, of the four dimensional Hilbert space for two qubits:  
$|\Phi^+\rangle=(|0\rangle_A\bigotimes|0\rangle_B)+(|1\rangle_A\bigotimes|1\rangle_B)$  
$|\Phi^-\rangle=(|0\rangle_A\bigotimes|0\rangle_B)-(|1\rangle_A\bigotimes|1\rangle_B)$  
$|\Psi^+\rangle=(|0\rangle_A\bigotimes|1\rangle_B)+(|1\rangle_A\bigotimes|0\rangle_B)$  
$|\Psi^-\rangle=(|0\rangle_A\bigotimes|1\rangle_B)-(|1\rangle_A\bigotimes|0\rangle_B)$  

## Create Bell State  
