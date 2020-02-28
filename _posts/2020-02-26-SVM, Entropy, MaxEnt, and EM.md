---
layout:     post
title:      Series Articles of Machine Learning -- 02
subtitle:   XGBoost And Its Usage
date:       2020-02-26
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Machine Learning, SVM, Entropy, MaxEnt, EM, Data Maining
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

> Translated from *Machine Learning -- Chi-H. Zhou*.   

## (I) Support Vectors  
Given training simple set $D = {(x_1, y_1), (x_2, y_2),\cdots,(x_m, y_m)}, y_i\in{-1, +1}$, and the basic thought of classification learning is that, finding out a hyperplane in sample space based on the training set $D$, to split the disparate samples. The problem is, as fig-1 illustrating, which is the most suitable one during so many hyperplanes being able to split these samples.   
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ml-svm-01.png" alt="fig-01" width="200"/>  

Splited hyperplane can be discribed by the linear equation $\it{w^Tx+b} = 0$ in samples space. Where $\it{w}=(w_1, w_2, \cdots,w_d)^T$ is normal vector, which determines the direction of hyperplane; $\it{b}$ is bias item, determining the distance between hyperplane and origin. Hyperplane now can be donated as $(\it{w}, b)$, meanwhile the distance between hyperplane $(\it{w}, b)$ and any points $x$ in samples space can be written as: <br>  

$$
r = \frac{|w^Tx + b|}{||w||}.  \tag{0.1}
$$  

Assuming hyperplane $(\it{w}, b)$ classifies training samples correctly, i.e., for all $(x_i, y_i)\in D$, $w^Tx_i+b>0$ when $y_i = +1$ wheraes $w^Tx_i+b<0$ when $y_i = -1$, let: <br>  

