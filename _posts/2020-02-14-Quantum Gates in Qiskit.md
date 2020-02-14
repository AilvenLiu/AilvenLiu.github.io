---
layout:     post
title:      Quantum Gates in Qiskit
subtitle:   Series Articles of Quantum Coding in Python -- 03
date:       2020-02-14
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

> Understanding the quantum gates is essential for learning quantum circuit.  


## Basic States  
A basic qubit $\alpha|0\rangle+\beta|1\rangle$ which satisfied $|\alpha|^2+|\beta|^2=1$ can be represented as vector:
$$
\alpha|0\rangle+\beta|1\rangle = 
    \begin{pmatrix}
        \alpha \\ \beta
    \end{pmatrix}, \,\,\,\,
\alpha, \beta \in \mathcal{C}, \\
| a + bi| = \sqrt{a^2 + b^2}
$$     
A quantum state consisted of two qubits $\alpha|00\rangle+\beta|01\rangle+\gamma|10\rangle+\eta|11\rangle$ which satisified $|\alpha|^2+|\beta|^2+|\gamma|^2+|\eta|^2=1$ can be represented as vector: 
$$
\alpha|00\rangle+\beta|01\rangle+\gamma|10\rangle+\eta|11\rangle$ = 
    \begin{pmatrix}
        \alpha \\ \beta \\ \gamma \\ \eta
    \end{pmatrix}, \,\,\,\,
\alpha, \beta, \gamma, \eta \in \mathcal{C}.
$$     
Then a doubel-qubits state such as $|10\rangle$ are represented as $\begin{pmatrix}0\\0\\1\\0\end{pmatrix}.$  <br>

Based on the aboves we define some commonly used states as follow: <br>  

$$
|0\rangle=\begin{pmatrix} 1\\ 0\end{pmatrix}, \,\,\,\,
|1\rangle=\begin{pmatrix} 0\\ 1\end{pmatrix}, \\
$$
$$
|+\rangle=\frac{1}{\sqrt{2}}\left(|0\rangle+|1\rangle\right)=\frac{1}{\sqrt{2}}\begin{pmatrix} 1\\ 1\end{pmatrix}, \\
$$
$$
|-\rangle=\frac{1}{\sqrt{2}}\left(|0\rangle-|1\rangle\right)=\frac{1}{\sqrt{2}}\begin{pmatrix} 1\\ -1\end{pmatrix}, \\
$$
$$
|\circlearrowright\rangle=\frac{1}{\sqrt{2}}\begin{pmatrix}1\\i\end{pmatrix}, \,\,\,\,
|\circlearrowleft\rangle=\frac{1}{\sqrt{2}}\begin{pmatrix}1\\-i\end{pmatrix}.
$$  

## The Pauli Operator  
The simplest quantum gates are the Paulis: $X$(NOT), $Y$ and $Z$(Phease Flip). Their action is to perform a half rotation of the Bloch sphere around the x, y and z axes.   
$$
X = \begin{pmatrix}0&1\\1&0\end{pmatrix},\,\,\,\, 
\begin{matrix}
X|0\rangle=|1\rangle, \\ X|1\rangle=|0\rangle,
\end{matrix}
$$
$$
Y = \begin{pmatrix}0&-i\\i&0\end{pmatrix},\,\,\,\,
\begin{matrix}
Y|\circlearrowright\rangle=\,\,\,\,|\circlearrowright\rangle, 
\\ Y|\circlearrowleft\rangle=-|\circlearrowleft\rangle,
\end{matrix}
$$
$$
Z = \begin{pmatrix}1&0\\0&-1\end{pmatrix},\,\,\,\,
\begin{matrix}
Z|+\rangle=|-\rangle, \\
Z|-\rangle=|+\rangle. 
\end{matrix}
$$

