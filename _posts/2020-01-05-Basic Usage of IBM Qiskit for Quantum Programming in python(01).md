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
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/quantum_GHZ.png" alt="GHZ" width="200"/>  

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
Conceptions such as CNOT, H gate are introduced in the next [blog](./2020-01-06-Some%20Basic%20Concepts%20in%20Quantum%20State.md)
.   

Then the complete quantum circuit consisted of quantum and classical registers can be simulated by simulators from Aer backend or real quantum computer po\roviders or cloud simulators. Setting backend, establishing job and excutting it, getting result from the above job, is one of the whole simple procedure to run a quantum circuit: <br>  

```python
backend_qasm = Aer.get_backend('qasm_simulator')  
job_qasm = execute(circ, backend_qasm, shots=1024)  
# where the shots is iterations the circuit runs, default as 1024  

result_qasm = job_qasm.result() 
counts_qasm = result_qasm.get_counts()
```  

Result returns us a series of pinpoint and full massages, though we always pay more attention into the valuables, such as the counts of occurences of each possible states:<br>  
\* *print(result_qasm)*: <br>
Result(backend_n ...... **(0x0=501, 0x7=523)**, header=....) <br>
\* *print(counts_qasm)*:<br>
**{'000': 501, '111': 523}**  <br>

***Applying and use real quantum computer via IBMQ*** <br>
It's so exciting that IBM provides real quantum computation resource and cloud simulator to users, which require only a IBM account. Copy personal API Token from [IBM-Q-Account page](https://quantum-computing.ibm.com/account) and  `IBMQ.save_account('API TOKEN')` to save the TOKEN into this computer. `IBMQ.load_account()` (without parameters) to load it after saving or `IBMQ.enable_account('API TOKEN')` to enable the Token this time to use IBM quantum resource. <br>
Let we see the quantum providers we can use: <br>
```python 
IBMQ.load_account()
# IBMQ.enable('API TOKEN')   

provider = IBMQ.get_provider(group='open')
provider.backends()
```  
It returns us: <br>
[<IBMQSimulator('ibmq_qasm_simulator') from IBMQ(hub='ibm-q', group='open', project='main')>,  
 <IBMQBackend('ibmqx2') from IBMQ(hub='ibm-q', group='open', project='main')>,  
 <IBMQBackend('ibmq_16_melbourne') from IBMQ(hub='ibm-q', group='open', project='main')>,  
 <IBMQBackend('ibmq_vigo') from IBMQ(hub='ibm-q', group='open', project='main')>,  
 <IBMQBackend('ibmq_ourense') from IBMQ(hub='ibm-q', group='open', project='main')>,  
 <IBMQBackend('ibmq_london') from IBMQ(hub='ibm-q', group='open', project='main')>,  
 <IBMQBackend('ibmq_burlington') from IBMQ(hub='ibm-q', group='open', project='main')>,  
 <IBMQBackend('ibmq_essex') from IBMQ(hub='ibm-q', group='open', project='main')>,  
 <IBMQBackend('ibmq_armonk') from IBMQ(hub='ibm-q', group='open', project='main')>]  
 <br>