$$
\left\{
\begin{aligned}
w^Tx_i+b\geq 1, & y_i=+1; \\
w^Tx_i+b\leq 1, & y_i=-1. 
\end{aligned}
\right.
\tag{0.2}
$$  

Then as illustrated in fig-02, these sample points closest to the hyperplane hold the equal sign in the above equation, and they are called *Support Vector*. Distance sum between two opposite support vectors and hyperplane is $\gamma=\frac{2}{||w||}$, and it is also called *margin*.  <br>
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ml-svm-02.png" alt="fig-02" width="200"/>  

## Solutions of Linear Hyperplane  
### 1. Basic format of SVM  
The primary target is finding the hyperplane with *maximum margin*, in other words, finding parameters $w$ and $b$ satisfying the constraint of $(0.2)$ maximizing $\gamma$. I.e.:<br>   
$$
\begin{aligned}
&\mathop{max}\limits_{w,b}\,\,\,\, \frac{2}{||w||} \\
&s.t.\,\,\,\,\,\,\,\, y_i(w^Tx_i+b)\geq 1, i = 1,2,\cdots,m.
\end{aligned}
\tag{1.1}
$$     
   
To maximize the margin, maximizing $||w||^{-1}$ is the only requirement, and is equal to minimize $||w||^w$. So that the above formula $(1.1)$ can be re-writen as   <br>  
$$
\begin{aligned}
&\mathop{min}\limits_{w,b}\,\,\,\, \frac12||w||^2 \\
&s.t.\,\,\,\,\,\,\,\, y_i(w^Tx_i+b)\geq 1, i = 1,2,\cdots,m.
\end{aligned}
\tag{1.2}
$$  

This is the basic foumat of SVM (*Support Vectoc Machine*).  

### 2. Establich dual problem model   
We expect to find hyperplane model by resolving eq. $(1.2)$: <br>  
$$
f(x) = w^Tx+b, \tag{1.3}
$$  
  
in which $w$ and $b$ are model's parameters. Notifying that eq $(1.2)$ is a convex quadraatic problemming itself, it can be resolved by existed optimization computational packages, but more efficient methods are expected.  
* **KKT conditon when inequality constraints exist**
  $$
  \left\{
      \begin{aligned}
      &\alpha_i\geq0;\\
      &y_if(x_i)-1\geq0;\\
      &\alpha_i(y_if(x_i)-1)=0.
      \end{aligned}
  \right.
  \tag{kkt.1}
  $$  

* **Considering the KKT condition**  
For inequality constraints $h(x_i)\leq0$ and its lagrange multipliers $\lambda_i$, the idea samples are expected to located at the border $h(x_i)=0$. When samples locate inside the constraint area, constraint will be noneffective. And when establishing lagrange multiplier function, the inequality constraint item $h(x)<0$ must be $\lambda\cdot h(x)$ but not $-\lambda\cdot h(x).$<br>
$$
\left\{
    \begin{aligned}
    h(x)<0, \,\,\,\,&\lambda=0 \\ h(x)=0, \,\,\,\,&\lambda>0
    \end{aligned}
\right.
\Longrightarrow\,\,\,\,
\lambda>0, \,\,\lambda h(x)=0
\tag{kkt.2}
$$

  
Using Lagrange Multiplier for formula $(1.2)$ will establish its *dual problem*. In particular, add lagrange multiplier $\alpha_i\geq 0$ to each constraint condition in eq.$(1.2)$, then the lagrange function of this problem is writen as: <br>  

$$
\mathcal L(w,b,\alpha) = \frac12||w||^2+\sum^m_{i=1}\alpha_i(1-(y_i(w_Tx_i+b))), \tag{1.4}
$$  
in which, $\alpha=(\alpha_1, \alpha_2, \cdots, \alpha_m)$. Then let the partial derivative $\frac{\partial \mathcal L}{\partial w} = 0$ and $\frac{\partial \mathcal L}{\partial b} = 0$, the reults are: <br>      
   
$$
w=\sum_{i=1}^m\alpha_iy_i. \tag{1.5}   
$$  
$$
0=\sum_{i=1}^m\alpha_iy_i. \tag{1.6}
$$   

Plugging eq.$(1.5)$ and eq.$(1.6)$ into eq.$(1.4)$, $w$ and $b$ are eliminated:  <br>  
$$
\begin{aligned}
\mathcal L \,\,\,\,&= 
\frac12\sum_i\alpha_ix_iy_i\cdot\sum_j\alpha_jx_jy_j +\sum_i\alpha_i \\
&-\sum_i\sum_j\alpha_ix_iy_i\cdot \alpha_jx_jy_j -b\sum_i\alpha_iy_i \\
&=\sum_i\alpha_i 
-\frac12 \sum_i\sum_j\alpha_ix_i^Ty_i\cdot\alpha_jx_jy_j
\end{aligned}
\tag{1.8}
$$     
   
Then a lagrange problem described in eq.$(1.4)$ become dual:<br>  
  
$$
\begin{aligned}
&\mathop{min}\limits_{w,b}\mathop{max}\limits_{\alpha} \mathcal{L(w,b,\lambda)} \Longrightarrow\\  
&\mathop{max}\limits_{\alpha} \sum_{i=1}^m\alpha_i - \frac12\sum_{i=1}^m\sum_{j=1}^m\alpha_i\alpha_jy_iy_jx_i^Tx_j
\\
&s.t. \,\,\,\,\sum_{i=1}^m \alpha_iy_i=0,\,\,\,\,
\alpha_i\geq0, i = 1,2,\cdots,m.
\end{aligned} 
\tag{1.9}
$$    
The final SVM model about $w$ and $b$ after resolving $\alpha$ only related to support vectors:<br>
$$
f(x)=w^Tx+b=\sum_{i=1}^m\alpha_iy_ix_i^Tx_i+b. \tag{1.10}
$$    
Notifying that eq.$(1.9)$ is a quadratic problemma, that means its scale is proportional to the size of training samples, which will bring huge computational cost in practice. To avoid this problem, algorithms like **SMO** (*Sequential Minimal Optimization*) are raised.  
After getting parameters $\alpha$ and $w$, the next task is bias parameter $b$. Notifying the equation $y_sf(x_s)=1$ makes sense for arbitrary support vectors $(x_s, y_s)$:<br>  
$$
y_s\left( \mathop{\sum}\limits_{i\in S}\alpha_iy_ix_i^Tx_s+b \right)=1,  
\tag{1.11}
$$  
in which $S=\{\,i\,|\,\alpha_i>0, \,\,i=1,2,\cdots m\}$ is the subscript set of all support vectors. In theory, arbitrary support vector can be used to solve eq.$(1.11)$ to figure out $b$, while in particular the more robust method is use the average results of all support vectors: <br>  
$$
b=\frac{1}{|S|}\mathop{\sum}\limits_{s\in S}
\left\{
y_s-\mathop{\sum}\limits_{i\in S}\alpha_iy_ix_i^Tx_s
\right\}
\tag{1.12}
$$  

## (II) Non-linear Problem and Kernel Method  
More generally in particular, primary sample space is not linearly separable, such as an XOR problem as fig-03 shown: <br>  
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ml-svm-03.png" alt="fig-03" width="380"/>   
As for the undivided linear sample space, a kernel function is needed to map onto another high dimension linear space. E.g. the XOR problem becomes separable after mapping it from a 2-D space into a suitable 3-D space. It's foutunately that there is always a higher dimisional feature space that makes the sample separable 
if the original dimision of sample space (number of features) is limited.<br>

Let $\phi(x)$ stands for the feature vector after mapping $x$, therefore the model of hyperplane corresponding to the new feature space is:<br>
$$f(x)=w^T\phi(x)+b, \tag{2.1}$$  
in which, $w$ and $b$ are parameters. Be akin to eq.$(1.2)$ we have: <br>
$$
\begin{aligned}
&\mathop{min}\limits_{w,b}\,\,\,\,\frac12||w||^2 \\
&s.t.\,\,\,\,\,\,\,\,\,y_i(w^T\phi(x_i)+b)\geq 1, i = 1,2,\cdots,m.
\end{aligned}
\tag{2.2}
$$   
Its lagrange dual problem is: <br>
$$
\mathop{max}\limits_{\alpha}
\sum_{i=1}^m\alpha_i-\frac12\sum_{i=1}^m\sum_{j=1}^m
\alpha_i \alpha_j y_i y_j \phi(x_i)^T \phi(x_j)
\tag{1.3}
$$  

$$
\begin{aligned}
s.t.\,\,\,\,&\sum_{i=0}^m\alpha_i y_i\,=\,0,\\
&\alpha_i\geq0,\,\,\,\,i=1,2,\cdots,m.
\end{aligned}
$$  
Solving eq.($1.3$) involves the calculation of $\phi(x_i)^T\phi(x_j)$, which is the inner product after mapping sample $x_i$ and $x_j$ into higher dimisional feature space. It's difficult to compute $\phi(x_i)^T\phi(x_j)$ as the feature space's dimision may be very high, even infinite. To avoid this problem, assuming a function $\kappa$ like: <br>  
$$
\kappa(x_i,x_j)=<\phi(x_i), \phi(x_j)>=\phi(x_i)^T\phi(x_j)
\tag{1.4}
$$

 
### SMO and Quadratic Programma  
Fixing parameters besides $\alpha_i$，extreme of $\alpha_i$ can be figured out. However $\alpha_i$ can be derived by others provided that the latter are fixed, as the constraint $\sum_{i=1}^m\alpha_iy_i=0$. Therefore SMO selects two variabels $\alpha_i$ and $\alpha_j$ each time, fixes other parameters. After parameters initailized, SMO runs the following two steps until converging:<br>
1. Selecting a pair of new varialbes $\alpha_i$ and $\alpha_j$ need to be updated;
2. Fixing other paramrters, solving eq.($1.9$) to get updated $\alpha_i$ and $\alpha_j$.  
Notifying the object function reudces as long as one amoung $\alpha_i$ and $\alpha_j$ uncomplies with KKT conditions eq.$(kkt.2)$, and the large the degree of KKT conditions is disobeyed, the large the reductive amplitude of object function is. 