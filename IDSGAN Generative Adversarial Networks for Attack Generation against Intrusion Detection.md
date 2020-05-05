# IDSGAN: Generative Adversarial Networks for Attack Generation against Intrusion Detection

### 摘要

入侵检测系统作为一种重要的安全工具，对恶意流量进行的网络攻击负有防御责任。目前，在机器学习算法的帮助下，入侵检测系统发展迅速。然而，当系统面临对抗性攻击时，其鲁棒性是值得怀疑的。为了改进检测系统，需要研究更多的潜在攻击方法。本文提出了一种生成性对抗网络IDSGAN框架，用于生成能够欺骗和规避入侵检测系统的对抗性攻击。考虑到检测系统的内部结构不为攻击者所知，对抗性攻击实例对检测系统进行黑盒攻击。IDSGAN利用生成器将原始恶意流量转换为敌对恶意流量示例。鉴别器对流量实例进行分类，并模拟黑盒检测系统。更重要的是，我们只修改了部分攻击的非功能特性，以保证入侵的有效性。基于NSL-KDD数据集，证明了该模型对多种不同攻击的检测系统进行攻击的可行性，并取得了良好的效果。此外，通过改变未修改特征的数量来测试IDSGAN的鲁棒性。

### 介绍

随着网络安全威胁的蔓延，入侵检测系统（IDS）成为检测和防御恶意网络流量形式的网络攻击的重要工具。**IDS监视网络流量，并在发现不安全流量时发出警报。IDS的主要目的是将网络记录分为正常记录和恶意记录。**在分类问题中，机器学习算法被广泛地应用于入侵检测中，并取得了良好的效果，这些检测算法被用来监测和分析恶意流量，包括**K近邻、支持向量机、决策树等**（Tsai等）。2009年）。近年来，**深度学习算法如卷积神经网络、递归神经网络、自动编码器等入侵检测领域**得到了迅速的发展，并推动了进一步的发展（Li等。2017年）。这些算法提高了入侵检测的准确性和简化程度（Lin、Shi和Xue2018）。

然而，入侵检测系统在对抗性的例子下逐渐暴露出其脆弱性：接近原始输入但分类错误的输入（Carlini和Wagner，2017）。攻击者试图通过使用敌对的恶意流量示例将模型欺骗到所需的错误分类中。而生成性对抗网络（GAN）是这种对抗性攻击的潜在方法。古德费罗等人。介绍了用于训练生成模型的框架GAN（Goodfellow等人。2014），其主要思想是两个网络，生成器网络和鉴别器网络，玩一个极小极大博弈，以收敛到一个最优解（Lee，Han，和Lee  2017）。GAN在图像、声音和文本的生成方面显示了其最新的进展（Ledig等人。2017年；Dong等人。2018年；Su等人。2018年）。信息安全具有文本或句子的相似性，也是近年来GAN研究的热点。目前的研究已经**使用GAN来改进恶意软件的检测，或者生成更多威胁攻击的对手恶意软件示例**（Kim、Bu和Cho  2017；Hu和Tan 2017b）。但目前对用于入侵检测的GAN的研究还很缺乏。

本文提出了一种新的入侵检测系统（IDSGAN）对抗攻击生成的GAN框架。该模型的目标是实现IDSGAN，生成能够欺骗和规避防御系统检测的恶意流量实例。将机器学习算法指定为黑盒入侵检测系统，模拟现实世界中未知入侵检测系统的内部结构。我们在Wasserstein  GAN的基础上设计并改进了发生器和鉴别器，以获得其优越的特性（Arjovsky、Chintala和Bottou  2017）。生成器生成敌对攻击：敌对恶意流量。鉴别器为生成器的训练提供反馈，并模拟黑盒id。攻击者可以根据敌对示例执行攻击。总之，我们作出了以下贡献：

•针对入侵检测系统，设计了一种改进的入侵检测系统框架IDS  GAN，生成攻击IDS的对抗性恶意流量实例。在生成过程中，对原始攻击流量的修改不会使其攻击功能失效。

•为了模拟现实世界中的攻击和入侵检测，对抗性攻击采用黑箱攻击和机器学习算法作为入侵检测模型。

•IDSGAN在实验中表现出良好的性能，在不同的黑盒IDS模型和不同的攻击中，对抗实例的检测率接近0，这意味着大多数对抗攻击都可以欺骗和绕过黑盒IDS的检测。

