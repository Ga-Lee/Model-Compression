# 起源  

## 1989年 
Lecun在 [Generalization and network design strategies](https://d1wqtxts1xzle7.cloudfront.net/30766382/lecun-with-cover-page-v2.pdf?Expires=1650274558&Signature=IQMgrx-qH6SlHiQoNoCBQNnim1Xjw8DcG4gdCNuGSdEqOXJobmsIwXdwWi8HZ0Gl9R0C7ulVcnH83NeoyeZqZ7aiRD1M1lhTKnr5sp3l0W3xxwFm5QsAGGsCG81WsCStAV77NpkPm7Bxf26IykdoWqWdPjM4DZhQdJNBhqjzAAbaAmYEMB4erHoq5qosrYfgqZ3YfclBqdWPxDDT9Mn8Xperfs62WwaTnskldc7UUhd44LNYLcT5AyD3HzDxds9rFhj0TP5JKuLhtupyuY3k1BzaECBaFco4DcHRKx44EIJUxjY9HVcN1CkmQNwoIirKCPQ12qHxhH3NqDGK6wUXyA__&Key-Pair-Id=APKAJLOHF5GGSLRBV4ZA) 中提出了**weight sharing**的概念  

#### 文章主要在于讨论模型泛化能力相关因素，以及可以用于提高泛化能力的策略
* 网络的泛化性能取决于假设空间的大小/网络中自由参数的数量和训练样本的大小，减小网络中自由参数的数量、增加训练样本的数量，能有效提高网络的泛化性能(也就是过拟合问题)  

* 提高泛化能力的策略包括：
>> 1. 在网络的训练过程中，动态地删除掉一些无用的连接（useless connections），通过在损失函数（cost function）中增加惩罚项，用于惩罚那些拥有过多参数的网络。
>> 2. 权重共享（weight sharing），指的是使用单个参数（weight）控制多个连接（connections）。权重共享被解释为在这些连接强度上面施加相同的限制（contraints）。
>> 3. 实质上是权重共享的泛化，称作权重空间转换（Weight-Space Transformation, WST ）。WST基于这样一个事实：在学习过程中，模型参数的优化并不一定要在权重空间中（weight space）执行，可以在任何适合该优化任务的参数空间中执行，只要权重空间可以由该参数空间通过已知的转换公式（transformation）计算得到，同时该转换公式的雅克比矩阵（Jacobian matrix）已知（因为在反向传播计算损失函数关于参数空间的梯度时需要用到）。

#### 总结：  
论文中设计了5个实验，验证了**最小化自由参数数量可以提高模型的泛化能力** 以及**参考目标任务的先验知识可以提高模型设计的有效性**（在文中体现为对Net3设计的启发：利用图像识别任务的先验知识，先用2-D array提取局部特征*local information*（不共享参数的2D卷积操作），再进一步可以结合成更高级特征。Net-3、Net-4、和Net-5都基于此，获得了优秀的泛化性能。）

#### * Note：
这篇论文的重点在于讨论*Generalization* ，同时这里的权值共享（weight-sharing）指的是Net3、4、5中卷积权重共享，得到的结论是“随着卷积操作共享权重的比例增加，网络整体自由参数数量下降，模型在相同任务上体现出更好的泛化能力。”  
**这与我想探索的weight-sharing-level不同。** 但是这里是权值共享概念、CNN权值共享的早期雏形，还是挺有意义的。

## 1992年
Hinton在 [simplifying the neural networks by soft weight-sharing](http://www.cs.toronto.edu/~hinton/absps/sunspots.pdf) 中提出了soft weight-sharing。  

#### 这篇文章同样致力于提升模型的泛化能力。  
* 可以理解为是上文Lecun工作的进一步发展
* 它的贡献在于：
> 在损失函数中增加了一种更合理的惩罚项（利用[a mixture of multiple gaussians](https://metacademy.org/graphs/concepts/soft_weight_sharing_neural_nets#focus=mixture_of_gaussians&mode=learn)对模型权重的分布情况进行建模），来对模型复杂度进行约束，以追求更好的泛化效果。
> 通过 **聚类（cluster）** 的方法，具有相似值的weights将被分配进同一个subset，整个学习过程中是动态调整的。  

大致原理是这样的：  
> 1. 首先，他假设所有的权重值都是分别由一系列的gaussian生成的，其中![fomula2](https://latex.codecogs.com/svg.image?w_i)由gaussian j生成的条件概率为：  
                                 ![formula1](https://latex.codecogs.com/svg.image?r_j(w_i)=\frac{\pi_jp_j(w_i)}{\sum_{k}{\pi_kp_k(w_i)}})  
其中，![f](https://latex.codecogs.com/svg.image?p_j(w_i))是![fomula2](https://latex.codecogs.com/svg.image?w_i)在gaussian j下的概率密度。  
> 2. 每个gaussian都代表一个weights cluster，相对应地它会引入需要额外学习的相关分布特征参数 ![f](https://latex.codecogs.com/svg.image?\mu_j,\pi_j,\sigma_j)。在初始阶段，权重的聚类是比较soft的。随着模型的学习，cluster内部分布趋向于shrink到更小的variance，更高的weight概率密度（weight分布更集中）。  
> 3. cost函数表达式为：  
                                 ![f](https://latex.codecogs.com/svg.image?C&space;=&space;\frac{K}{\sigma&space;_{y}^{2}}\sum_{c}\frac{1}{2}(y_{c}-d_{c})^{2}-\sum_{i}log[\sum_{j}\pi_jp_j(w_i)&space;])  
 使用梯度下降的方法来对![f](https://latex.codecogs.com/svg.image?\mu_j,\pi_j,\sigma_y,w_i,\sigma_j)进行联合寻优。  
 
 #### * Note：  
* 主要贡献包括：1、cluster方法在weight sharing中的应用，这种约束是soft的，而不是严格的复用（其实这里和minivit就有点像了，本质都不是严格意义上的权重复用）2、是一种模型正则化手段，通过使同一个cluster中的权重值尽可能相近，追求仅使用一组weight表达整个模型，降低模型复杂度，达到更高的泛化效果。  
* 文章实在是比较有年代感了...很多细节并没有看明白，比如在同一个cluster内，权重的梯度计算和更新策略是怎么样的？QAQ 立个Flag以后回来填坑...  

# 近代
## 2015年  
chen 在[Compressing Neural Networks with the Hashing Trick](file:///C:/Users/leega/Desktop/HKUSTGZ/paper/model%20compression/weight%20sharing/compressing%20Neural%20Networks%20with%20the%20Hashing%20Trick.pdf)中提到了基于hash-function 的权重分组共享机制
## 2016年
Han song在 [simplifying the neural networks by soft weight-sharing](http://www.cs.toronto.edu/~hinton/absps/sunspots.pdf) 中提出了soft weight-sharing。  