Sent our jobs into remote quantum computers or cloud simulators and minotor their states with *job_monitor* (from `qiskit.tools.monitor`:<br>
```python 
backend_qasm_real = provider.get_backend('ibmq_qasm_simulator')
job_qasm_real = execute(qc, backend=backend_qasm_real)
# job_qasm_real_id = job_qasm_real.job_id()  

job_monitor(job_qasm_real)

backend_essex = provider.get_backend('ibmq_essex')
job_essex = execute(qc, backend=backend_essex)
# job_essex_id = job_essex.job_id()  

job_monitor(job_essex)

backend_ourense = provider.get_backend('ibmq_ourense')
job_ourense = execute(qc, backend=backend_ourense)
# job_ourense_id = job_ourense.job_id()  

job_monitor(job_ourense)

backend_burlington = provider.get_backend('ibmq_burlington')
job_burlington = execute(qc, backend=backend_burlington)
# job_burlington_id = job_burlington.job_id()  

job_monitor(job_burlington)
```
Job monitors will print real-time status involving   
*Job Status: job is being validated*   
*Job Status: job is actively running*   
*Job Status: job is queued(13)*   
Wait for a while or a long time, who know.<br>
*Job Status: job has successfully run*   

Get results and counts to plot histograms to observe the calculational difference with `plot_histogram()`, which returns object typed in **Figure**. Save figure via `Figure.savefig("/path/filename.png")`: 
```python 
result_qasm_real = job_qasm_real.result()
counts_qasm_real = result_qasm_real.get_counts(qc)

result_x2 = job_x2.result()
counts_x2 = result_x2.get_counts(qc)

result_melbourne = job_melbourne.result()
counts_melbourne= result_melbourne.get_counts(qc)

result_burlington = job_burlington.result()
counts_burlington = result_burlington.get_counts(qc)

qiskit_histogram_01 = plot_histogram([counts_qasm_real, counts_x2, counts_melbourne, counts_burlington], \
               legend=['qasm_real_Q', 'X2_Q', 'Ourense_Q', 'Melbourne_Q'])

qiskit_histogram_01.savefig("qiskit_histogram_01.png")  
```  
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/qiskit_histogram_01.png" alt="qiskit_histogram_01" width="300"/>  

It is same as all random calculational process that each time of running is different, so that recording a job id to retrieve the sepecific result is satisfied for a requirement of reproduction: <br>

```python
job_id_qasm = job_qasm.job_id()
job_monitor(job_qasm)
result_qasm = job_qasm.result()
counts_qasm = result_qasm.get_counts(qc)
plot_histogram(counts_qasm) 
```


```python
retrieve_job_qasm_1 = backend_qasm_Q.retrieve_job(job_id_qasm)
retrieve_result_job_qasm = retrieve_job_qasm_1.result()
retrieve_counts_job_qasm = retrieve_result_job_qasm.get_counts()
plot_histogram(retrieve_counts_job_qasm)
```

## Different between QuantumCircuit(QuantumRegister(a), ClassicalRegister(b)) and QuantumCircuit(a,b)  
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
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/qiskit_circ_1.png" alt="circ_1" width="200"/><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/qiskit_circ_2.png" alt="circ_2" width="200"/>  

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
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/qiskit_meas_3.png" alt="meas_3" width="200"/><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/qiskit_meas_4.png" alt="meas_4" width="200"/> 

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
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/qiskit_qc_13.png" alt="qc_13" width="200"/><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/qiskit_qc_14.png" alt="qc_14" width="200"/><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/qiskit_qc_23.png" alt="qc_23" width="200"/><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/qiskit_qc_24.png" alt="qc_24" width="200"/>  

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

## About the IBMQ Account  
### Functions from IBMQ  
* `enable_account(TOKEN)`: Enable your account in the current session. <br>
* `save_account(TOKEN)`: Save account to disk for future use.
* `load_account()`: Load account using stored credential
* `disable_account()`: Disable account in the current session.
* `stored_accoount()`: List the accounts stored to disk.
* `activate_account()`: List the account currently in the session .
* `delete_account()`: Delete the saved accounts from disk.
    
### The provider and backends  
Get provider and list backends via  
```python 
provider = IBMQ.get_provider(**kwargs)  
# parameters can be (hub="ibm-q", group="open", ..), it's inessential 

provider.backends() # to list  

```

We have the following devices/simulator to use:   
* **ibmq_qasm_simulator**  
    *cloud simulator*, 32 qubits  
* **ibmqx2**  
    *real device*,  5 qubits  
* **ibmq_16_melbourne**  
    *real device*, 15 qubits  
* **ibmq_vigo**  
    *real device*,  5 qubits  
* **ibmq_ourense**  
    *real device*,  5 qubits  
* **ibmq_london**   
    *real device*,  5 qubits  
* **ibmq_burlington**   
    *real device*,  5 qubits  
* **ibmq_essex**   
    *real device*,  5 qubits  

`configuration()` returns a backend's definite configure, such as available basic gates, max_experiments, max_shots, qubits number, and many so on.  `status()` returns us the most valuable information that how many jobs in line (item *pending_jobs*).   
 
### The least busy 5 qibits device  
```python 
from qiskit.providers.ibmq import least_busy
small_devices = provider.backends( filters = lambda x: \
    x.configuration().n_qubits == 5 and \
    not x.configuration().simulator)
least_busy(small_devices)
```

# Quantum Gates in Qiskit

## Basic States  
A basic qubit $\alpha|0\rangle+\beta|1\rangle$ which satisfied $|\alpha|^2+|\beta|^2=1, |a+bi|=\sqrt{a^2+b^2}$ can be represented as vector:    

$$
\alpha|0\rangle+\beta|1\rangle = 
    \begin{pmatrix}
        \alpha \\ \beta
    \end{pmatrix}, \,\,\,\,
\alpha, \beta \in \mathcal{C}
$$     

A quantum state consisted of two qubits $\alpha|00\rangle+\beta|01\rangle+\gamma|10\rangle+\eta|11\rangle$ which satisified $|\alpha|^2+|\beta|^2+|\gamma|^2+|\eta|^2=1$ can be represented as vector: 
$$
\alpha|00\rangle+\beta|01\rangle+\gamma|10\rangle+\eta|11\rangle = 
    \begin{pmatrix}
        \alpha \\ \beta \\ \gamma \\ \eta
    \end{pmatrix}, \,\,\,\,
\alpha, \beta, \gamma, \eta \in \mathcal{C}.
$$     
Then a doubel-qubits state such as $|10\rangle$ are represented as
$$\begin{pmatrix}0\\0\\1\\0\end{pmatrix}$$   
  
Based on the aboves we define some commonly used states as follow:   

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
\end{matrix}\\  
$$  

$$
Y = \begin{pmatrix}0&-i\\i&0\end{pmatrix},\,\,\,\,
\begin{matrix}
Y|\circlearrowright\rangle=\,\,\,\,|\circlearrowright\rangle, 
\\ Y|\circlearrowleft\rangle=-|\circlearrowleft\rangle,
\end{matrix}\\
$$  

$$
Z = \begin{pmatrix}1&0\\0&-1\end{pmatrix},\,\,\,\,
\begin{matrix}
Z|+\rangle=|-\rangle, \\
Z|-\rangle=|+\rangle. 
\end{matrix}\\
$$

## Hadamard and S   
Hadamard is also a half rotation of Bloch sphere. The difference is that it rotates around a axis located halfway between x and z. This gives it the effect of rotating states that pointing along the z axis to those pointing along x. Vice versa.   

$$
H = \frac{1}{\sqrt{2}}\begin{pmatrix}1&1\\1&-1\end{pmatrix}, 
\begin{matrix}
H|0\rangle=|+\rangle,&H|+\rangle=|0\rangle, \\
H|1\rangle=|-\rangle,&H|-\rangle=|1\rangle.
\end{matrix}
$$  
$S$ and $S^\dagger$ gates are quanter turns of the Bloch sphere around the z axis, and so can be regard as two possible square roots of $Z$ gate ($\sqrt{Z}$):    

$$
S = \begin{pmatrix}1&0\\0&i\end{pmatrix}:\,\,\,\,
\begin{matrix}
S|+\rangle = |\circlearrowright\rangle, \,\,\,\,
S|\circlearrowright\rangle = |-\rangle, \\
S|-\rangle = |\circlearrowleft\rangle, \,\,\,\,
S|\circlearrowleft\rangle = |+\rangle.
\end{matrix} \\
$$
and    

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

Considering the 
$$
Z=Z\dagger=\begin{pmatrix}1&0\\0&-1\end{pmatrix}, 
(-1 = e^{i\pi} = e^{-i\pi})
$$
and   

$$ 
S=\sqrt{Z}=\begin{pmatrix}1&0\\0&i\end{pmatrix},\,\,\,\, 
S\dagger=\sqrt{Z}\dagger=\begin{pmatrix}1&0\\0&-i\end{pmatrix},
$$   

two possible square roots of $S$, named $T$:   

$$
T = \begin{pmatrix} 1&0 \\\\ 0&e^{i\pi/4}\end{pmatrix}, \, \, \, \, T^\dagger = \begin{pmatrix} 1&0 \\\\ 0&e^{-i\pi/4} \end{pmatrix}.
$$   
All single-qubit operations are compiled down to gates known as $U_1, U2$ , and $U_3$ before running real IBM quantum hardware. For that reason, they are sometimes called *physical gate*.  
$$
U_3(\theta, \phi, \lambda) = 
\begin{pmatrix}
    cos\frac{\theta}{2} & -e^{i\lambda}sin\frac{\theta}{2} \\\\
    e^{i\phi}sin\frac{\theta}{2} & e^{i\lambda+i\phi}cos\frac{\theta}{2}
\end{pmatrix}
$$   

This is a operation running in a pauly sphere. Based on $U_3$, $U_1$ and $U_2$ can be defined as:   

$$
U_1(\lambda) = U_3(0, 0, \lambda) = 
\begin{pmatrix}1&0\\0&e^{i\lambda}\end{pmatrix} 
\\
U_2(\phi, \lambda) = U_3(\pi/2, \phi, \lambda) = 
\frac{1}{\sqrt{2}}\begin{pmatrix}1&-e^{i\lambda} \\ e^{i\phi}&e^{i\lambda+i\phi}\end{pmatrix}
$$


## Multiple gates   
* **C-NOT** 
changes the target qubit (the second) only when the controled qubit (the first) is $|1\rangle$:  

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

A 2-qubits state is composed from two qubits:   

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