•我们在流量实例中测试并讨论了模型在不同数量的未修改特征下的影响。不同的攻击和检测算法对模型的影响不同，反映了IDSGAN在这些攻击和IDS模型下的不一致鲁棒性。

### **相关工作**

### 提出方法

##### 数据集：NSL-KDD数据集描述

KDD'99改进，NSL-KDD现在被用作评估IDS的基准数据集（Hu等人。2015年）。在NSLKDD中，数据集由训练集KDDTrain+和测试集KDDTest+组成。为了模拟真实的网络环境，流量数据包含正常流量和四大类恶意流量：探测（Probe）、拒绝服务（DoS）、用户到根（U2R）和根到本地（R2L）。

将NSL-KDD中的流量记录提取到特征组合序列中，作为对正常和恶意网络流量的抽象描述。离散值有9个特征，连续值有32个特征，共有41个特征。

根据特征的含义，这些特征包括四组，包括“内在的”、“内容的”、“基于时间的流量”和“基于主机的流量”（Davis  and Clark 2011；Lee and Stolfo  2000），如图1所示。

一组特征的详细描述如下：

•“内在”特征：反映一般网络分析中单个连接的内在特征。

•“内容”特征：标记连接的内容，表明流量中是否存在与攻击相关的某些行为。

•“基于时间的流量”功能：检查过去2秒钟内与当前连接具有相同目标主机或相同服务的连接，包括“相同主机”功能和“相同服务”功能。

•“基于主机的流量”功能：监控过去100个连接中的连接，这些连接与当前连接具有相同的目标主机或相同的服务，作为“基于时间的流量”功能的镜像。

![image-20200417102712733](C:\Users\LEO\AppData\Roaming\Typora\typora-user-images\image-20200417102712733.png)

##### 数据预处理

独热编码

标准化、归一化

##### IDSGAN的结构

随着GAN的迅速发展，许多GAN版本都是针对不同的要求而设计的。为了防止GAN的非收敛性和不稳定性，基于wassersteingan的结构设计了IDSGAN。在IDSGAN中，生成器修改一些特定的特性以生成对抗性恶意流量示例。训练鉴别器来模拟黑盒IDS并辅助生成器训练。黑盒入侵检测是通过机器学习算法来实现的。通过在训练中使生成器的权值参数不同于IDS，可以生成对抗实例来逃避IDS的检测。IDSGAN的框架如图2所示。

![image-20200417102938622](C:\Users\LEO\AppData\Roaming\Typora\typora-user-images\image-20200417102938622.png)

IDSGAN的训练。将训练数据集分为正常流量和恶意流量。添加噪声后，恶意流量被发送到生成器中。利用黑盒IDS对敌对恶意流量和正常流量进行预测。在鉴别器中使用预测标签和原始标签来模拟黑盒 IDS。根据鉴别结果和黑盒IDS的预测标签，计算出生成器的loss。

###### 生成对抗性例子的限制

虽然敌方攻击实例生成的主要目的是躲避IDS，但**前提是该生成应保留流量的攻击功能**。从攻击的原理和目的来看，每一类攻击都有其功能特点，代表了这种攻击的基本功能。换句话说，如果我们只更改非功能特性，而不是功能特性，则攻击属性保持不变。因此，为了避免流量失效，必须保持每次攻击的功能特征不变。对于一次攻击中不存在与攻击相关的功能的非功能性特征，我们可以排除或保留它们。这些保留在生成中的特征，包括功能特征，在本文中称为未修改特征。如（Lee和Stolfo  2000）所示，NSL-KDD中各类攻击的功能特征见表1。

![image-20200417103337646](C:\Users\LEO\AppData\Roaming\Typora\typora-user-images\image-20200417103337646.png)

###### Generator

作为该模型的重要组成部分，生成器对规避攻击起到了对抗性恶意流量实例生成的作用。为了将原始示例转化为对抗示例，每个流量示例的输入向量应该由m维原始示例向量m和n维噪声向量n组成。作为原始示例部分，m已经过预处理。为了与预处理的矢量一致，噪声部分的元素由随机数组成，在[0,1]间。

我们设计的生成器结构有5个线性层的神经网络。利用ReLU非线性 F＝max（0，x）来激活前4个线性层的输出。为了使对抗实例满足原实例向量M的公式，输出层必须有M个单位。该网络的参数更新是基于鉴频器的反馈。

