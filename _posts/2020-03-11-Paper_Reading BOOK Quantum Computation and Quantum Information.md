---
layout:     post
title:      Series Articles of Paper Reading & Translation -- 03
subtitle:   Quantum Computation and Quantum Information, by Michael etc.
date:       2020-03-11
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Paper Reading
    - Quantum Machine Learning
    - Quantum 
    - Quantum Computation
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

> Parts of Translation of Classical Quantum Computation Book [**"Quantum Computation and Quantum Information"**](https://github.com/OUCliuxiang/PaperReading/tree/master/QML/)

## (I) Fundamental concepts (part)    
> 科学提供了这个时代最勇敢的形而上学。它是一种彻底的人类构想，我们梦想、然后努力去发现、去解释、又继续去梦想，正是如此的信念驱动了科学的不懈前进。也正是这种信念促使我们不断踏足新的领域，世界因此以某种方式更加清晰明了，而我们将掌握宇宙真正的奇异之处。这些奇异之处终将被证明有某些关联，而且意义非凡。  
> Science offers the boldest metaphysics of the age. It is thoroughly human construct, driven by the faith that if we dream, press to discover, explain, and dream again, thereby plunging repeately into new terrain, the world will somehow come clearer and we will grasp the true strangeness of the universe. And the strangeness will all prove to be connected, and make sense.   
> Edward O. Wilson    

量子计算和量子信息的基本概念指的是什么？这种基本概念又是怎样发展的？它们会被用于什么地方？它们在这本书中将会被怎样地呈现？引言章节的目的正是通过粗略地描述量子计算和量子信息的发展前景去回答这些问题。本章节希望传达对于该领域核心概念的基本理解，展望其发展前景，帮助读者决定如何完成本书的剩余部分。   

本章节从1.1小节开始，讲述量子信息和量子计算的历史脉络。剩下的每个小节会给出一个或几个基本概念的简短描述，分别是：量子比特(1.2)，量子计算机、量子门和量子电路(1.3)，量子算法(1.4)，量子信息处理实验(1.5)，量子信息和量子通信(1.6)。   

在此过程中(Along the way)，我们会使用本章提到的基础数学方法介绍一些说明性的和易于理解的发展状况，比如量子隐态传输(quantum teleportation)和简单的量子算法。这些展示内容是独立的(self-contained)，设计地即使没有计算机科学或物理学基础也可以轻松理解。随着逐步地前进，在后面的章节中我们也将给出对于更加深入的讨论的指示，当然也包括了进一步阅读的参考和意见。   

如果你读到了感觉难以理解的地方，略过它跳到一个读起来舒服的地方即可。在这些方面上我们不得不使用一些技术术语(technical lingo)，最本书的最后会做出详细解释。先接受这些不理解的地方，当详细地理解了所有技术的细节后再回过头来看。第一张的重点是大局，细节部分将后续补充。  

### (I-1) Global presectives   
量子计算和量子信息是可以在量子力学系统中完成的信息处理任务的研究。这听起来相当地简单和显而易见，事实真的如此吗？和许多简单却深刻的想法一样，很长一段时间没有人考虑过使用量子力学系统处理信息。为了了解为什么出现这种情况，我们必须回到过去研究对量子信息和量子计算贡献了基础思想的各个领域——量子力学，计算机科学，信息理论和密码学(cryptography)。读者需要分别以物理学家、计算机科学家、信息理论学家和密码学家的角色去思考以在不同角度理解这个领域。   

#### (I-1.1) History of quantum computation and quantum information     
故事开始在二十世纪，当时科学界正经历一场未为人知(unheralded)的革命，物理学领域出现了一系列危机。问题开始于彼时的物理理论(现在被称为(dubbed)经典物理学)做出了一系列荒谬的预测(absurdity, n. 荒谬；谬论；荒谬的言行)比如含有无限能量的紫外线大灾难(ultraviolet catastrophe)，或电子无情地(inexorably)螺旋进入(spiraling into)原子核。起初，这些问题可以通过在经典物理学中添加特例(*ad hoc*)假设(hypothesis)来解决，但是随着对原子(atom)和辐射(radiation)的理解日益深入，这些尝试性的解释变得愈益错综复杂(convoluted)。经历了进四分之一个世纪的混乱(turmoil)后，在1920年代，危机来了，一个崭新的现代理论*量子力学*在这场危机中诞生了。自诞生之日起，量子力学就成为科学体系不可或缺(indispensable)的一部分，在太阳内外一切领域取得巨大成功(has been applied with enormous sucess to everything under and inside the Sun)，包括原子结构、恒星核聚变(nuclear fusion of stars)、超导体(superconductor)、DNA结构和自然界基本粒子(the elementary particles of Nature)。   

什么是量子力学？量子力学是一种构建(construction)物理理论的数学框架或规则集合。例如，有一种称为*量子电动力学(quantum electrodynamics)*的物理理论，极其精确地(with fantastic accuracy)描述了原子和光子之间的相互作用(interaction)，量子电动力学就是建立在量子力学框架上的，它包含了一些没有被量子力学确定的特殊规则。量子力学和特定物理理论比如量子电动力学之间的关系就像计算机操作系统和特定计算机应用软件之间的关联——操作系统设定了基本参数和操作模式，又给应用软件如何去完成特定任务留足了空间。   

量子力学的规则非常简洁，又但是即使是专家也会认为它是反直觉的(counterintuitive)，量子计算和量子信息的最早先兆(antecedent, 祖先)可能在物理学家更好的理解量子力学的长久渴望(long-standing desire)中被发现。最著名的量子力学批评家(critic)阿尔伯特爱因斯坦(Albert Einstein)直到去世也不认同(unreconciled with)这个由他所帮助缔造的理论。自量子力学诞生之日起一代又一代的物理学家一直在与之角力(wrestle with)，力图使量子力学的预测更容易被接受(palatable, adj, 美味的，可口的)。量子计算和量子信息的一个目标是开发(develop)令我们的直觉对量子力学更敏锐的工具，令它的预测对人类大脑更透明(trensparent, 透明的、坦率地、易懂的)。   

比如，在1980年代早期，人们对于能否利用量子效应(quantum effect)完成信号超光速传播产生了兴趣——根据爱因斯坦的相对论理论，这显然不可能(a big no-no)。这个问题的解答也转变成了能否能否克隆一个未知的量子态，也就是说构建(construct)一个量子态的副本。如果这种克隆是可行的，那么利用量子效应使信号比光还快也就成为可能。然而，克隆——在经典信息很容易完成的事情——在量子力学中却通常是不可能的。在1980年代早期发现的*不可克隆理论(no-cloning theorem)*是量子计算和量子信息的最早结论之一。自*no-cloning*理论诞生后人们又对它做出许多改进工作，现在我们有了概念工具，允许我们理解一个量子克隆设备(必然是不完善的, necessarily imperfect)可以工作得多好。这些工具反过来也被应用于理解量子力学的的其它方面。     

对量子计算和量子信息起到贡献作用的另一个历史分支(hestorical strand)是1970年代对于获得单个量子系统完全控制的兴趣。在1970年代之前(prior to the 1970s)对量子力学的应用通常包括对一个有大量量子力学系统组成的大样本(bulk sample)的总体级别(a gross level of)的控制，无一直接可行。比如超导电性(superconductivity)有一个极好的(superb)量子力学解释。然而由于一个超导体包含了大量(相比较于原子尺度而言)导体金属样本，我们只能证实其量子力学性质(nature)的极少几个方面，组成超导体的独立的量子系统依然是不可知的(inaccessible)。有一些系统比如粒子加速器(particle accelerators) 的确允许对独立量子系统进行有限的访问(access)，但依然极少提供对于组成系统(constitute system)的控制。   

自1970年代起，许多控制单原子的技术得到发展，例如已经开发出一种方法，使用“原子阱”(atom trap)捕获(trap)单原子中，将之隔离在其余世界(rest of the world)之外，这就允许我们以不可想象的精度对原子行为的许多不同方面加以证实。扫描隧道显微镜(scanning tunneling microscope)用以移动单个原子，依照设计者的意愿创造原子阵列。只涉及单个电子转移的电子设备也已被论证(demonstrate)可行。    

为什么以上种种努力都力图实现对单量子系统的完全控制？撇开(Setting aside)种种技术原因而专注于(constrating on)纯粹(pure)的科学，主要的原因是研究人员凭直觉(on hunch)做事。通常科学最深邃的见解(insight)来自于对于新的自然制度(regime)的证明方法的发展。例如1934-40年代射电天文学(radio astronomy)的发明导致了一系列惊人(a spectacular sequence of)的发现，包括银河系(Milky Way galaxy)星系核(galactic core)、脉冲星(pulsars)和类星体(quasars)。低温物理学在寻找降低不同系统温度的方法上取得了惊人的成功。类似地，通过获得单量子系统完全控制，我们正在探索未被触及(untouched)的自然状态(regime)以期发现新的意料之外的现象(phenomena)。我们正沿着这些方向(along these lines)迈出第一步，而且在这个体制(regime)已经发现了一些有趣的意外(interesting surprises)。那么在我们获取单量子系统的更复杂控制、并将之扩展到更复杂系统的过程中还会有什么其他发现吗？   

量子计算和量子信息对于近乎自然的适合此类问题(哪类问题？)。它们在不同级别的困难上为人类发明(devise)更好的操作单量子系统的方法、刺激新实验技术的发展、为最有趣的方向进行实验提供引导准则……等方面提供了一系列useful的挑战。相反地，如果我们要harness量子力学的力量应用到量子计算和量子信息，那么控制单量子系统的能力将必不可少。   

尽管有这强烈的兴趣，建立量子信息处理系统的努力已经resulted in了迄今为止的(to date)一定程度的(modest)成功。有能力在少量量子比特(昆比特qubits)进行一系列操作的小型量子计算机，展示了量子计算的最顶尖(the state of art)成果。实现*量子加密(quantum cryptography)*的实验原型(experimental prototypes)——一种长距离加密交流的方式——已经被论证，甚至已经在一定水平上应用到现实世界的某些任务上了。然而未来留给物理学家和工程师的挑战依然是巨大的：发展使大规模量子信息处理成为现实的技术。    

让我们把注意力从量子力学转移到20实际另一个伟大的智力大胜利(intellectual triumphs)，计算机科学。计算机科学的源头已经消弭在历史长河中。楔形(cuneiform)文字板表明，在汉谟拉比(Hammurabi)时期(circa 1750 B.C.大约公元前1750年)巴比伦人(Babylonians)已经发展出一些相当复杂的(sopsisticated)想法思想，而且似乎许多想法可以追溯到更早的时期。   

现代计算机的原型(incarnation, 化身，典型)由伟大的数学家阿兰图灵(Alan Turing)在1936年的一篇杰出论文中提出(announce)。图灵详细地developed了一个我们现在称之为可编程计算机的抽象概念，为了纪念图灵(in his honor)，这种计算模式也被称作*图灵机*。图灵表明(showed)有一种通用图灵机可以模拟一切其他图灵机。此外，他声称通用图灵机完整的掌握(capture)通过算法手段(mean)执行任务的含义(mean)。这也就是说，如果一个算法可以被某个硬件(可以理解为PC机)所执行，那么在通用图灵机也有一个对应的算法，可以实现与PC机上运行的算法所能完成的绝对相同的任务。这种论断(assertion)——被称为*丘奇图灵理论(Church-Turing thesis)*以纪念图灵和另一位计算机科学的先驱(pioneer)，Alonzo Church——断言(assert)了物理设备可以执行怎样的算法的物理概念和通用图灵机的严格的数学概念之间的等效性(equivalence)。这一理论广泛被接受，为丰富的计算机科学理论发展奠定了基础(laid the foundation)。  

在图灵的论文发表后不久，由电子组件构成的初代计算机结构得到了发展。约翰·冯·诺伊曼(John von Neumann)提出了一个简单的理论模型，模型描述了怎样把所有计算机需要的组件以实际风格组合在一起，使其具有全部通用图灵机的功能。硬件开始真正的高速发展(take off, 起飞)在1947年，晶体管的出现。自那之后，计算机硬件以惊人的速度(race)强力发展,以至于1965年被戈登摩尔(Gordon Moore)总结为了一条定律，后来被称为*摩尔定律(Moore's law)*，这条定律声明计算机能力在成本不变的情况下(for constant cost)每两年翻一番。   

自1960年代开始的几十年间，摩尔定律几乎未曾失效。然而大多数观察家(observers)预计(expect)这个美梦将在二十一世纪的头二十年结束。传统计算机制造(fabrication)方法(approaches)正开始面临(run up against)尺寸方面的根本(fundamental)困难。随着电子器件的制作越来越小，量子效应开始干扰(interfere)它们的功能(functioning)。  

这个有摩尔定律最终失效所导致(posed by)的问题的一个可能的解是转向不同的计算机范式(paradigm, n, 范例，词形变化表)。量子计算理论提供了一种范式，其基本思想是基于量子力学而不是经典物理学执行计算。事实表明(It turns out)尽管普通的(ordinary)量子计算机可以用来模拟量子计算机，但以有效的方式执行模拟看起来依然是不可能的。相比经典计算机，量子计算机提供了基本的(essential)速度优势。这种速度优势是非常巨大的，以至于许多研究者都坚信经典计算的任何可想像的进步(conceivable progress)都不足以克服经典计算机和量子计算机的算力之间的差距。  

对量子计算机的有效模拟和(versus, v.s.)无效模拟是什么意思？回答这个问题的许多核心理念(notions)实际在量子计算机这一概念提出之间就有了。特别地，在计算复杂度领域，有效和无效算法的思想在数学上被精确化了。大概来讲，一个有效的算法可以在问题规模的多项式时间内解决问题；相反地，低效算法则需要超多项式(通常是指数级别)时间。值得注意的是在1960年代末70年代初，图灵机计算模型看起来好像(as though)至少和任何其他计算模型一样有效，从这种意义(in the sense that)看来，任何可以被其他计算模型解决的问题通过使用图灵机模拟其他计算模型，也都可以被图灵模型有效地解决。这种观察被总结(condified into)为一种加强的(strengthened)丘奇图灵理论：  
*<center> 图灵机可以有效地模拟任何算法过程 </center>*
*<center> Any algorithmic process can be simulated rfficiently using a Turing machine. </center>*    

强化丘奇图灵理论强调的关键是*有效的efficiently*一词。如果强化丘奇图灵理论是正确的，那就意味着无论我们在什么类型的机器上执行算法，这个机器都都以使用图灵模型进行模拟。这是一个非常重要的加强，因为这指示了为了分析一个给定的计算任务是否可以被有效地完成，我们可以自我限定在图灵机模型的计算上。   

强化丘奇图灵理论的一类挑战来自于模拟计算(analog computation)领域，自图灵之后，许多不同的研究团队注意到某些类型的计算机可以有效地解决曾被认为在图灵机上没有有效解决方案的问题。最初看来(at first glance)这些模拟计算机似乎(appear to)违反了(violate)丘奇图灵理论的强化形式。很不幸的是对于模拟计算，事实表明当对模拟计算机中存在的噪声做出现实的假设时，在所有已知的例子(in all known instances)中他们的能力都将消失；他们不再能解决图灵机无法有效解决的问题。这个教训(lesson)——在评估(evaluate)计算模型有效性时现实噪声的影响必须考虑在内(take into account)——是量子计算和量子信息早期一个巨大的挑战；*量子纠错编码(quantum error-correcting codes)*和*容错量子计算(fault-tolerent quantum computation)*理论成功应对了这一挑战(a challenge met by ...)。因此不同于模拟计算(analog computation)，量子计算原则上讲(in principle)可以容忍有限量(a finite amount)的噪声依然保持其计算优势。   

强化丘奇图灵理论的第一个较大挑战出现在(arose)1970年代中叶，其时 Robert Solovay和Volker Strassen展示了使用*随机化算法(randomized algorithm)*测试一个整数(integer)是质数(prime)还是合数(composite)是可行的。也就是说(That is)，素性(primality)的Solovay-Strassen测试使用随机性(randomness)作为算法最重要部分。这个算法并不确定一个给定的整数是质数还是合数，而是给出一个数是质数还是合数的确定的概率。仅需要少量几次重复Solovay-Strassen测验几乎就能确定一个数是质数还是合数。Solovay-Strassen测验的提出在那个时代具有特殊(especial)意义，因为质数确定性检验(deterministic test)其时尚是未知的。因此，它看起来好像在具有随机数生成器的计算机上可以高效地执行在传统确定图灵机上没有有效解的计算任务。这个发现激发(inspire)了人们对于其他有着丰厚回报(has paid off handsomely  handsomely, adv,漂亮的慷慨的相当大的)随机算法的研究，这个领域发展(blossoming into)为一个繁荣(thriving)的研究领域。   

随机算法对强化丘奇图灵理论提出(pose)了挑战，该挑战认为(suggesting that)的确有一些高效可解的问题存在，尽管确定图灵机无法有效解决他们。这个挑战看起来可以被强化丘奇图灵理论的一个很简单的修正所克服(solved)：   
*<center>概率图灵机可以有效地模拟任何算法过程</center>* 
*<center>Any algorithmic process can be simulated rfficiently using a probabilitics Turing machine.</center>*  

这个强丘奇图灵理论的特设(*ad hoc*)修正应该令你感到有一些反胃(queasy,adj. 呕吐的；不稳定的；催吐的)。这是不是预示着在未来的某天还会有其他的计算模型可以有效解决图灵机不可解的问题？我们是否可以通过某种方式寻得一个单独的计算模型，可以保证(guarantee)其可以高效模拟任何其他计算模型？  

1985年，在这个问题的驱使下(motivated by)大卫多伊奇(David Deutsch)开始探寻是否可以用物理规则推导(derive)丘奇图灵理论的一个更强大版本。多伊奇寻求用物理理论而不是向其添加特设假设(ad hoc hypothesis)提供给丘奇图灵理论一个如物理理论本身状态一般稳固(as secure as)的根基(foundation)。特别地，多伊奇尝试定义一种可以高效模拟任意物理系统的计算设备。由于物理定律最终(ultimately)是量子力学的，多伊奇很自然的会考虑以量子力学规则(principles)为根基的计算设备。这些设备，在四十九年前就已经被图灵定义的机器的量子对应(analogues)，最终导出(led ultimately)了一个现代概念，也就是本书所介绍的量子计算机。    

事实上截至本书撰写时，多伊奇的通用量子计算机的概念是否能高效模拟任意物理系统依然是未知的(not clear)。证明或证伪(refute, 反驳，驳斥)这个猜想(conjecture)是量子计算和量子信息领域一个很大的开放命题。例如量子场论(quantum field theory)或者甚至是更加深奥的(esoteric)基于弦论、量子引力或其他物理理论的一些效应或许可以令我们超越多伊奇的通用量子计算机，带给我们一个更强的计算模型。不过在现在这个阶段，一切还未可知。  
 
对丘奇图灵理论的强化形式来说，多伊奇的量子计算机模型可以做到的事情是一个挑战。多伊奇想要知道使用量子计算机能否有效解决在经典计算机甚至概率图灵机上无解的计算问题。他设计了一个简单的例子用以指示量子计算机的确可能有远超过经典计算机的算力。   

多伊奇做出的这杰出的第一步在接下来(subsequent)的十年n间得到了许多人的改进(was improved)，最终(culminating)Peter Shor在1994年证明(demonstration)了两个最重要的(enormously important)问题——寻找整数质因子问题和被称作“离散化对数”的问题——可以被量子计算机有效解决。Shor的证明引起了(attracted)人们广泛(widespread)的兴趣，因为这两个问题一直到现在还被认为在经典计算机上无有效解。Shor的结论有力的表明了量子计算机比图灵机甚至是概率图灵机更加niubility。1995年量子计算机威力的被进一步(further)证明，Lov Grover展示了另一个重要问题——在一些非结构化搜索空间中实行搜索的问题——也可以被量子计算机加速。尽管Grover算法并没有提供Shor水平的惊人(spectacular)加速，基于搜索的方法论的广泛适应性(applicability)激起了人们对Grover算法的强烈(considerable)兴趣。   

几乎在Shor算法和Grover算法提出(discover)的同时，许多人也在挖掘(develop)理查德费曼(Richard Feynman)1982年提出(suggest)的一个想法。Feynman曾指出在经典计算机上模拟量子力学系统看起来有着巨大的(essential)困难，并且提出(suggest)到直接给予量子力学准则构建计算机可以避免这个困难。在1990年代，许多研究团队开始充实(flesh ... out)这个想法，表示使用量子计算机去有效模拟在经典计算机上没有已知有效模拟方法的系统的确是可能的。或许(It's likely that)量子计算机在未来一个主要的用途(application)就是执行经典计算机难以模拟的量子力学系统的模拟，这是一个有着深远(profound)科技意义(implication)的问题。   

相比经典计算机，量子计算机可以更快的解决的问题还有什么？简单地说，我们还不知道。落实较好的量子算法看起来依然是困难的。悲观主义者可能会认为这是因为可以很好地支持已发现算法的量子计算机还不存在，但我们持有不同意见。由于开发者面临着设计经典计算机算法不可能遇到的两个难题，导致量子计算机的算法设计本身就是困难的。首先，人类的直觉是扎根在现实世界的，如果我们依靠直觉辅助(aid to)算法设计，那我们实现的算法思想依然会是经典的。要设计出优秀的量子算法，至少在设计算法的过程中，必须“关闭”(turn off)我们的经典直觉(intuition)，而使用真正的量子效应实现想要的(desired)算法结果(end)。其次，真正有趣的是，仅仅设计一个量子力学算法是不够的。该算法必须胜过所有现有的经典算法。因此，人们可能会找到一种利用了量子力学真正的量子方面的算法，但由于有在对应性能上表现与之类似的经典算法的存在，它并不足以(nevertheless做插入语)引起(be not of...)广泛的兴趣。这两个问题的的结合使未来新量子算法的构建成为一个挑战。    

更广泛地(Even more broadly)，我们还可以探寻(ask)关于经典计算机和量子计算机地能力，我们是否可以做出一些概括(generalization)。假设情况的确如此，是什么令量子计算机比经典计算机更加强力？量子计算机可以有效解决什么样的问题，和经典计算机可以有效解决的问题相比呢。关于量子计算和量子信息，最令人激动的事情之一是我们对这些问题的答案知之甚少，这对未来更好的理解这些问题又是一个不小的挑战。     

已经到达量子计算的前沿，我们现在转而考虑(switch to)对量子计算和量子信息有贡献的另一个思想分支的历史：信息论。就在1940年代计算机科学爆炸般(exploding)发展的同时，另一场关于我们对*交流(communication)*的理解的革命也正在进行。1948年克劳德香农(Claude Shannon)发表的两篇璀璨的论文奠定了现代信息和通信理论的基础。   

或许香农做出的最重要的一步就是数学地定义了信息这一概念。在许多数学科学领域，根本定义(fundamental defination)的选择是一件相当灵活的事情。试着花几分钟的时间单纯地思考一个问题：你会怎样给信息源这个概念做一个数学定义？这个问题很多不同的答案已经得到广泛应用；然而就增进理解而言(in terms of increased understanding)香农做出的定义看起来是远远(far and away)最富有成果的(fruitful)，该定义引出了大量(a plethora of)深层结论和一个结构丰富(with a rich structure)的理论，看起来准确地反映了许多(尽管不是全部)现实世界的通讯问题。   

香农对于在信道(communications channel)上进行的信息通讯的两个相关问题非常感兴趣。第一个是，在信道上发送信息需要怎样的资源(resource but not source)？比如电话公司需要知道在给定的电缆(cable)上可以可靠的传输多少信息。第二个是，在信道有噪声干扰时信息能否传播(transmit)？  

通过对信息论的两个基本定理(theorems)做出证明，香农解答了这两个问题。第一，香农的无噪声通道编码定理*noiseless channel coding theorem*量化了需要从信息源存储输出的物理资源。香农第二基本定理，无噪声通道编码定理*noiseless channel coding theorem*量化了通过含噪信道能可靠传输的信息量。为了实现噪声存在时的信息可靠传输，Shannon展示了可以用来保护发送信息的纠错码(*error-correcting codes*)。香农噪声通道编码定理给出了纠错码提供的(afforded by)保护上限。不幸的是，香农定理没有显式地给出实现这个上限的实践可行的纠错码集合。从香农论文发表直到如今，在逐渐逼近香农定理给定的极限的尝试中，研究人员构建了更多和更好类别的纠错码。一个复杂的(sopsisticated)纠错码理论给寻求(in their quest to)设计良好纠错码的使用者提供了大量(a plethora of)选择。这些编码用在包括如(小型)光盘播放器(compact disc(光碟) player)、计算机调制解调器(modems)和卫星(satellite)通信系统等。    

量子信息理论有着类似的发展历程。1995年本舒马赫(Ben Schumacher)提出了香农无噪声编码理论的一个对应(analogue)，并在该过程中定义量子比特*qubit*作为实体物理资源(tangible physical resource)。然而Shannon噪声信道编码定理在量子信息中的对应(analogue)至今仍未可知。不过尽管如此(Nevertheless)，类似于(in analogy to)其经典对应物(counterparts)，一个量子纠错码理论，就像已经提到过的，已经得到一定发展，其允许量子计算机在噪声存在时执行高效的计算，并且在含噪量子信道建立可靠的通信也是允许的。   

的确，经典纠错码的思想已经被证明对于发展和理解量子纠错码非常重要。1996年，由*Calderbank & Shor*，和Steane组成的两个团队独立研究发现了一种非常重要的量子编码，以他们名字的首字母(after their initial)命名为CSS编码。该工作后来(since)被归为(been subsumed by)稳定器(stabilizer)编码，由Calderbank，Rains，Shor，Sloane和Gotessman独立发现。基于经典线性编码理论的思想进行构建(build upon)，这些发现为快速理解(rapid understanding)量子纠错码及其在量子计算和量子信息上的应用提供了极大便利(facilitate)。    

量子纠错码为保护量子态免受噪声干扰而发展(be developed to)。经由量子信道传输原始(oridinary)经典信息怎么样？这样做的效率如何？我们在这个舞台(arena，舞台，竞技场)上发现了一点(a few)惊喜。1992年Bennett和Wiesner解释了如何在仅有一个量子比特从发送方传输到接收方的情况下传输两个经典量子比特的信息，这是一个被称为(dub)超稠密编码(*superdense coding*)的结果。   

分布式(distributed)量子计算的结果更加有趣。想象你有两台联网的计算机，用以尝试解决一个特殊问题。解决这个问题需要多少次通信？近来发现，比之联网的经典计算机，量子计算机某些问题需要的通讯次数呈指数级减少。但不幸的是as yet(迄今为止)这些问题在实际环境(in practical setting)中并没有那么重要(especially)，并且受到一些undesirable技术限制。对量子信息和量子计算的未来的一个主要挑战，是寻找在具有现实重要性的问题，相比于分布式经典计算，分布式量子计算在这些问题上可以提供实质性的优势。   

让我们回到信息论本身。信息论的研究始于对单信道性质的研究，实际应用中，我们需要处理的问题(deal with)往往不是单信道，而是有许多通道的网络。信息网论(*networked information theory*)这一课题着眼于(deal with)具有多信道的网络的信息承载性质(information carring properities)。其已经发展成一个内容丰富又错从复杂(intricate)的学科。   

相反的是，量子信息网理论的研究还处于(is very much in, very much 作副词可省略)起步阶段(infancy)。甚至对于量子信道网络的信息搭载能力这种最基本问题我们都知之甚少。过去几年间我们已经做出了一些相当(rather)杰出的(striking)初步工作(perliminary)，然而对于量子信道仍不存在一个统一的量子网信息理论。一个量子信息网理论的例子应当足以(suffice to)使人相信(convince)它具有一般性理论的价值。假设我们正尝试经由含噪量子信道从Alice向Bob发送量子信息，如果该信道对量子信息的容量为零，那么任何信息都不可能可靠发送。再想象我们有两个该信道的同步(in synchrony)运行的副本(copies)。直觉(intuitively)上（而且是可以严格证明(riforously justified)的）这种信道对发送量子信息的容量也是零。但是，如果我们把其中一条信道的方向反过来，如下图**Figure1.1**所示，情况就成了(it turns out)我们可以得到Alice到Bob这件信息传输的非零容量！类似这样的反直觉性质正反应了量子信息的本质。更好地理解这种量子渠道网络的信息搭载性质是量子计算和量子信息的一个主要的开放性问题。
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/Quantum-book-01.png" alt="Figure1.1" width="550"/>  
<center>图一：经典地认为，如果我们有两个并排运行的由强噪声通道，这个组合通道无法发送信息。毫不意外地，及时将其中一条通道反向，其依然无法发送信息。在量子力学中，将其中一条零容量通道反向事实上是允许我们发送信息的！</center>   

让我们最后一次切换领域，转向庄严(venerable)而古老的*密码学(cryptography)*科学与艺术。广义地(broadly)讲，密码学是两个或多个互不相信的当事人(parties)进行交流或计算的问题。最著名的密码学(cryptograpsic)问题就是传输加密信息了，假设两个当事人希望加密交流，就比如你打算把你的信用卡号给商人(merchant)以换取商品(goods)，并且希望没有任何恶意的(malevolent)第三方截获(intercept)你的卡号。做成这件事的的方法正是使用*密码协议(cryptograpsic protocol)*。我们会在本书的后面部分详细描述密码协议是怎样工作的，但是现在简单做一些区分(distinction)就足够了(suffice)。最重要的是私钥密码系统和公钥密码系统之间的差别。  

私钥密码系统的工作机制是，两个当事人Alice和Bob希望通过共享一个只有他们知道的私钥进行通信。密钥的准确(exact)形式此处并不重要，可以认为其是一个0/1串。重要的是Alice使用这个私钥加密她希望发送给Bob的信息。加密完成会Alice将之发送给Bob，后者现在必须恢复原始信息。Alice依靠私钥加密加密(encrypt)信息，所以为了recover信息Bob也必须知道这串私钥，以撤销(undo)Alice做出(apply)的转换(transformation)。   

不幸的是在很多情境(contexts)私钥系统是有很多问题的，其中最基本的问题就是如何分配(distribute)密钥。在许多方面(in many ways)，密钥的分配问题和原始的私密交流问题一样困难——一个恶意的(malevolent)第三方可能会盗取(eavesdrop)密钥分配，然后使用这个截获的(intercepted)密钥解加密(decrypt)某些信息转换。    

在量子计算和量子信息方面最早的发现之一量子力学可以用来进行密钥分配，如此Alice和Bob的安全将不受任何影响(compromised)。这个过程称作量子密码学(quantum cryptography)或量子密钥分发(quantum key distribution)。基础的想法是利用(exploit)量子力学规则也就是在一般情况下观测(observation)会干扰(disturb)被观测系统。因此，如果一个窃听者(eavesdropper)在Alice和Bob尝试传输密钥时进行监听，由于在Alice和Bob正在用已建立密钥的信道上的干扰的存在，窃听者将无处遁形(be visible)。Alice和Bob就可以抛弃(throw out)在窃听者(eavesdroppers)监听时建立的密钥位并重新开始。第一个量子密码学(quantum cryptograpsic)的idea是Stephen Wiesner在1960年代提出的，但很不幸的是当时并未被大众所接受。1984年Bennet和Brassard基于Wiesner的早期工作提出(propose)了使用量子力学在Alice和Bob之间分发密钥的协议(protocol)，依照该协议密钥分发没有任何妥协的可能(probability of a compromise)。自那以后，不计其数的量子密码学协议被提出，实验原型也得到了发展。截至写作时，实验原型正在接近在现实世界实现有限尺度应用的水准(stage)。   


## (V) The quantum Fourier transform and its application (part of)      
> 当有人做出量子计算机(If computers that you build are quantum.)   
> 所有的间谍都想得到它(Then spies everywhere will all want'em.)   
> 它会破解我们的密码(Our codes will all fail,)   
> 读取我们的邮件(And they'll read our email,)   
> 直到我们以量子加密术对抗他们(Till we get crypt that's quantum, and daunt'em.)   
> Jennifer and Peter Shor     

> 为了读取我们的邮件(To read our E-mail, how mean)    
> 间谍和他们的量子机器如此卑鄙(of spies and their quantum machine;)      
> 值得安慰的是(be comforted though,)     
> 他们仍然不知道(they do not yet know)     
> 怎样分解12和15的因子(how to factorize twelve or fifteen.)    
> Volker Strssen    

> 计算机编程是一种艺术形式，就像创作诗歌和音乐一般。    
> Computer programming is a art form, like the creation of poetry or music.       
> Donald Knuth   

量子计算机可以高效地执行在经典计算机上不可行(not feasible)的任务，是迄今为止在量子计算领域最引人注目的(spectacular)发现。比如截至写作时，寻找n比特整数质因子这一任务，最好的经典算法，数域筛(number field sieve)仍需要$exp(\Theta(n^\frac13 log^\frac23 n))$步操作才能实现。该算法是指数于(be exponential in)被分解整数规模的，所以在经典计算机做因数分解被认为是个棘手的(intractabel)问题：即使是分解一个不大的数也会迅速成为一个不可能的事儿。相反的是，量子算法只需$O(n^2log\,n\,log\,log\,n)$步操作即可完成相同的任务。这就意味这，量子计算机在因数分解上指数倍快于已知最好的经典算法。这个结果本身就很重要(in its own right)，但或许最令人激动的方面是它引出的问题：量子计算机还能高效完成哪些经典计算机不可行的(infeasibel)问题？   
本章我们会介绍(develop)量子因式分解和许多其他有趣的量子算法的关键成分(ingredient)：量子傅里叶变换(*quantum Fourier transform*)。

### (V-1) The quantum Fourier transform   
> 一个好想法是可以简化的，且可以解决其创造时没有面临的问题。   
> A good idea has a way of becoming simpler and solving problems other than that for which it was intended.      
> --Robert Tarjan      

在数学和计算机科学领域，解决问题最有效的方法之一时将其转化为其他解已知的问题。有几类变换，其频繁地出现且适用于许多不同问题，以至于变换本身(for their own sake)就是值得研究的。量子计算领域有一个大发现：在量子计算机上计算一些此类的变换(transformation that be studied for their own sake)比经典计算机快得多。这是一个使基于量子计算机的快速算法的构建变得可行的发现。   

一种此类变换是离散傅里叶变换(*discrete Fourier Transform*)。在经典数学概念中，离散傅里叶变换的输入时一个复数向量(complex numbers)，$x_0,\cdots,x_{N-1}$，数组的长度N是定参数(fixed parameter)。输出的转换数据也是一个复数向量$y_0,\cdots,y_{N-1}$，$y_k$定义为：  
$$
y_k\equiv\frac{1}{\sqrt{N}\sum_{j=0}^{N-1}x_je^{i2\pi jk/N}},
\tag{5.1}
$$
此处$i$代表欧拉方程$e^{i\theta}$中的单位虚数。   
尽管量子傅里叶变换中卷积的概念有些不同，但其仍是一个完全相同的变换。建立在正交基$\|0\rangle,\cdots,,\|N-1\rangle$上的傅里叶变换可以定义为基态上的线性操作如下:   
$$
|j\rangle \longrightarrow \frac{1}{\sqrt{N}} \sum_{k=0}^{N-1} e^{i2\pi jk/N}|k\rangle. \tag{5.2}
$$   
同样的，任意(arbitirary)状态的的行为都可以写作：   
$$
\sum_{j=0}^{N-1}x_j|j\rangle\longrightarrow\sum_{k=0}^{N-1}x_k|k\rangle, \tag{5.3}
$$   
此处振幅$\|y_k\rangle$是振幅$\|x_j\rangle$的离散傅里叶变换。从定义上来看并不明显，但该变换就是一个幺正变换(unitary transformation)，因此可以作为量子计算机动力学来实现(implement as)。我们会通过构建一个清晰明白的(manifestly)幺正量子电路计算傅里叶变换的方式证明其幺正性：   
* **实验5.1**：给出式$(5.2)$定义的线性变换的幺正性的直接证明。
* **实验5.2**：显示地(Explictly)计算n昆比特状态$\|00...0\rangle$的傅里叶变换。   

接下来，令$N=2^n$，其中$n$是整数；基$\|0\rangle,\cdots,\|2^n-1\rangle$是有$n$昆比特的量子计算机的计算基础。使用二进制表示法$j\,=\,j_1j_2\cdots j_n$书写状态$\|j\rangle$更便于理解。再正式一点可以写作$j\,=\,j_12^{n-1}+j_22^{n-2}+\cdots+j_n2^0$。使用(adopt)$0.j_lj_{l+1}\cdots j_{l+m}$这种符号(notation)表示二进制分式$j_l/2+j_{l+1}/4+\cdots +j_{l+m}/2^{m-l+1}$也很方便。   
量子傅里叶变换再加一点代数(algebra)就变成了下面这种实用的乘积(product)表示：    

$$  
\begin{aligned}
& |j_1,\cdots,j_n\rangle \rightarrow \\
& \frac{
    \left(|0\rangle+e^{i2\pi0.j_n}|1\rangle\right)\cdots 
    \left(|0\rangle+e^{i2\pi0.j_1\cdots j_{n-1}j_n}|1\rangle \right)}
    {2^{n/2}}.
\end{aligned}   \tag{5.4}
$$   

此处对每个$\|1\rangle$的系数$e^{i2\pi0.j_n}, e^{i2\pi0.j_{n-1}j_n}, e^{i2\pi0.j_1\cdots j_{n-1}j_n}$的设置保留疑问。  
乘积表示(product represatation)实用到你甚至希望考虑以之作为量子傅里叶变换的定义（讲真，没看出来，先记住吧）。正如我们简单介绍的，这种表示允许我们建立(construct)一个高效的计算傅里叶变换的量子电路，证明量子傅里叶变换的幺正性，提供基于量子傅里叶变换的算法的观点(insight)。作为一个附加奖励(incidental bonus)，我们会在实验中实现(obtain)快速傅里叶变换。运用一些基本代数(elementary algebra)知识，可以证明乘积表达$(5.4)$和定义$(5.2)$之间的相等关系：  

$$
\begin{aligned}
&(5.5)\,\,\,\,|j\rangle&\rightarrow&\frac{1}{2^{n/2}}\sum_{k=0}^{2^n-1}e^{i2\pi jk/2^n}|k\rangle\\
  
&(5.6)&=&\frac{1}{2^{n/2}}\sum_{k_1=0}^1\cdots\sum_{k_n=0}^1e^{i2\pi j(\sum_{l=0}^nk_l2^{-l})}|k_1\cdots k_n\rangle\\
  
&(5.7)&=&\frac{1}{2^{n/2}}\sum_{k_1=0}^1\cdots\sum_{k_n=0}^1\bigotimes\limits_{l=1}^{n} e^{i2\pi jk_l 2^{-l}}|k_l\rangle\\

&(5.8)&=&\frac{1}{2^{n/2}}\bigotimes\limits_{l=1}^{n}\left[\sum_{k_l=0}^1e^{i2\pi jk_l2^{-l}}|k_l\rangle\right]\\

&(5.9)&=&\frac{1}{2^{n/2}}\bigotimes\limits_{l=1}^{n}\left[|0\rangle+e^{i2\pi j2^{-l}}|1\rangle\right]\\

&(5.10)&=&\frac{
    \left(|0\rangle+e^{i2\pi0.j_n}|1\rangle\right)
    \left(|0\rangle+e^{i2\pi0.j_{n-1}j_n}|1\rangle\right)\cdots 
    \left(|0\rangle+e^{i2\pi0.j_1\cdots j_{n-1}j_n}|1\rangle \right)}
    {2^{n/2}}.
\end{aligned}
$$  

尝试解释该过程（译注）：  
* **$(5.5)\rightarrow (5.6)$**：$\|k\rangle$同样是$n-qubit$的量子态，需要$2^n$个经典比特位线性表示。所以多项累加$\sum_{k_1=0}^1\cdots\sum_{k_n=0}^1$并不表示乘法，仅表示涵盖所有$k=k_1k_2\cdots k_n$的可能取值；而$k=k_1k_2\cdots k_n$也显然是二进制串。那么考虑
$$
\frac{k}{2^n} = \frac{k_1k_2\cdots k_n}{2^n}，
$$

* $k_1$在最高位(第1位)，单列出来也就是$k_1\cdot2^{n-1}/2^n=k_1/2^1=k_12^{-1}$(在 **“实验5.2”**下第一段有相关说明，最低位$2^0$)，将第$l$位推广至一般化就是$k_l\cdot2^{n-l}/2^n=k_l2^{-l}$，从而：   
$$
\frac{k}{2^n} = \frac{k_1k_2\cdots k_n}{2^n} = \sum_{l=1}^nk_l2^{-l}。  
$$  
* **$(5.6)\rightarrow (5.7)$**：为了方便电路设计和理解，我们把$n-qubit$的$\|k_1\cdots k_n\rangle$分解成单量子位$\|0\rangle$的内积形式：   
$$
\begin{aligned}
e^{i2\pi j\sum_{l=1}^n}k_l2^{-l} |k_1\cdots k_n\rangle&=
e^{i2\pi jk_1/2}|k_1\rangle\otimes e^{i2\pi jk_2/2^2}|k_2\rangle\cdots e^{i2\pi jk_n/2^n}|k_n\rangle\\
&=\bigotimes\limits_{l=1}^ne^{i2\pi jk_l/2^l}|k_l\rangle.  
\end{aligned}   
$$    
* **$(5.8)\rightarrow (5.9)$**：每个单昆比特量子位表示为线性组合$\sum_{k_l=0}^1e^{i2\pi jk_l2^{-l}}\|k_l\rangle$，展开到$(5.9)$式为：$\|0\rangle+e^{i2\pi j2^{-l}}\|1\rangle$。
* **$(5.9)\rightarrow (5.10)$**：将内积形式展开到定义$(5.4)$的最终形式。对于$\frac{j_1\cdots j_n}{2^l}$的结果只保留小数部分，认为整数部分$j_{int}$对$e^i2\pi j_{int}$的结果不产生影响。从而：   
$$
\frac{j_1\cdots j_n}{2^l} = \frac{j_12^{n-1}\cdots j_n2^0}{2^l} = j_1\cdots j_{n-l}.j_{n-l+1}\cdots j_{n}.
$$   
由乘积表示$(5.4)$可以很轻松的推出(derive)量子傅里叶变换的电路形式，如图**Figure5.1**所示：   
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/Quantum-book-02.png" alt="Figure5.1" width="550"/>    
量子门$R_k$表示幺正转换
$$
R_k\equiv\left[
\begin{matrix}
1&0\\0&e^{i2\pi/2^k}
\end{matrix}\right].   \tag{5.11}
$$   

为知晓(To see)上图所示的电路怎样计算量子傅里叶变换，我们先考虑量子态$\|j_1\cdots j_n$被输入之后会发生什么。部署(Apply)哈德马(Hadamard)门到第一个比特生成状态   
$$
\frac{1}{2^{1/2}}\left(|0\rangle+e^{i2\pi 0.j_1}|1\rangle\right)|j_2\cdots j_n\rangle ,   
\tag{5.12}
$$   
（译注：所有过程均应注意$j=j_1j_2\cdots j_n$是二进制串）   
（译注：对于$\|j_1\rangle$的初始状态不深究，仅记$H(\|j_1\rangle)=\frac{|0\rangle+e^{i2\pi 0.j_1}|1\rangle}{\sqrt{2}}$）当$j_1=1$时$e^{i2\pi0.j_1}=e^{2\pi i/2}=-1$，$j_1=0$时$e^{i2\pi0.j_1}=e^{2\pi\cdot 0}=+1$。再部署一个连接$j_2$的二阶受控幺正变换controlled-$R_2$，从而产生状态：   
$$
\frac{1}{2^{1/2}}\left(|0\rangle+e^{i2\pi 0.j_1j_2}|1\rangle\right)|j_2\cdots j_n\rangle . 
\tag{5.13}
$$   
继续部署受控幺正变换controlled-$R_3$, $R_4$直到$R_n$，每一个受控幺正门都会在第一个$\|1\rangle$的有效相位(co-efficient)上添加一个额外位(extra bit)，最终得到状态：   
$$
\frac{1}{2^{1/2}}\left(|0\rangle+e^{i2\pi 0.j_1\cdots j_n}|1\rangle\right)|j_2\cdots j_n\rangle . 
\tag{5.14}
$$    
对第二个昆比特执行类似过程的操作，现在被操作的昆比特已经是两个了。部署哈德马门得到状态：   
$$
\frac{1}{2^{2/2}}\left(|0\rangle+e^{i2\pi 0.j_1\cdots j_n}|1\rangle\right) \left(|0\rangle+e^{i2\pi 0.j_2}|1\rangle\right)|j_3\cdots j_n\rangle , 
\tag{5.15}  
$$  
部署受控旋转门（幺正变换）controlled-$R_2,\cdots R_n-1$（译注，由于$j_2$到$j_n$的距离只有$n-1$）产生(yield)状态：   
$$
\frac{1}{2^{2/2}}\left(|0\rangle+e^{i2\pi 0.j_1\cdots j_n}|1\rangle\right) \left(|0\rangle+e^{i2\pi 0.j_2\cdots j_n}|1\rangle\right)|j_3\cdots j_n\rangle . 
\tag{5.16}  
$$  
类似地，对后面的每个昆比特执行类似的操纵得到最终状态：   
$$
\frac{1}{2^{n/2}}\left(|0\rangle+e^{i2\pi 0.j_1\cdots j_n}|1\rangle\right) \left(|0\rangle+e^{i2\pi 0.j_2\cdots j_n}|1\rangle\right) \cdots \left(|0\rangle+e^{i2\pi 0.j_n}|1\rangle\right) . 
\tag{5.17}  
$$   
简洁起见(for clearity)，图**Figure-5.1**中省略了(omitted from)Swap门。其用意反转昆比特的顺序。经过swap操作后的昆比特状态就成了：   
$$
\left(|0\rangle+e^{i2\pi 0.j_n}|1\rangle\right) 
\left(|0\rangle+e^{i2\pi 0.j_{n-1} j_n}|1\rangle\right). 
\cdots 
\frac{1}{2^{n/2}}\left(|0\rangle+e^{i2\pi 0.j_1\cdots j_n}|1\rangle\right).
\tag{5.18}  
$$   
对比式$(5.4)$，我们看到这正是量子傅里叶变换所需的(desired)输出。电路中的每个量子门都是幺正的，从而这种结构也证明了量子傅里叶变换是幺正的。图$Box-5.1$显示地展示了一个三昆比特量子傅里叶变换电路的例子：  
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/Quantum-book-03.png" alt="Box-5.1" width="200"/>  
确切地讲(for concreteness)这可能有助于查看(look at)三昆比特量子傅里叶变换的显式电路。  

这个电路使用了几个门？先是在第一个昆比特上部署一个哈德马门和$n-1$个受控旋转门(分别为二阶旋转$\frac\pi2^2$和三阶旋转$\frac\pi2^3$)，然后在第一个昆比特上部署一个哈德马门和$n-2$个受控(二阶)旋转门，现在是$n+(n-1)$个门。如此继续，总共需要$n+(n-1)+\cdots+1=n(n+1)/2$个门，再加上Swap门。最多需要$n/2$个Swap门，每个交换可以使用三个受控非门(controlled-NOT, CX)来实现(accomplished)。这样一来，该电路提供了一个复杂度$\Theta(n^2)$的算法实现量子傅里叶变换。  

对比而言(In contrast)，在$2^n$个元素上实现离散傅里叶变换的最好经典算法比如快速傅里叶变换FFT的复杂度是$\Theta(n2^n)$。也就是说，相比于在量子计算机上，经典算法计算傅里叶变换需要的操作数指数倍多于量子实现(inplement)。  

表象上(At face value)听起来(sounds)这棒极了(terrific)，因为傅里叶变换是现实世界许多数据处理应用的重要步骤(crucial)。比如计算机语音识别，音素(phoneme)识别的第一步就是进行数字化(digitized)声音的傅里叶转变。那我们能否利用量子傅里叶变换加速傅里叶变换的计算？很遗憾，答案是至今没有已知的方法完成如此操作。问题出在，量子计算机的振幅(amplitude)无法通过测量直接获取。因此就无从(no way of)确定(determine)初始状态的傅里叶变换振幅。Worse still，通常(in gerneral)没有办法有效准备傅里叶变换的初始状态。因此寻找量子傅里叶变换的用途(uses)可能比我们希望的更加微妙(subtle)。在本章和下一章节，我们会介绍(develop)一些建立在量子傅里叶变换的更精妙应用上的算法。   

### (V-2) Phase estimation   
傅里叶变换是相位估计(*phase estimation, pe*)这一通用过程(general procedure)的关键，(in turn)相位估计又是许多量子算法的关键。假设幺正操作$U$对应于特征值(eigenvalue)$e^{i2\pi\psi}$的特征向量(eigenvector)为$\|u\rangle$，此处$\psi$是未知值，相位估计算法的目标就是估计出$\psi$。为了完成估计，我们假设我们有可用的(available)黑盒(有时也被称为神谕*oracles*)，对于合适的非负整数$j$该*black box*用以(capable of)准备好状态$\|u\rangle$并执行受控幺正操作$controlled-U^{2_j}$。黑盒的使用意味着(indicate)相位估计过程就其自身而言不是一个完备量子算法，而更应被认为是一种"子程"*subroutine*或"模块"*modeule*，当与其他子程结合就可以用来执行一些有趣的计算任务。在相位估计过程的具体(specific)应用中我们将准确地(exactly)做到这一点以描述这些黑盒操作怎样被执行，并且把它们和相位估计过程结合去做一些真正(genuinely)有用的任务。但现在我们依然先把它们视作黑盒。   
量子相位估计过程使用两个寄存器。第一个由$t$个初始化为$\|0\rangle$状态的昆比特组成。$t$的选择依靠两个条件：我们对$\psi$的估计所希望得到的精确度的数字位数，以及我们所希望相位估计过程的成功概率。第二个寄存器以状态$\|u\rangle$开始，包含存储$\|u\rangle$所需的昆比特数。相位估计分成两步执行。首先部署图**Figure-5.2**所示的电路，
<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/Quantum-book-04.png" alt="Figure-5.2" width="500"/> 
<center>此处保留疑惑，controlled-U应当是受控门，为什么会对控制量子位施加影响？0初始态的量子位经哈德马门后应当统一是:+态，此处却根据所控制受控门的不同而输出不同。</center>

电路先是在第一个寄存器部署哈德马门，再把受控幺正变换controlled-$U$操作应用于第二个寄存器，U升幂至2的连续(successive)次幂(译注：根据第一个$t$-qubits寄存器，one by one 地$U^{2^0},\cdots,U^{2^{t-1}}$)。第一个寄存器的终态很显然就成了：   
$$
\begin{aligned}
&\frac{1}{2^{t/2}} \left(|0\rangle+e^{i2\pi 2^{t-1}\psi}|1\rangle\right) \left(|0\rangle+e^{i2\pi 2^{t-2}\psi}|1\rangle\right) \cdots \left(|0\rangle+e^{i2\pi 2^0\psi}|1\rangle\right)\\   
=\,\,\,&\frac{1}{2^{t/2}} \sum_{k=0}^{2^t-1}e^{i2\pi k\psi}|0\rangle.
\end{aligned}
\tag{5.20}   
$$   
忽略(omit)第二个寄存器，其自始至终保持状态$\|u\rangle$不变（所以受控操作controlled-$U$以之为控制位？受多量子比特控制？）  
 
相位估计的第二阶段是在第一个寄存器部署逆量子傅里叶变换，可以由反转上一节实验五中的量子傅里叶变换电路完成(be obtained)，只需$\Theta(t^2)$步。第三和最后一阶段就是通过进行计算基础上的测量读出第一寄存器的状态。该算法提供了一个相当出色的对$\psi$的估计，其整体原理图(schematic)如图**Figure-5.3**所示。   

<img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/Quantum-book-04.png" alt="Figure-5.3" width="550"/>   

图**Figure-5.3**：相位估计过程的整体原理图。顶部$t$个昆比特('/'通常表示一捆量子比特 a bundle of wires)是第一个寄存器，底部的量子比特是第二个寄存器，数目为执行幺正操作$U$所需的量。$\|u\rangle$是$U$对应于特征值$e^{i2\pi\psi}$的特征向量。测量输出是$\psi$的近似，精度为$t-\left[log(2+\frac{1}{2\epsilon})\right]$个比特位，成功的概率至少$1-\epsilon$。   

为了增强我们对相位估计为什么可以运行的直觉，假设相位$\psi$可以精确地表示为$t$比特$\psi=0.\psi_1\cdots\psi_t$。此时考虑$(5.20)$所示的第一阶段状态，可以被重写为：   
$$
\frac{1}{2^{t/2}} \left(|0\rangle+e^{i2\pi0.\psi_t}\right)
\left(|0\rangle+e^{i2\pi0.\psi_{t-1}\psi_t}\right)\cdots
\left(|0\rangle+e^{i2\pi0.\psi_1\cdots\psi_{t-1}\psi_t}\right).
\tag{5.21}
$$    
第二阶段应用逆量子傅里叶变换。与先前乘法形式傅里叶变换公式$(5.4)$相比可以看出第二阶段输出状态的形式是乘积态$\|\psi_1\cdots\psi_t\rangle$。从而计算基础上的测量精确地提供相位$\psi$！（从而？从哪儿？！）    
总结一下，相位估计算法允许我们在给定特征向量$\|u\rangle$的情况下估计出对应的幺正操作$U$特征值的相位$\psi$。此过程的核心(in the heart of)基本(essential)特征是逆傅里叶变换执行变换的能力：   
$$
\frac{1}{2^{t/2}}\sum_{j=0}^{2^t-1}e^{i2\pi\psi j}|j\rangle|u\rangle\longrightarrow|\hat{\psi}\rangle|u\rangle,  \tag{5.22}
$$   
此处$\hat{\psi}$表示测量$\psi$的良好估计态。

# 完结   
基础部分至此完结，关于量子傅里叶变换，相位估计，依然知其然不知其所以然，甚至大部分情况下不知其然。不过对于野路子出身的学生来讲，有一个粗浅印象也足够了，留待与后续理论和实验内容相互映衬以深入理解。下一篇HHL算法和实验会大量应用本章内容。   
