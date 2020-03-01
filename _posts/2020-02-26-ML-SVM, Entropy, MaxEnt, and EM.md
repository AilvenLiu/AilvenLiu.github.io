---
layout:     post
title:      Series Articles of Machine Learning -- 02
subtitle:   SVM and SMO
date:       2020-02-26
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Machine Learning, SVM, SMO, Data Maining
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

## O, Support Vectors  
Given training simple set $D = {(x_1, y_1), (x_2, y_2),\cdots,(x_m, y_m)}, y_i\in{-1, +1}$, and the basic thought of classification learning is that, finding out a hyperplane in sample space based on the training set $D$, to split the disparate samples. The problem is, as fig-1 illustrating, which is the most suitable one during so many hyperplanes being able to split these samples.   
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ml-svm-01.png" alt="fig-01" width="280"/>  

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
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ml-svm-02.png" alt="fig-02" width="280"/>  

## (I) Solutions of Linear Hyperplane  
### I-1. Basic format of SVM  
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

### I-2. Establich dual problem model   
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
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ml-svm-03.png" alt="fig-03" width="480"/>   
As for the undivided linear sample space, a kernel function is needed to map onto another high dimension linear space. E.g. the XOR problem becomes separable after mapping it from a 2-D space into a suitable 3-D space. It's foutunately that there is always a higher dimisional feature space that makes the sample separable 
if the original dimision of sample space (number of features) is limited.<br>

Let $\phi(x)$ stands for the eigenvector after mapping $x$, therefore the model of hyperplane corresponding to the new feature space is:<br>
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
\tag{2.3}
$$    
$$
s.t.\,\,\,\,\sum_{i=0}^m\alpha_i y_i\,=\,0,\,\,\,\,
\alpha_i\geq0,\,\,\,\,i=1,2,\cdots,m.
$$  
Solving eq.($1.3$) involves the calculation of $\phi(x_i)^T\phi(x_j)$, which is the inner product after mapping sample $x_i$ and $x_j$ into higher dimisional feature space. It's difficult to compute $\phi(x_i)^T\phi(x_j)$ as the feature space's dimision may be very high, even infinite. To avoid this problem, constructing a function $\kappa$ as: <br>  
$$
\kappa(x_i,x_j)=<\phi(x_i), \phi(x_j)>=\phi(x_i)^T\phi(x_j)
\tag{2.4}
$$    
I.e., the inner product of $x_i$ and $x_j$ in their feature space is equal to the calculated result of function $\kappa$ in original space. After having such a function, the calculation of inner product in high or even inifinate dimision feature space. The dual problem described by eq.($2.3$) is then re-writen as:  
$$
\mathop{max}\limits_{\alpha}
\sum_{i=1}^m\alpha_i-\frac12\sum_{i=1}^m\sum_{j=1}^m
\alpha_i \alpha_j y_i y_j \kappa(x_i, x_j)
\tag{2.5}
$$    
$$
s.t.\,\,\,\,\sum_{i=0}^m\alpha_i y_i\,=\,0,\,\,\,\,
\alpha_i\geq0,\,\,\,\,i=1,2,\cdots,m.
$$   
Be akin to eq.($1.10$), the solution of eq.($2.5$) is:<br>  

$$
\begin{aligned}
f(x) = \,\,\,\,& w^T\phi(x)+b \\
=\,\,\,\,&\sum_{i=1}^m \alpha_i y_i \phi(x_i)^T \phi(x) + b \\
=\,\,\,\,&\sum_{i=1}^m \alpha_i y_i \kappa(x_i, x) + b.
\end{aligned}
\tag{2.6}
$$    

Eq.($2.6$) is called *support vector expansion*.  
What need to be pointed out is that, the most suitable form of kernel function is uncertain when eigenvector's unknown. The kernel function implicitly defines a feature space, therefore the inappropriate selection of kernel function means mapping sample into an inappropriate feature space, and that may cause a bad profermance. Some common kernels are listed below:<br>

|name|expression|parameters|
|:----|:----|:----|
|Linear kernel|$\kappa(x_i,x_j)=x_i^Tx_j$||
|Lolynomial kernel|$\kappa(x_i,x_j)=(x_i^Tx_j)^d$|$d\geq1$, the degree of polynomial|
|Gauss kernel|$\kappa(x_i,x_j)=exp(-\frac{\|x_i-x_j\|^2}{2\sigma^2})$|$\sigma>0$, the width of gauss kernel|
|Laplace kernel|$\kappa(x_i,x_j)=exp(-\frac{\|x_i-x_j\|}{\sigma})$|$\sigma>0$, the width of laplace kernel|
|Sigmoid kernel|$\kappa(x_i,x_j)$=$tanh(\beta x_i^Tx_j+\theta)$|$tanh$ is hyperbolic tangent function, $\beta>0, \theta<0$|
  