此外，在处理修改后的特征时还有一些技巧。若要将输出元素限制在[0,1]的范围内，则将1以上的元素设置为1，将0以下的元素设置为0。此外，考虑到“Intrinsic”特征是NSL-KDD中所有攻击的功能特征，我们不会修改两个以上的离散特征值，这些离散特征值都聚集在“内在”特征值中。因此，修正后的离散特征是二元离散特征。这些修改后的离散特征值将转换为二进制值。二进制转换的阈值为0.5。高于或低于阈值的值将转换为1或0。

###### Discriminator

在不了解黑箱入侵检测模型结构的情况下，在对抗性恶意流量实例和正常流量实例的基础上，利用鉴别器模拟黑盒入侵检测。模拟有助于生成器的训练，因为在生成器的训练过程中，敌方攻击会试图绕过IDS。同时，作为一种分类工具，鉴别器对生成器的输出进行分类，并将反馈信息提供给生成器。

判别器是一个多层神经网络，用于对恶意流量和正常流量进行分类。其训练数据由对抗性恶意流量和正常流量组成。

作为鉴别的主要目标之一，模拟黑盒 IDS 入侵检测能力的工作需要将黑盒入侵检测结果转化为正常和对抗性的恶意流量实例。首先，通过黑盒IDS对正常流量和恶意流量进行分类。然后，将黑盒 IDS 的检测结果作为判别器训练数据的目标标签，使判别器的分类与黑盒 IDS 相似。鉴别器模拟黑盒IDS模型的过程如图3所示。

![image-20200417105056814](C:\Users\LEO\AppData\Roaming\Typora\typora-user-images\image-20200417105056814.png)

###### 训练算法

在**生成器的训练**中，从判别器分类到对抗实例的结果为发生器的训练提供了梯度信息。生成器的损耗函数定义如下：

![image-20200417110304839](C:\Users\LEO\AppData\Roaming\Typora\typora-user-images\image-20200417110304839.png)

其中Sattack是原始恶意流量示例；G表示生成器，D表示鉴别器。为了训练和优化生成器来愚弄黑盒 IDS，我们需要最小化LG。

对于**鉴别器训练**，对抗性恶意流量实例是训练集的一部分。根据上述关于训练集与黑盒 IDS 的预测标签之间的关系的介绍，通过判别器的输出标签和黑盒 IDS 得到的预测标签来计算判别器的损失。因此，用于优化的鉴别器的损耗函数的定义如下：

![image-20200417110607697](C:\Users\LEO\AppData\Roaming\Typora\typora-user-images\image-20200417110607697.png)

其中s表示训练鉴别器的流量示例；Bnormaland 、Battack分别表示黑盒IDS预测的正常示例和对抗攻击示例。据Wasserstein-GAN所说，RMSProp是IDSGAN优化网络参数的优化器。算法1显示了一般IDSGAN训练的概要。

![image-20200417210901229](C:\Users\LEO\AppData\Roaming\Typora\typora-user-images\image-20200417210901229.png)



### **实验和结果**

实验中，采用PyTorch作为深度学习框架，实现了IDSGAN（Paszke等。2017年）。该模型是运行和评估在一台Linux个人电脑与英特尔核心i7-2600。IDSGAN接受了100个迭代的64 batch 训练。生成器和鉴别器的学习率均为0.0001。噪声矢量的维数是9。鉴别器训练的权值限幅阈值设置为0.01。

为了全面、深入地评价模型的性能，实验中采用了多种机器学习算法作为黑盒id。在对入侵检测相关研究的基础上，实验中采用的黑盒入侵检测算法包括支持向量机（SVM）、朴素贝叶斯（NB）、多层感知器（MLP）、逻辑回归（LR）、决策树（DT）、随机森林（RF）和K近邻（KNN）。在对IDSGAN进行评估之前，黑盒IDS模型已经用它们的训练集进行了训练。

基于由KDDTrain+和KDDTest+组成的NSL-KDD数据集，设计了训练集和测试集。黑盒 IDS 的训练集由KDDTrain+中的一半记录组成，包括正常和恶意流量记录。鉴别器的训练集包括KDDTrain+另一半的正常流量记录和来自生成器的对抗性恶意流量实例。由于GAN可以应用于二值分类，IDSGAN每次只能生成一类攻击的对抗实例。因此，在每个实验中，生成器的训练集都是KDDTrain+另一半中一类攻击的记录。KDDTest+中的一次攻击记录构成了一个实验生成器的测试集，在该实验中，生成一种用于测试的对抗性攻击来对IDS执行该攻击。

