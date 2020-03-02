---
layout:     post
title:      Series Articles of Machine Learning -- 03
subtitle:   Bayesian Classification
date:       2020-02-26
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Machine Learning, Bayesia, Classification
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