## Hadamard and S   
Hadamard is also a half rotation of Bloch sphere. The difference is that it rotates around a axis located halfway between x and z. This gives it the effect of rotating states that pointing along the z axis to those pointing along x. Vice versa.<br>
$$
H = \frac{1}{\sqrt{2}}\begin{pmatrix}1&1\\1&-1\end{pmatrix}, 
\begin{matrix}
H|0\rangle=|+\rangle,&H|+\rangle=|0\rangle, \\
H|1\rangle=|-\rangle,&H|-\rangle=|1\rangle.
\end{matrix}
$$  
$S$ and $S^\dagger$ gates are quanter turns of the Bloch sphere around the z axis, and so can be regard as two possible square roots of $Z$ gate ($\sqrt{Z}$): <br>
$$
S = \begin{pmatrix}1&0\\0&i\end{pmatrix}:\,\,\,\,
\begin{matrix}
S|+\rangle = |\circlearrowright\rangle, \,\,\,\,
S|\circlearrowright\rangle = |-\rangle, \\
S|-\rangle = |\circlearrowleft\rangle, \,\,\,\,
S|\circlearrowleft\rangle = |+\rangle.
\end{matrix}
$$
$$
S^\dagger = \begin{pmatrix}1&0\\0&-i\end{pmatrix}:\,\,\,\,
\begin{matrix}
S^\dagger|+\rangle = |\circlearrowleft\rangle, \,\,\,\,
S^\dagger|\circlearrowleft\rangle = |-\rangle, \\
S^\dagger|-\rangle = |\circlearrowright\rangle, \,\,\,\,
S^\dagger|\circlearrowright\rangle = |+\rangle.
\end{matrix}
$$  
```python 
qc.s(0) # s gate on qubit 0  

qc.sdg(0) # s† on qubits0
```


## Other single-qubit gates  
Gates $X, Y, Z$ are separately rotations around the x, y, and z axes by a specific angle. More generally these concepts can be extended to rotations by a arbitrary angle $\theta$. That are gates $R_x(\theta), R_y(\theta), R_z(\theta)$ where the angle is expressed in radians. So the Pauli gates ($X, Y, Z$) corresond to $\theta=\pi$. Their square roots (such S of Z) require half this angle, $\theta=\pm\pi/2$. So on.<br>
```python 
qc.rx(theta, 0) # theta x rotation on qubit 0  

qc.ry(theta, 0) # theta y rotation on qubit 0  

qc.rz(theta, 0) # theta z rotation on qubit 0  

```

Considering the $Z=Z\dagger=\begin{pmatrix}1&0\\0&-1\end{pmatrix}, (-1 = e^{i\pi = e^{-i\pi}})$ and $S=\sqrt{Z}=\begin{pmatrix}1&0\\0&i\end{pmatrix}$, $S\dagger=\sqrt{Z}\dagger=\begin{pmatrix}1&0\\0&-i\end{pmatrix}$, two possible square roots of $S$, named $T$:
$$
T = \begin{pmatrix} 1&0 \\\\ 0&e^{i\pi/4}\end{pmatrix}, \, \, \, \, T^\dagger = \begin{pmatrix} 1&0 \\\\ 0&e^{-i\pi/4} \end{pmatrix}.
$$  
All single-qubit operations are compiled down to gates known as $U_1, U2$ , and $U_3$ before running real IBM quantum hardware. For that reason, they are sometimes called *physical gate*.<br>
$$
U_3(\theta, \phi, \lambda) = 
\begin{pmatrix}
    cos\frac{\theta}{2} & -e^{i\lambda}sin\frac{\theta}{2} \\\\
    e^{i\phi}sin\frac{\theta}{2} & e^{i\lambda+i\phi}cos\frac{\theta}{2}
\end{pmatrix}
$$
This is a operation running in a pauly sphere. Based on $U_3$, $U_1$ and $U_2$ can be defined as:<br>
$$
U_1(\lambda) = U_3(0, 0, \lambda) = 
\begin{pmatrix}1&0\\0&e^{i\lambda}\end{pmatrix} \\
U_2(\phi, \lambda) = U_3(\pi/2, \phi, \lambda) = 
\frac{1}{\sqrt{2}}\begin{pmatrix}1&-e^{i\lambda} \\ e^{i\phi}&e^{i\lambda+i\phi}\end{pmatrix}
$$


