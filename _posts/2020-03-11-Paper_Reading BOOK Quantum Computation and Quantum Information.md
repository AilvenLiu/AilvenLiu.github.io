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

> Partial Translation of Classical Quantum Computation Book *Quantum Computation and Quantum Information*

## (I) Fundamental concepts    
> 科学提供了这个时代最勇敢的形而上学。它是一种彻底的人类构想，我们梦想、然后努力去发现、去解释、又继续去梦想，正是如此的信念驱动了科学的不懈前进。也正是这种信念促使我们不断踏足新的领域，世界因此以某种方式更加清晰明了，而我们将掌握宇宙真正的奇异之处。这些奇异之处终将被证明有某些关联，而且意义非凡。  
> Science offers the boldest metaphysics of the age. It is thoroughly human construct, driven by the faith that if we dream, press to discover, explain, and dream again, thereby plunging repeately into new terrain, the world will somehow come clearer and we will grasp the true strangeness of the universe. And the strangeness will all prove to be connected, and make sense.   
> Edward O. Wilson    

量子计算和量子信息的基本概念指的是什么？这种基本概念又是怎样发展的？它们会被用于什么地方？它们在这本书中将会被怎样地呈现？引言章节的目的正是通过粗略地描述量子计算和量子信息的发展前景去回答这些问题。本章节希望传达对于该领域核心概念的基本理解，展望其发展前景，帮助读者决定如何完成本书的剩余部分。   

本章节从1.1小节开始，讲述量子信息和量子计算的历史脉络。剩下的每个小节会给出一个或几个基本概念的简短描述，分别是：量子比特(1.2)，量子计算机、量子门和量子电路(1.3)，量子算法(1.4)，量子信息处理实验(1.5)，量子信息和量子通信(1.6)。   

在此过程中(Along the way)，我们会使用本章提到的基础数学方法介绍一些说明性的和易于理解的发展状况，比如量子隐态传输(quantum teleportation)和简单的量子算法。这些展示内容是独立的(self-contained)，设计地即使没有计算机科学或物理学基础也可以轻松理解。随着逐步地前进，在后面的章节中我们也将给出对于更加深入的讨论的指示，当然也包括了进一步阅读的参考和意见。   

如果你读到了感觉难以理解的地方，略过它跳到一个读起来舒服的地方即可。在这些方面上我们不得不使用一些技术术语(technical lingo)，最本书的最后会做出详细解释。先接受这些不理解的地方，当详细地理解了所有技术的细节后再回过头来看。第一张的重点是大局，细节部分将后续补充。  

### (I-1) Global presectives   
量子计算和量子信息是可以在量子力学系统中完成的信息处理任务的研究。这听起来相当地简单和显而易见，事实真的如此吗？和许多简单却深刻的想法一样，很长一段时间没有人考虑过使用量子力学系统处理信息。为了了解为什么出现这种情况，我们必须回到过去研究对量子信息和量子计算贡献了基础思想的各个领域————量子力学，计算机科学，信息理论和密码学(cryptography)。读者需要分别以物理学家、计算机科学家、信息理论学家和密码学家的角色去思考以在不同角度理解这个领域。   

#### (I-1.1) History of quantum computation and quantum information     
故事开始在二十世纪，当时科学界正经历一场未为人知(unheralded)的革命，物理学领域出现了一系列危机。问题开始于彼时的物理理论(现在被称为(dubbed)经典物理学)做出了一系列荒谬的预测(absurdity, n. 荒谬；谬论；荒谬的言行)比如含有无限能量的紫外线大灾难(ultraviolet catastrophe)，或电子无情地(inexorably)螺旋进入(spiraling into)原子核。起初，这些问题可以通过在经典物理学中添加特例(*ad hoc*)假设(hypothesis)来解决，但是随着对原子(atom)和辐射(radiation)的理解日益深入，这些尝试性的解释变得愈益错综复杂(convoluted)。经历了进四分之一个世纪的混乱(turmoil)后，在1920年代，危机来了，一个崭新的现代理论*量子力学*在这场危机中诞生了。自诞生之日起，量子力学就成为科学体系不可或缺(indispensable)的一部分，在太阳内外一切领域取得巨大成功(has been applied with enormous sucess to everything under and inside the Sun)，包括原子结构、恒星核聚变(nuclear fusion of stars)、超导体(superconductor)、DNA结构和自然界基本粒子(the elementary particles of Nature)。   