在实验指标上，测试了IDSGAN的检测率和规避增加率，比较直观地反映了IDSGAN的性能。检测率（DR）反映了黑盒IDS正确检测到的恶意流量记录占所有检测到的攻击记录的比例，直接体现了模型的规避能力和黑盒IDS的健壮性。原始检测率和对抗检测率分别表示对原始恶意流量记录和对抗恶意流量记录的检测率。此外，我们定义了逃逸增加率（EIR）为IDS未发现的对抗性恶意流量实例与原始恶意流量实例相比的增加率，比较反映了IDSGAN的能力，特别是在不同的背景下。这些指标计算如下：

![image-20200419131904809](C:\Users\LEO\AppData\Roaming\Typora\typora-user-images\image-20200419131904809.png)

较低的检测率意味着更多的恶意流量规避黑箱ID，直接反映了idsgan更强的能力。其次，低的规避率反映了黑箱ID能够检测到更多的对抗性实例，这意味着原始恶意流量和对抗性恶意流量之间的规避攻击能力的比较距离有所下降。因此，IDSGAN的动机是获得较低的检测率和较高的规避增加率。

#### IDSGAN在不同攻击下的性能

为了对该模型进行综合评估，对IDSGAN进行训练，然后在基于KDDTest+的测试中生成对抗性恶意流量。考虑到DoS和Probe都是基于网络的攻击，我们只对DoS进行了实验和分析，以反映IDSGAN对这类攻击的性能。此外，还对U2R和R2L等基于流量内容的攻击进行了实验。由于NSL-KDD中U2R和R2L的记录量较小，且具有相似的特点，本实验将U2R和R2L合并为一个攻击组。在应用IDSGAN之前，DoS、U2R和R2L的原始检测率是在经过训练的黑盒IDS上测量的，如表2所示。由于训练集中U2R和R2L的记录量较小，学习不足，使得对U2R和R2L的原始检测率较低。首先，在未修改功能特性的情况下，测试IDSGAN在不同攻击下的性能。实验结果如表2和图4所示。结果表明，经过IDSGAN处理后，DoS、U2R和R2L在不同黑盒IDS模型下的对抗检测率均下降，接近0，这意味着IDS模型几乎无法对任何一个敌方流量进行分类，这些攻击的原始检测率和敌方检测率之间的距离也很大且明显。如图4（a）所示，在所有检测算法下，DoS的对抗检测率从80%左右显著降低到2%以下。尽管多层感知器在所有的黑盒入侵检测模型中表现出了最好的鲁棒性，但其对DoS的对抗检测率仅为1.56%。逃逸率均在98%以上。结果表明，IDSGAN在DoS下具有良好的性能。在每次测试中，98%以上的对抗性DoS流量实例可以避开实验性IDS模型的检测。

![image-20200419132506255](C:\Users\LEO\AppData\Roaming\Typora\typora-user-images\image-20200419132506255.png)

![image-20200419172039339](C:\Users\LEO\AppData\Roaming\Typora\typora-user-images\image-20200419172039339.png)

对于图4（b）所示的U2R和R2L，虽然算法之间的原始检测率的差异是明显的，但是在对抗生成之后，所有的对抗检测率都等于或接近于0，这意味着大多数原始的U2R和R2L恶意流量实例在IDSGAN处理后能够欺骗和规避IDS。加上大量生成线索后无法检测出的实例，说明规避率提高很高，均在85%以上。实验结果表明，IDSGAN在各种攻击下都表现出很强的对抗攻击能力。面对对抗性攻击，IDSGAN的能力和黑盒IDS模型在不同攻击类别和不同IDS模型下的鲁棒性存在微小差异。

### 不同未修改特征量下IDSGAN的性能

在IDSGAN的能力和鲁棒性研究中，未修改特征的数量是决定该模型对抗攻击成功与否的重要因素。为了评价IDSGAN与未修改特征数量的关系，在DoS、U2R和R2L上进行了对比实验，改变了未修改特征的数量。考虑到每次攻击的功能特征是表示攻击功能的最少特征，改变未修改特征数量的唯一方法是在功能特征的基础上添加非功能特征。为了测试添加潜在特征对IDSGAN的整体影响，从每个其他特征集中随机选择这些添加的未修改特征。其余集合中添加的要素的比率相同。下面列出了实验中添加的未修改功能。