## Multiple gates   
* **C-NOT** 
changes the target qubit (the second) only when the controled qubit (the first) is $|1\rangle$. A 2-qubits state is composed from two qubits:   
$$
|0\rangle\otimes|1\rangle=
\begin{pmatrix}1\\0\end{pmatrix}
\otimes
\begin{pmatrix}0\\1\end{pmatrix} = 
\begin{pmatrix}1\times0\\1\times1\\0\times0\\0\times1
\end{pmatrix} = 
\begin{pmatrix}0\\1\\0\\0
\end{pmatrix}
$$
$$
CNOT = \begin{pmatrix}
1 & 0 & 0 & 0 \\ 0 & 1 & 0 & 0 \\ 0 & 0 & 0 & 1 \\ 0 & 0 & 1 & 0 
\end{pmatrix},\,\,\,\,\,
\begin{matrix}
CNOT|00\rangle = |00\rangle \\
CNOT|01\rangle = |01\rangle \\
CNOT|10\rangle = |11\rangle \\
CNOT|11\rangle = |10\rangle 
\end{matrix}
$$
$\,\,\,\,\,\,\,\,\,\,\,\,\,\,$
That $X$ (NOT) can be controlled, so do $Y$ and $Z$.

* **Toffoli**  
Being akin to CNOT, but Toffoli controlled on two qubits with another one qubit as target, which receives three qubits. Toffoli flips the last qubit only when the formers are $|11\rangle$, so that it can be represented as following matrix form:
$$
Toffoli = \begin{pmatrix}
1 &0 &0 &0 &0 &0 &0 &0 \\
0 &1 &0 &0 &0 &0 &0 &0 \\
0 &0 &1 &0 &0 &0 &0 &0 \\
0 &0 &0 &1 &0 &0 &0 &0 \\
0 &0 &0 &0 &1 &0 &0 &0 \\
0 &0 &0 &0 &0 &1 &0 &0 \\
0 &0 &0 &0 &0 &0 &0 &1 \\
0 &0 &0 &0 &0 &0 &1 &0 
\end{pmatrix}, \,\,\,\,
\begin{matrix}
Toffoli|000\rangle = |000\rangle, \\
Toffoli|001\rangle = |001\rangle, \\
Toffoli|010\rangle = |010\rangle, \\
Toffoli|011\rangle = |011\rangle, \\
Toffoli|100\rangle = |100\rangle, \\
Toffoli|101\rangle = |101\rangle, \\
Toffoli|110\rangle = |111\rangle, \\
Toffoli|111\rangle = |110\rangle. 
\end{matrix}
$$

* **SWAP**
As the the name means, SWAP gate swap two the qubits controlled by SWAP. 
$$
SWAP = \begin{pmatrix}
1 &0 &0 &0 \\ 0 &0 &1 &0 \\ 0 &1 &0 &0 \\ 0 &0 &0 &1
\end{pmatrix},\,\,\,\, 
\begin{matrix}
SWAP|00\rangle = |00\rangle, \\
SWAP|01\rangle = |10\rangle, \\
SWAP|10\rangle = |01\rangle, \\
SWAP|11\rangle = |11\rangle.  
\end{matrix}
$$

The above gates are called in qiskit by: 
```python 
qc.cx(0,1) # CNOT controlled on qubit 0 with qubit 1 as target

qc.ccx(0,1,2) # Toffoli controlled on qubits 0 and 1 with qubit 2 as target

qc.cy(0,1) # controlled-Y, controlled on qubit 0 with qubit 1 as target

qc.cz(0,1) # controlled-Z, controlled on qubit 0 with qubit 1 as target

qc.swap(0,1) # swap states between qubit 1 and 0
```