什么是量子力学？量子力学是一种构建(construction)物理理论的数学框架或规则集合。例如，有一种称为*量子电动力学(quantum electrodynamics)*的物理理论，极其精确地(with fantastic accuracy)描述了原子和光子之间的相互作用(interaction)，量子电动力学就是建立在量子力学框架上的，它包含了一些没有被量子力学确定的特殊规则。量子力学和特定物理理论比如量子电动力学之间的关系就像计算机操作系统和特定计算机应用软件之间的关联————操作系统设定了基本参数和操作模式，又给应用软件如何去完成特定任务留足了空间。   

量子力学的规则非常简洁，又但是即使是专家也会认为它是反直觉的(counterintuitive)，量子计算和量子信息的最早先兆(antecedent, 祖先)可能在物理学家更好的理解量子力学的长久渴望(long-standing desire)中被发现。最著名的量子力学批评家(critic)阿尔伯特爱因斯坦(Albert Einstein)直到去世也不认同(unreconciled with)这个由他所帮助缔造的理论。自量子力学诞生之日起一代又一代的物理学家一直在与之角力(wrestle with)，力图使量子力学的预测更容易被接受(palatable, adj, 美味的，可口的)。量子计算和量子信息的一个目标是开发(develop)令我们的直觉对量子力学更敏锐的工具，令它的预测对人类大脑更透明(trensparent, 透明的、坦率地、易懂的)。   

比如，在1980年代早期，人们对于能否利用量子效应(quantum effect)完成信号超光速传播产生了兴趣————根据爱因斯坦的相对论理论，这显然不可能(a big no-no)。这个问题的解答也转变成了能否能否克隆一个未知的量子态，也就是说构建(construct)一个量子态的副本。如果这种克隆是可行的，那么利用量子效应使信号比光还快也就成为可能。然而，克隆————在经典信息很容易完成的事情————在量子力学中却通常是不可能的。在1980年代早期发现的*不可克隆理论(no-cloning theorem)*是量子计算和量子信息的最早结论之一。自*no-cloning*理论诞生后人们又对它做出许多改进工作，现在我们有了概念工具，允许我们理解一个量子克隆设备(必然是不完善的, necessarily imperfect)可以工作得多好。这些工具反过来也被应用于理解量子力学的的其它方面。     

对量子计算和量子信息起到贡献作用的另一个历史分支(hestorical strand)是1970年代对于获得单个量子系统完全控制的兴趣。在1970年代之前(prior to the 1970s)对量子力学的应用通常包括对一个有大量量子力学系统组成的大样本(bulk sample)的总体级别(a gross level of)的控制，无一直接可行。比如超导电性(superconductivity)有一个极好的(superb)量子力学解释。然而由于一个超导体包含了大量(相比较于原子尺度而言)导体金属样本，我们只能证实其量子力学性质(nature)的极少几个方面，组成超导体的独立的量子系统依然是不可知的(inaccessible)。有一些系统比如粒子加速器(particle accelerators) 的确允许对独立量子系统进行有限的访问(access)，但依然极少提供对于组成系统(constitute system)的控制。   

自1970年代起，许多控制单原子的技术得到发展，例如已经开发出一种方法，使用“原子阱”(atom trap)捕获(trap)单原子中，将之隔离在其余世界(rest of the world)之外，这就允许我们以不可想象的精度对原子行为的许多不同方面加以证实。扫描隧道显微镜(scanning tunneling microscope)用以移动单个原子，依照设计者的意愿创造原子阵列。只涉及单个电子转移的电子设备也已被论证(demonstrate)可行。    

