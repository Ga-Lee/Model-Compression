# Model Analysis  
especially for the redundancy of its weights
## 相关工作  
### 1.*Denil(2013)* [Predicting parameters in deep learning](https://www.cs.toronto.edu/~ranzato/publications/denil_nips2013.pdf)  
文章主要工作：  
* 对 ***W*** 权重矩阵进行low rank分解，只学习其中一个factor。
* 证明了权重中很大部分value是冗余的，利用其中一部分可以准确预测剩下的权重。  
### 2.*Ba(2014)* [Do Deep Nets Really Need to be Deep?](https://papers.nips.cc/paper/2014/file/ea8fcd92d59581717e06eb187f10666d-Paper.pdf)
文章主要工作：  
* 用一层shallow网络来模拟深层网络的表达能力（***但是并不意味着parameter量的下降，单层网络内的节点量也很巨大***）
* 发现用原始dataset的hard label训练shallow网络时，效果并不理想；使用训练好的deep NN的logit作为regression targets训练shallow网络（prediction logit distillation）可以使其学习到与DNN相同的能力。  

#### Note：
> 启发1. 希望用小的网络（结构简单\参数量少）去完成原本大网络的功能，可以采用模型蒸馏的方法，而不用dataset直接训练小网络，因为直接用hard label不一定work。  
> 启发2. 对以上情况的解释：
>> * DNN更robust，可以对一些错误样本进行矫正，使shallow net学习更容易
>> * DNN学习能力更强，可以学习一些比较难的任务，把输入转换为更容易区分的soft logit
>> * logit比hard label包含更丰富的信息
>> * hard label部分依赖输入，deep net把和目标不相关的feature都过滤掉了，输出的logit可以看做是从input data 中学习的浓缩的最有效的feature
> 模型蒸馏的方法包括prediction logit distillation（软硬蒸馏），hidden-state distillation， self-attention distillation(在transformer中,如minivit)...）（后面explore到的会继续补充）

