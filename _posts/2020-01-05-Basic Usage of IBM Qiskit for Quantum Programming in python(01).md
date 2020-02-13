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