为什么以上种种努力都力图实现对单量子系统的完全控制？撇开(Setting aside)种种技术原因而专注于(constrating on)纯粹(pure)的科学，主要的原因是研究人员凭直觉(on hunch)做事。通常科学最深邃的见解(insight)来自于对于新的自然制度(regime)的证明方法的发展。例如1934-40年代射电天文学(radio astronomy)的发明导致了一系列惊人(a spectacular sequence of)的发现，包括银河系(Milky Way galaxy)星系核(galactic core)、脉冲星(pulsars)和类星体(quasars)。低温物理学在寻找降低不同系统温度的方法上取得了惊人的成功。类似地，通过获得单量子系统完全控制，我们正在探索未被触及(untouched)的自然状态(regime)以期发现新的意料之外的现象(phenomena)。我们正沿着这些方向(along these lines)迈出第一步，而且在这个体制(regime)已经发现了一些有趣的意外(interesting surprises)。那么在我们获取单量子系统的更复杂控制、并将之扩展到更复杂系统的过程中还会有什么其他发现吗？   

量子计算和量子信息对于近乎自然的适合此类问题(哪类问题？)。它们在不同级别的困难上为人类发明(devise)更好的操作单量子系统的方法、刺激新实验技术的发展、为最有趣的方向进行实验提供引导准则……等方面提供了一系列useful的挑战。相反地，如果我们要harness量子力学的力量应用到量子计算和量子信息，那么控制单量子系统的能力将必不可少。   

尽管有这强烈的兴趣，建立量子信息处理系统的努力已经resulted in了迄今为止的(to date)一定程度的(modest)成功。有能力在少量量子比特(昆比特qubits)进行一系列操作的小型量子计算机，展示了量子计算的最顶尖(the state of art)成果。实现*量子加密(quantum cryptography)*的实验原型(experimental prototypes)——一种长距离加密交流的方式——已经被论证，甚至已经在一定水平上应用到现实世界的某些任务上了。然而未来留给物理学家和工程师的挑战依然是巨大的：发展使大规模量子信息处理成为现实的技术。    

让我们把注意力从量子力学转移到20实际另一个伟大的智力大胜利(intellectual triumphs)，计算机科学。计算机科学的源头已经消弭在历史长河中。楔形(cuneiform)文字板表明，在汉谟拉比(Hammurabi)时期(circa 1750 B.C.大约公元前1750年)巴比伦人(Babylonians)已经发展出一些相当复杂的(sophisticated)想法思想，而且似乎许多想法可以追溯到更早的时期。   

现代计算机的原型(incarnation, 化身，典型)由伟大的数学家阿兰图灵(Alan Turing)在1936年的一篇杰出论文中提出(announce)。图灵详细地developed了一个我们现在称之为可编程计算机的抽象概念，为了纪念图灵(in his honor)，这种计算模式也被称作*图灵机*。图灵表明(showed)有一种通用图灵机可以模拟一切其他图灵机。此外，他声称通用图灵机完整的掌握(capture)通过算法手段(mean)执行任务的含义(mean)。这也就是说，如果一个算法可以被某个硬件(可以理解为PC机)所执行，那么在通用图灵机也有一个对应的算法，可以实现与PC机上运行的算法所能完成的绝对相同的任务。这种论断(assertion)————被称为*丘奇图灵理论(Church-Turing thesis)*以纪念图灵和另一位计算机科学的先驱(pioneer)，Alonzo Church————断言(assert)了物理设备可以执行怎样的算法的物理概念和通用图灵机的严格的数学概念之间的等效性(equivalence)。这一理论广泛被接受，为丰富的计算机科学理论发展奠定了基础(laid the foundation)。  

