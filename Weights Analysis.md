# Model Analysis  
especially for the redundancy of its weights
## 相关工作  
### 1.*Denil(2013)* [Predicting parameters in deep learning](https://www.cs.toronto.edu/~ranzato/publications/denil_nips2013.pdf)  
文章主要工作：  
* 对 某个***W*** 权重矩阵进行low rank分解，只学习其中一个factor。
* 证明了权重matrix中很大部分value是冗余的，利用其中一部分可以准确预测剩下的权重。  
#### Note：
> 研究的是**层内**的parameter redundancy，用低至5%的parameter即可估计其余parameters。
> 是之后很多**层内**weight sharing工作的实验基础。  

### 2.*Denton(2014)* [Exploiting Linear Structure Within Convolutional Networks for Efficient Evaluation](https://proceedings.neurips.cc/paper/2014/file/2afe4567e1bf64d32a5527244d104cea-Paper.pdf)  
文章主要工作：  
* 受到上文的启发，将low rank decomposition拓展到CNNs上，显著加速了CNNs的test过程（主要是对第一、第二层的加速）
* 在第三节详细介绍了一些可以“compress 4 dimensional convolutional weight tensors and fully connected weight matrices into a representation that permits efficient computation and storage”的方法，涉及到techniques for low-rank tensor techniques for low-rank tensor approximationss及其应用方法。
#### Note：
> 本质上研究的还是**层内**的parameter redundancy，每一层的low-rank tensor approximations是独立的。
> 是一个trained + decomposition for conv layer + finetune for upper layers（为了提升performance）的过程。
#### compression training策略  
目前来看，大致有三类：  
* 先train original big size网络，达到较好performance后，再对权重compression（+finetune）代表工作：本文。
* 先设计weight sharing的准则（哪些weights应该共享同一weight value，group方式是什么？（hash function or cluster？）weight value的更新方式是？（成员梯度求和？））等，然后在此constraint下，直接train当前small size网络（small指的是自由参数量较少）
* train → compression → train → 交替进行（貌似是Han Song那篇经典的compression论文中的方法，挖个坑先）

### 3.*Ba(2014)* [Do Deep Nets Really Need to be Deep?](https://papers.nips.cc/paper/2014/file/ea8fcd92d59581717e06eb187f10666d-Paper.pdf)
文章主要工作：  
* 用一层shallow网络来模拟深层网络的表达能力（***但是并不意味着parameter量的下降，单层网络内的节点量也很巨大***）
* 发现用原始dataset的hard label训练shallow网络时，效果并不理想；使用训练好的deep NN的logit作为regression targets训练shallow网络（prediction logit ***soft distillation***）可以使其学习到与DNN相同的能力。  

#### Note：
> 启发1. 希望用小的网络（结构简单\参数量少）去完成原本大网络的功能，可以采用模型蒸馏的方法，而不用dataset直接训练小网络，因为直接用hard label不一定work。  
> 启发2. 对以上情况的解释：
>> * DNN更robust，可以对一些错误样本进行矫正，使shallow net学习更容易
>> * DNN学习能力更强，可以学习一些比较难的任务，把输入转换为更容易区分的soft logit
>> * logit比hard label包含更丰富的信息
>> * hard label部分依赖输入，deep net把和目标不相关的feature都过滤掉了，输出的logit可以看做是从input data 中学习的浓缩的最有效的feature
> 模型蒸馏的方法包括prediction logit distillation（软硬蒸馏），hidden-state distillation， self-attention distillation(在transformer中,如minivit)...）（后面explore到的会继续补充）  

### 4.*Chen(2015)* [Compressing Neural Networks with the Hashing Trick](http://proceedings.mlr.press/v37/chenc15.pdf)
文章主要工作：  
* 用hash function 随机地把某一层中的所有weights（用virtual matrix表示）分配到若干hash buckets，其中属于同一bucket的weight共用weight value。真正存weights的是real 
* 
* 发现用原始dataset的hard label训练shallow网络时，效果并不理想；使用训练好的deep NN的logit作为regression targets训练shallow网络（prediction logit ***soft distillation***）可以使其学习到与DNN相同的能力。  

#### Note：
> 启发1. 希望用小的网络（结构简单\参数量少）去完成原本大网络的功能，可以采用模型蒸馏的方法，而不用dataset直接训练小网络，因为直接用hard label不一定work。  
> 启发2. 对以上情况的解释：
>> * DNN更robust，可以对一些错误样本进行矫正，使shallow net学习更容易
>> * DNN学习能力更强，可以学习一些比较难的任务，把输入转换为更容易区分的soft logit
>> * logit比hard label包含更丰富的信息
>> * hard label部分依赖输入，deep net把和目标不相关的feature都过滤掉了，输出的logit可以看做是从input data 中学习的浓缩的最有效的feature
> 模型蒸馏的方法包括prediction logit distillation（软硬蒸馏），hidden-state distillation， self-attention distillation(在transformer中,如minivit)...）（后面explore到的会继续补充）

