---
layout:     post
title:      Series Articles of Machine Learning -- 03
subtitle:   Bayesian Classification
date:       2020-02-26
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Machine Learning, Bayes, Classification
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

## (I) Bayesian Decision Theory  
Bayesian decision theory is the basic method to put decision into effect under probabilistic framework. Under the ideal circumstance of every relative probability is known, Bayesian decision theory considers how to select the best category labels based upon these probabilities and misclassified loss. The following paragraphes will explain its principle with the multiclassification task as example.   
Assuming there are N possible  category labels $\mathcal{Y} = \{c_1, c_2, \cdots, c_N\}$, and $\lambda_{ij}$ is the loss produced by classifying smaple correctly labeled by $c_j$ as $c_i$. Based on posterior probability $P(c_i|x)$, the *expected loss* produced by classifying sample $x$ as $c_i$, i.e., *conditional risk* of sample $x$, is written as:  
$$
R(c_i|x) = \sum_{j=1}^N\lambda_{ij}P(c_i|x).  \tag{1.1}
$$  
The target now is finding a descriminative principle: $h:\mathcal{X}\rightarrow\mathcal{Y}$ to minimizing the overall risk:  
$$
R(h) = E_x[\,R\,(\,h(\,x)|\,x)].    \tag{1.2}
$$    
Obveriously for each sample $x$, if $h$ is able to minimize the conditional rist $R( h(x) | x)$, the overall risk $R( h)$ is also minimized. The *Bayesian decision rule* then is drawn as: in order to minimize the overall risk, simply select the category label on each sample that minimizes the *conditinoal risk*, that is:   
$$
h^*(x) = \mathop{argmin}\limits_{c\in\mathcal Y} R(c|x),  \tag{1.3}   
$$   
now, $h^\*$ is called *Bayesian optimal classifier*, and the corresponding overall risk $R(h^\*)$ is called *Bayesia risk*. 
$1-R(h^\*)$ represents the best performance the classifier can reach, or the theoretical limit of model accurancy produced by machine learning. More particularly, if the target is minimizing error ratio of classification, the misclassified loss $\lambda_{ij}$ can be written as:  
$$
\lambda_{ij} = \left\{
\begin{aligned}
&0,\,\,\,\,if\,\, i=j; \\ &1, \,\,\,\,otherwise,
\end{aligned}
\right.  
\tag{1.4}
$$   
the conditional risk then is:  
$$
R(c|x) = 1-P(x|x),   \tag{1.5}
$$   
Therefore the Bayesia optimal classifier which minimizes the classification error ratio is:   
$$
h^*(x) = \mathop{argmax}\limits_{c\in \mathcal{Y}} P(c|x), \tag{1.6}
$$     
i.e. to select category label maximizing the posterior probability $P(c|x)$ for each smaple $x$.  
The precondition of using Bayesia descriminative principle to minimizing decision risk is get the posterior probability $P(c|x)$. It's difficult to obtain directly in particular. Consider from this view, what machine learning need to do is estimate posterior probability $P(c|x)$ based on limited training smaples as preciously as precise as possible. In general, there are two strategies: given $x$ and predict $c$ by directly modeling for $P(c|x)$, which is *discriminatinve models*; or, model joint probability $P(x, c)$ firstly and then the posterior probability $P(c|x)$ is obtained, which is *generative models*. It's obveriously, models such as "Decision Tree", "BP networks", "SVM" and so on can be categorized as descriminative models. For generative models, we must consider:  
$$
P(c|x) = \frac{P(x, c)}{P(x)}. \tag{1.7}
$$     
Based on Bayes theorem, the above equation is:  
$$
P(c|x) = \frac{P(c)P(x| c)}{P(x)}, \tag{1.8}
$$   
in which, $P(c)$ is prior probability; $P(x| c)$ is *class-conditional probability* of sample $x$ to class label $c$, also called *"likelihood"*; $P(x)$ is evidence factor used for uniformization. Given sample $x$, the evidence factor $P(x)$ is unrelated to class label. Thus the problem of estimate $P(c|x)$ becomes how to estimate prior probability $P(c)$ and likelihood $P(x |c)$ based on training data $D$.    
The prior probability $P(c)$ represents the precentage each class of samples occupied. According to the law of large numbers, when there are enough independent and indentical samples involved in training set, $P(c)$ can be estimated by the frequency of various classes of samples. The class-conditional probability $P(x|c)$ is ought to be estimated by *"Maximum Likelihood"* method.   

## (II) Maximum Likelihood Estimation   
Assuming the class-conditional probability $P(x|c)$ has a definite form and is uniquely determined by paremeter vector $\theta_c$. For clarity, the primary $P(x|c)$ is donated as $P(x|\theta_c)$, and targets is estimate parameter $\theta$ by training dataset D.  
In particular, the probability model's training process is process of parameter estimation. For parameter estimation, two schools of statistics provide their different solutions that: Frequentist consider the parameters have objective fixed values though they are unknown, so that the can be determined by some principles such as optimizing likelihood function; Bayesian consider the paremeters are unobserved random variables, abiding by a sort of distribution, thus its posterior probability can be calculated by the observed data after assuming parameters abiding by a kind of prior distribution. *Maximum Likelihood Estimation (MLE)* is a classical method to estimate probability distribution of parameters based on datas sampling and sourced from Frequentist.    
Now let the set consituted by smaples of class $c$ represented by $D_c$, and assume these samples are distributed independently and identically, then the likelihood of parameter $\theta_c$ to dataset $D_c$ is:  
$$
P(D_c|\theta_c) = \Pi_{x\in D_c}P(x|\theta_c).  \tag{2.1}  
$$   
Estabilish the *log-likelihood*:  
$$
LL(\theta_c) = log\,\,P(D_c|\theta_c) = \sum_{x\in D_c}log\,\,P(x|\theta_c).   \tag{2.2}
$$   
Parameter $\theta_c$ then has its maximum likelihood estimation $\hat{\theta}_c$:  
$$
\hat{\theta}_c = \mathop{argmax}\limits_{\theta_c} LL(\theta_c).  
\tag{2.3}
$$   

## (III) Naive Bayes Classifier  
The primary obstacle of estimating posterior probability based on Bayse formula eq.$(1.8)$ is that, class-conditional probability $P(x | c)$ is a joint probability on all attributes, and is hard to directly estimate from limited smaples. The *naive Bayes classifier* uses *attribute conditional independence assumption* to avoid the obstacle. It assumes all attributes are independent for known categories, in other words, assumes each attribute affects the classified result independently. Based on the above assumption, the formula eq.$(1.8)$ can be re-written as:  
$$
P(c|x) = \frac{P(c)P(x|c)}{P(x)} = \Pi_{i=1}^d P(x_i|c),  \tag{3.1}  
$$     
in which, $d$ is the number of attributes, $x_i$ is the value of $i^{th}$ attribute of $x$. $P(x)$ is equal for all categories, therefore based on formula eq.$(1.6)$, the Bayes descriminative principle is:  
$$
h_{nb}(x) = \mathop{\argmax}\limits_{c\in \mathcal{Y}} P(c)\Pi_{i=1}^d P(x_i|c), \tag{3.2}
$$    
which is the expression of *naive Bayes classifier*. The training process of naive Bayes classifier is that estimate class-prior probability $P(c)$ based on training set $D$, and estimate conditional probability $P(x_i|c)$ for each attribute.   
Let $D_c$ represents the set of class $c$ in training set, and when there are abundant independent and identical sampels, the class-prior probability can be easily estimated:  
$$
\begin{aligned}
& P(c) = \frac{|D_c|}{|D|}, \\
& P(x_i|c) = \frac{|D_{x_i,c}|}{|D_c|},       
\end{aligned} \tag{3.3}
$$    
in which, $\|D_{x_i,c}\|= \frac{\|D_{x_i,c}\|}{\|D_c\|}$ represents the set consituted by samples whose value is $x_i$ in the $i^{th}$ attribute among $D_c$.    
For continuous attribute, $\|D_{x_i,c}\|$ is calculated by probability density function. Assume $p(x_i|c)~\mathcal{N}(\mu_{c,i}, \sigma_{c,i}^2)$, in which $\mu_{c,i}, \sigma_{c,i}^2$ are separately the mean and varience of the $i_{th}$ attribute in class $c$:   
$$
p(x_i|c) = \frac{1}{\sqrt{2\Pi}\sigma_{c,i}}exp(-\frac{(x_i-\mu_{c,i}^2}{2\sigma_{c,i}^2}).  
$$     

We use the *watermelon dataset 3.0* ( from table 3.0, page.84, *Machine Learning -- Chi-H. Chou*) to train a naive Bayes classifier, and classifies the sample "test.01": 
$$ 
|色泽|根蒂|敲声|纹理|脐部|触感|密度|含糖率|好瓜|
|----|----|----|----|----|----|----|----|----|
|青绿|蜷缩|浊响|清晰|凹陷|硬滑|0.697|0.460|？|   
$$   

Firstly to estimate the class-prior probability $P(c)$:   
$$
\begin{aligned}
&P(好瓜=是) = \frac{8}{17} \approx 0.471, \\
&P(好瓜=否) = \frac{9}{17} \approx 0.529.
\end{aligned}
$$   
Estimate each attribute's conditional probability $P(x_i|c)$:   
$$
\begin{aligned}  
& P_{青绿|是}=P(色泽=青绿|好瓜=是)=\frac{3}{8}=0.375, \\
& P_{青绿|否}=\frac{3}{9}\approx 0.333, \\
& P_{蜷缩|是}=\frac{5}{8} = 0.625, \\
& P_{蜷缩|否}=\frac{3}{9}\approx 0.333, \\
& P_{浊响|是}=\frac{6}{8} = 0.750, \\
& P_{浊响|否}=\frac{4}{9}\approx 0.444, \\
& P_{清晰|是}=\frac{7}{8} = 0.875, \\
& P_{清晰|否}=\frac{2}{9}\approx 0.222, \\
& P_{凹陷|是}=\frac{6}{8} = 0.750, \\
& P_{凹陷|否}=\frac{2}{9}\approx 0.222, \\
& P_{硬滑|是}=\frac{6}{8} = 0.750, \\
& P_{硬滑|否}=\frac{6}{9}\approx 0.667, \\
& P_{密度:0.697|是} = p(密度=0.697|好瓜=是) = \frac{1}{\sqrt{2\pi}\cdot0.129}exp\left\( -\frac{(0.697-0.574)^2}{2\cdot0.129^2}\right\) \approx 1.959, \\
& P_{密度:0.697|否} = \frac{1}{\sqrt{2\pi}\cdot0.195} exp\left\( -\frac{(0.697-0.496)^2}{2\cdot0.195^2}\right\) \approx 1.203, \\
& P_{含糖:0.460|是} = \frac{1}{\sqrt{2\pi}\cdot0.101} exp\left\( -\frac{(0.460-0.279)^2}{2\cdot0.101^2}\right\) \approx 0.788, \\
& P_{含糖:0.460|否} = \frac{1}{\sqrt{2\pi}\cdot0.108} exp\left\( -\frac{(0.460-0.154)^2}{2\cdot0.108^2}\right\) \approx 0.066,  
\end{aligned}
$$     
And then:   
$$
\begin{aligned}    
&P(好瓜=是) \times P_{青绿|是} \times P_{蜷缩|是} \times P_{浊响|是} \times P_{清晰|是} \times P_{凹陷|是} \\
&\,\,\,\,\times P_{硬滑|是}\times P_{密度:0.697|是}\times P_{含糖:0.460|是} \approx 0.038,  \\\\
&P(好瓜=否) \times P_{青绿|否} \times P_{蜷缩|否} \times P_{浊响|否} \times P_{清晰|否} \times P_{凹陷|否} \\
&\,\,\,\,\times P_{硬滑|否}\times P_{密度:0.697|否}\times P_{含糖:0.460|否} \approx 6.80\times10^{-5}.   
\end{aligned}   
$$     
$0.038 > 6.80\times10^{-5}$, naive Bayes classifier classifies sample *"test.01"* as *"好瓜"*.     
What should note is, if one attribute in training set never appeared with a certain category, and still use the above original frequency-based method to estimate prior probability, the naive Bayes classifier will not work at all since the occurrance of zero value:  
$$
P_{清脆|是} = \frac08 = 0.  
$$   
Smoothing the value of probability with *"Laplace correction"*:  
$$
\hat P(c) = \frac{|D_c|+1}{|D|+N},    \tag{3.4}
$$     
$$
\hat P(x_i|c) = \frac{|D_{c,x_i}|+1}{|D_c|+N},    \tag{3.5}
$$    
in which, $N$ represents the number of possible category in training set, $N_i$ represents the number of possible value of $i_th$ attribute.   

## (Semi-naive Bayes Classifier)   
To be completed...