在图灵的论文发表后不久，由电子组件构成的初代计算机结构得到了发展。约翰·冯·诺伊曼(John von Neumann)提出了一个简单的理论模型，模型描述了怎样把所有计算机需要的组件以实际风格组合在一起，使其具有全部通用图灵机的功能。硬件开始真正的高速发展(take off, 起飞)在1947年，晶体管的出现。自那之后，计算机硬件以惊人的速度(race)强力发展,以至于1965年被戈登摩尔(Gordon Moore)总结为了一条定律，后来被称为*摩尔定律(Moore's law)*，这条定律声明计算机能力在成本不变的情况下(for constant cost)每两年翻一番。   

自1960年代开始的几十年间，摩尔定律几乎未曾失效。然而大多数观察家(observers)预计(expect)这个美梦将在二十一世纪的头二十年结束。传统计算机制造(fabrication)方法(approaches)正开始面临(run up against)尺寸方面的根本(fundamental)困难。随着电子器件的制作越来越小，量子效应开始干扰(interfere)它们的功能(functioning)。  

这个有摩尔定律最终失效所导致(posed by)的问题的一个可能的解是转向不同的计算机范式(paradigm, n, 范例，词形变化表)。量子计算理论提供了一种范式，其基本思想是基于量子力学而不是经典物理学执行计算。事实表明(It turns out)尽管普通的(ordinary)量子计算机可以用来模拟量子计算机，但以有效的方式执行模拟看起来依然是不可能的。相比经典计算机，量子计算机提供了基本的(essential)速度优势。这种速度优势是非常巨大的，以至于许多研究者都坚信经典计算的任何可想像的进步(conceivable progress)都不足以克服经典计算机和量子计算机的算力之间的差距。  

对量子计算机的有效模拟和(versus, v.s.)无效模拟是什么意思？回答这个问题的许多核心理念(notions)实际在量子计算机这一概念提出之间就有了。特别地，在计算复杂度领域，有效和无效算法的思想在数学上被精确化了。大概来讲，一个有效的算法可以在问题规模的多项式时间内解决问题；相反地，低效算法则需要超多项式(通常是指数级别)时间。值得注意的是在1960年代末70年代初，图灵机计算模型看起来好像(as though)至少和任何其他计算模型一样有效，从这种意义(in the sense that)看来，任何可以被其他计算模型解决的问题通过使用图灵机模拟其他计算模型，也都可以被图灵模型有效地解决。这种观察被总结(condified into)为一种加强的(strengthened)丘奇图灵理论：  
$$图灵机可以有效地模拟任何算法过程$$ 
$$Any algorithmic process can be simulated rfficiently using a Turing machine. $$    

强化丘奇图灵理论强调的关键是*有效的efficiently*一词。如果强化丘奇图灵理论是正确的，那就意味着无论我们在什么类型的机器上执行算法，这个机器都都以使用图灵模型进行模拟。这是一个非常重要的加强，因为这指示了为了分析一个给定的计算任务是否可以被有效地完成，我们可以自我限定在图灵机模型的计算上。   

强化丘奇图灵理论的一类挑战来自于模拟计算(analog computation)领域，自图灵之后，许多不同的研究团队注意到某些类型的计算机可以有效地解决曾被认为在图灵机上没有有效解决方案的问题。最初看来(at first glance)这些模拟计算机似乎(appear to)违反了(violate)丘奇图灵理论的强化形式。很不幸的是对于模拟计算，事实表明当对模拟计算机中存在的噪声做出现实的假设时，在所有已知的例子(in all known instances)中他们的能力都将消失；他们不再能解决图灵机无法有效解决的问题。这个教训(lesson)————在评估(evaluate)计算模型有效性时现实噪声的影响必须考虑在内(take into account)————是量子计算和量子信息早期一个巨大的挑战；*量子纠错编码(quantum error-correcting codes)*和*容错量子计算(fault-tolerent quantum computation)*理论成功应对了这一挑战(a challenge met by ...)。因此不同于模拟计算(analog computation)，量子计算原则上讲(in principle)可以容忍有限量(a finite amount)的噪声依然保持其计算优势。   

强化丘奇图灵理论的第一个较大挑战出现在(arose)1970年代中叶，其时 Robert Solovay和Volker Strassen展示了使用*随机化算法(randomized algorithm)*测试一个整数(integer)是质数(prime)还是合数(composite)是可行的。也就是说(That is)，素性(primality)的Solovay-Strassen测试使用随机性(randomness)作为算法最重要部分。这个算法并不确定一个给定的整数是质数还是合数，而是给出一个数是质数还是合数的确定的概率。仅需要少量几次重复Solovay-Strassen测验几乎就能确定一个数是质数还是合数。Solovay-Strassen测验的提出在那个时代具有特殊(especial)意义，因为质数确定性检验(deterministic test)其时尚是未知的。因此，它看起来好像具有随机数生成器的计算机可以高效地执行在传统确定图灵机上没有有效解的计算任务。