<br>

* Polynomial kernel degenerates as linear kernel when $d=0$.
* Gauss kernel is also called *RBF kernel*.
* Trying Gauss kernel firstly at unknown conditions is suggested.
* Kernel functions can also be:   
  1. linear combination of different kernels:  
  $\,\,\,\,\,\lambda_1\kappa_1 + \lambda_2\kappa_2$  
  2. direct product:  
  $\,\,\,\,\kappa_1\otimes\kappa_2(x,z)=\kappa_1(x,z)\kappa_2(x,z)$  
  3. combination of arbitrary functions:  
  $\,\,\,\,\kappa(x,z)=g(x)\kappa_1(x,z)g(z)$  

## (III) Soft Margin and Nornalization    
The above discussion always assumes the training samples are linear divided in sample space or feature space, i.e. the spliting hyperpline is existed to separa samples which belong to different classes. However it is difficult to determine a suitable kernel function making the training sampes linear separable in feature space in particular tasks. To say the least, even if we find a very kernel that allows samples linear divided in feature space, it is hard to argue the seemingly approciate hyperpline is not caused by overfitting. One of the ways to alleviate this problem is allowing SVM to fail in some samples. That is *soft margin*, as shown in fig-04:   
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ml-svm-04.png" alt="fig-04" width="280"/>    

More concretely, the form of SVM intruduced above requires the whole samples satisfying constraint $(0.2)$, i.e. every sampels are obliged to divided correctly, called *hard margin*. Whereas *soft margin* allows some sampels ignoring the above constraint:  
$$
y_i(w^Tx_i+b)\geq 1.  \tag{3.1}
$$   

And of caurse, at the meantime of maximizing margin, the samples which do not meet constraint are obliged to be as few as possible. Hence the optimizational target is written as:  
$$
\mathop{min}\limits_{w,b}\frac12 |w|^2 + C\sum_{i=1}^m l_{0/1}(y_i(w^Tx_i+b)-1), \tag{3.2}  
$$   

in which $C>0$ is a constant, $l_{0/1}$ is $0/1 \,\,loss \,\,function$:  
$$
l_{0/1} (z) = 
\left\{
\begin{aligned}
1,\,\,\,\,&if\,\,\,\, z < 0; \\ 0, \,\,\,\,&otherwise.    
\end{aligned}
\right.
\tag{3.3}
$$   

Obveriously, when $C$ tends to infinity, the equation eq.$(3.2)$ forces all samples to match constraint eq.$(3.1)$, in this case, the eq.($3.2$) is equal to eq.($1.1$) (inequation $y_i(w^Tx_i+b)-1$ works for every samples); when $C$ is a limited value, the equation eq.$(3.2)$ allows some of samples go against thet constraint.   
The current problem is, $\ell l_{0/1}$ is not convex or concave, and discontinuous. These unsatisfactory mathematical properties make it difficult to solve eq.($3.2$) direcly. Replacing $\ell l_{0/1}$ with
other functions called *surrogate loss* is a common method. The surrogate loss functions have more ideal mathematic properties, such as they are usually convex and continuous, and the upper bound of $\ell l_{0/1}$. There are three commonly used surrogate loss shown in fig-05:   
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/ml-svm-05.png" alt="fig-04" width="280"/>    

1. hinge loss: $\ell l_{hinge}(z) = max(0, 1-z); \tag{3.4}$   
2. exponential loss: $\ell l_{exp}(z) = e^{-z}; \tag{3.5}$  
3. logistic loss: $\ell l_{log}(z) = log(1+e^{-z}); \tag{3.6}$  

To be continued...

 
### SMO and Quadratic Programma  
Fixing parameters besides $\alpha_i$，extreme of $\alpha_i$ can be figured out. However $\alpha_i$ can be derived by others provided that the latter are fixed, as the constraint $\sum_{i=1}^m\alpha_iy_i=0$. Therefore SMO selects two variabels $\alpha_i$ and $\alpha_j$ each time, fixes other parameters. After parameters initailized, SMO runs the following two steps until converging:<br>
1. Selecting a pair of new varialbes $\alpha_i$ and $\alpha_j$ need to be updated;
2. Fixing other paramrters, solving eq.($1.9$) to get updated $\alpha_i$ and $\alpha_j$.  
Notifying the object function reudces as long as one amoung $\alpha_i$ and $\alpha_j$ uncomplies with KKT conditions eq.$(kkt.2)$, and the large the degree of KKT conditions is disobeyed, the large the reductive amplitude of object function is. 