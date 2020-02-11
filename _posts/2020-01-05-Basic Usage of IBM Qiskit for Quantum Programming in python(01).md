---
layout:     post
title:      Starting To Code Quantum Programme with IBM Qiskit(Basis)
subtitle:   Series Articles of Quantum Coding in Python -- 01
date:       2020-01-05
author:     OUC_LiuX
header-img: img/quantum_IBM.jpeg
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

> For the requirement of my graduation project and change of researching intrest, based upon the github forum of [IBM Qiskit](https://github.com/OUCliuxiang/qiskit-iqx-tutorials/blob/63f6d52747716d90c8f43eb16d110bcdd39dda05/qiskit) (and its [community](https://github.com/OUCliuxiang/qiskit-community-tutorials)) I start a quantum machine learning studying process since Dec.2019. However, I do not forgive deep learning at all due to Lu & Zhaoyang's project. Now I record all my studying experience here as a series articles for myself after that as well as others who have a urgent need about QML.
> 
> This is the first article of the series.

## Libraries used in this article
```python
import numpy as np  
from qiskit import (  
    IBMQ,  
    QuantumCircuit,  
    QuantumRegister,  
    ClassicalRegister,   
    Aer,  
    execute  
)  
from qiskit.visualization import plot_histogram, plot_state_city  
from qiskit.tools.monitor import job_monitor  
import matplotlib.pyplot as plt    
import matplotlib.image as mpimg  
```  

## Build a basic quantum circuit  
Here we create a basic quantum circuit comprised of three qubits.
```python
circ = QuantumCircuit(QuantumRegister(3),ClassicalRegister(3))  
# apply and alloc three quantum bits as register     
# parameters of QuantumCircuit():     
# QuantumRegister(int ), ClassicalRegister(int )     
# or (int, (int))     
# the only integer parameter stands for the qubit(s)    
```  

After creation, gate("operation") can be addded to manipuate registers. Considering the below operation:

$$
|\psi\rangle = \frac{|000\rangle+|111\rangle}{\sqrt{2}}.
$$  
Its circuit diagram is illustrated below:

![GHZ](https://github.com/OUCliuxiang/OUCliuxiang.github.io/tree/master/img/quantum_GHZ.png)

This is a quantum quantum circuit that makes a three qubit [GHZ](https://en.wikipedia.org/wiki/Greenberger%E2%80%93Horne%E2%80%93Zeilinger_state) state.  We'd know by default that each qubits in register is intilized to 
$\|0\rangle$.  
To make such GHZ state, we apply the following gates:  
1. A Hadamard gate H on qubit 0, which puts it into the superposition state 
   $$
   {\frac{|0\rangle + |1\rangle}{\sqrt{2}}}
   $$ 
2. A controlled-Not operation $\left(C_X\right)$ between qubit 0 and qubit 1.
3. A controlled-Not operation between qubit 0 and qubit 2.

On an ideal quantum computer, the state produced by running this circuit would be the GHZ state above.  
In IBM Qiskit operations can be added to circuit one by one, as shown below.

~~~python
circ.h(0)   
# add a H gate on qubit 0(this is index), putting it in superposition   
 
circ.cx(0, 1)   
# add a CX(C-NOT) gate on control qubit 0 and target qubit 1, putting    

# the qubits in Bell state. CAUTION: 'BELL' will be explain below   

circ.cx(0, 2)  
# add a CX(C-NOT) gate on control qubit 0 and target qubit 2, putting   

# the qubits in GHZ state.  

circ.barrier(range(3)) # add barrier between qubits operations and measures   

circ.measure(range(3), range(3)) 
# measure separately the qubuts into classical bits.   

~~~   
Then the complete quantum circuit consisted of quantum and classical registers can be simulated by simulators from Aer backend or real quantum computer po\roviders or cloud simulators. Setting backend, establishing job and excutting it, getting result from the above job, is one of the whole simple procedure to run a quantum circuit: <br>  
```python
backend_qasm = Aer.get_backend('qasm_simulator')  
job_qasm = execute(circ, backend_qasm, shots=1000)  
# where the shots is iterations the circuit runs, default as 1024  
result_qasm = job_qasm.result() 
```
Conceptions such as CNOT, H gate are introduced in the next [blog](./2020-01-06-Some%20Basic%20Concepts%20in%20Quantum%20State.md)
. 
  
#### Different between QuantumCircuit(QuantumRegister(a), ClassicalRegister(b)) and QuantumCircuit(a,b)  
In fact, the both forms can build a quantum circuit within registers consisted seperately of 'a' qubits and 'b' classical bits. The former which declears registers catecory and set the sepecific bits into registers, in which all bits are unique. Wheares the later, which puts just bits/qubits into registers, is not care about which specific bit/qubit it is in specific pisition. As the consequence, bits/qubits appointed circuits cannot be fused as one when the quantum circuit and measure circuit are established sequencely. Bits/qubits unappointed cicuits are able to be mearged. The Following examples illustrate the difference concretely: <br>  
```python
circ_1 = QuantumCircuit(QuantumRegister(3))
circ_1.h(0)
circ_1.cx(0, 1)
circ_1.cx(0, 2)

circ_2 = QuantumCircuit(3)
circ_2.h(0)
circ_2.cx(0, 1)
circ_2.cx(0, 2)

circ_1.draw(output='mpl')
circ_2.draw(output='mpl')
```  
Then the circ 1 & 2 are sequencely displayed as:   
<center class="half">
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/qiskit_circ_1.png" alt="circ_1" width="200px"/><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/qiskit_circ_2.png" alt="circ_2" width="200px"/>  
</center>
Watching carefully the label 'q' and 'q+number'.<br>  

```python
meas_3 = QuantumCircuit(QuantumRegister(3), ClassicalRegister(3))
meas_3.barrier(range(3))  
meas_3.measure(range(3), range(3))  

meas_4 = QuantumCircuit(3, 3)  
meas_4.barrier(range(3))  
meas_4.measure(range(3), range(3))  

circ_3.draw(output='mpl')  
circ_4.draw(output='mpl')  
```  
The measure circuits meas_3 and meas_4 are illustrated sequencely below, noticing the labels 'c' and 'c+number':   
![meas_3](https://github.com/OUCliuxiang/OUCliuxiang.github.io/tree/master/img/qiskit_meas_3.png)   
![meas_4](https://github.com/OUCliuxiang/OUCliuxiang.github.io/tree/master/img/qiskit_meas_4.png)  

```python
qc_13 = circ_1 + meas_3 # the order is irreversibel  
qc_14 = circ_1 + meas_4 # the order is irreversibel  
qc_23 = circ_2 + meas_3 # the order is irreversibel  
qc_24 = circ_2 + meas_4 # the order is irreversibel 

qc_13.draw(output='mpl')
qc_14.draw(output='mpl')
qc_23.draw(output='mpl')
qc_24.draw(output='mpl')
```  
Figures after circuits addition are illustrated in code sequence as follow:<br>  
![qc_13]([../img](https://github.com/OUCliuxiang/OUCliuxiang.github.io/tree/master/img)/qiskit_qc_13.png)  
![qc_14]([../img](https://github.com/OUCliuxiang/OUCliuxiang.github.io/tree/master/img)/qiskit_qc_14.png)  
![qc_23]([../img](https://github.com/OUCliuxiang/OUCliuxiang.github.io/tree/master/img)/qiskit_qc_23.png)  
![qc_24]([../img](https://github.com/OUCliuxiang/OUCliuxiang.github.io/tree/master/img)/qiskit_qc_24.png)  
It can be learn obveriously from the above four figures that quantum circuit merges with measure circuit only when bits/qubits in registers are not appointed.  <br>  

And finally, to build a complete cicuit with both quantum register and classical register, the following form is always recommended: <br>  
```python 
qr = QuantumRegister(3)
cr = ClassicalRegister(3)
qc = QuantumCircuit(qr, cr)
qc.h(0)
qc.cx(0, 1)
qc.cx(0, 2)
qc.barrier(range(3))
qc.measure(range(3), range(3))
```

