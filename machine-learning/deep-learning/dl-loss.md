# 损失函数
## 引言
损失函数（Loss function）用于评估模型预测值f(x)与真实Y的不一致程度。损失函数是经验风险函数的核心部分

## 风险
### 经验风险
经验风险是模型在样本集上的预测能力，等于模型对于样本中所有样本点输出值对应上真实值在**损失函数**上输出的损失的均值。经验风险越小说明模型对训练集的拟合程度越好，但如果只考虑经验风险会出现过拟合的现象。
它是局部的
$$R_{exp}(f)=\frac{\sum_N^i{L(y_i,f(x_i))}}{N}$$

### 期望风险
表示模型针对世界上所有样本的全局的预测能力，它是一个期望值，因为需要知道X和Y的概率分布P(X,Y)故不可得到的。
它是全局的。

### 结构风险
结构风险是经验风险和期望风险的折中。它在经验风险函数的后面加了一个正则化项（惩罚项）。
$$R_{exp}(f)=\frac{\sum_N^i{L(y_i,f(x_i))}+\lambda J(f)}{N},J(f)表示模型复杂度$$
但个人认为当样本越多，越接近真实分布，越少需要考虑的结构风险

## 分类损失<sup>[1]
### 0-1损失函数
$$ L_{01}(x)=\left\{
\begin{aligned}
&1&, &y = y' \\
&0&, &y != y'
\end{aligned}
\right.
$$

### Cross Entropy Loss/Log Loss(交叉熵损失/log损失)<sup>[2,3]
交叉熵（Cross Entropy）是Shannon信息论中一个重要概念，主要用于度量两个概率分布间的差异性信息。交叉熵损失函数可以衡量真实分布p与当前训练得到的概率分布q有多么大的差异。
随着预测概率偏离实际标签，交叉熵损失会逐渐增加。(以下$y'$均是预测值，$y$为gt)
$$L_{CrossEntropy}=-\sum_i^C{y_i\log(y_i')} , C为类别集合$$

$$对于二元分类L_{CrossEntropyBinary}=-y\log(y')-(1-y)\log(1-y') $$

在多元分类下，j假设真实标签为[1 0 0 0 0]，预测值为[0.15,0.5,0.1,0.1,0.2]，那么该损失评估的就是两者的分布偏差，最终便是-log(0.15)
#### Categorical Cross-Entropy loss/Softmax Loss<sup>[3]
softmax激活函数+交叉熵损失。一般被用于输出一张图片的在多个类别上的概率，即多类别分类。
#### Binary Cross-Entropy Loss<sup>[3]
sigmoid激活函数+交叉熵损失。不同于Softmax Loss，该损失计算的向量每一维度上的损失是独立于其它维度的，通常被用于多标签分类。

#### KL Divergence（KL散度/相对熵）
KL散度是描述两个概率分布P和Q差异的一种方法。
### Focal Loss<sup>[4]
Focal loss主要是为了解决one-stage目标检测中正负样本比例严重失衡的问题。该损失函数降低了大量简单负样本在训练中所占的权重，也可理解为一种困难样本挖掘。

$$ L_{fl}(x)=\left\{
\begin{aligned}
&-\alpha(1-y')^\gamma\log(y')&, &y = 1 \\
&-(1-\alpha)y'^\gamma\log(1-y')&, &y = 0
\end{aligned}
\right.
$$

首先在原有的基础上加了一个因子，其中gamma>0使得减少易分类样本的损失。使得更关注于困难的、错分的样本。

例如gamma为2，对于正类样本而言，预测结果为0.95肯定是简单样本，所以（1-0.95）的gamma次方就会很小，这时损失函数值就变得更小。而预测概率为0.3的样本其损失相对很大。对于负类样本而言同样，预测0.1的结果应当远比预测0.7的样本损失值要小得多。对于预测概率为0.5时，损失只减少了0.25倍，所以更加关注于这种难以区分的样本。这样减少了简单样本的影响，大量预测概率很小的样本叠加起来后的效应才可能比较有效。

此外，加入平衡因子alpha，用来平衡正负样本本身的比例不均
### Exponential Loss（指数损失）
### Hinge Loss(铰链损失/多分类 SVM 损失)
hinge loss常用于最大间隔分类（maximum-margin classification），最常用的是支持向量机。
尽管不可微，但它是一个凸函数，因此可以轻而易举地使用机器学习领域中常用的凸优化器。

表示如果被正确分类，损失是0，否则损失由模型对各个错误类别的预测值与对正确类别的预测值的差值的累加和表示。
$$L_{hinge}=\sum_{j != y}{max(0, s_j-s_{y}+1)}, s_j为模型对j类别的预测分数$$

## 回归损失<sup>[1]
### Mean Square Error(均方误差/平方损失/L2损失)
均方误差（MSE）度量的是预测值和实际观测值间差的平方的均值。
它只考虑误差的平均大小，不考虑其方向。
但由于经过平方，与真实值偏离较多的预测值会比偏离较少的预测值受到更为严重的惩罚。
再加上 MSE 的数学特性很好，这使得计算梯度变得更容易。
$$L_{MSE}=\frac{\sum_{i=1}^n{(y_i-y_j')^2}}{n}$$
### Mean Absolute Error（均绝对误差/L1 损失）
平均绝对误差（MAE）度量的是预测值和实际观测值之间绝对差之和的平均值。
和 MSE 一样，这种度量方法也是在不考虑方向的情况下衡量误差大小。
但和 MSE 的不同之处在于，MAE 需要像线性规划这样更复杂的工具来计算梯度。
此外，MAE 对异常值更加稳健，因为它不使用平方。
$$L_{MSE}=\frac{\sum_{i=1}^n{|y_i-y_i'|}}{n}$$
### Huber Loss
### Log cosh Loss
### Quantiel Loss

# 参考
- [1] [机器学习中常用的损失函数](https://www.jiqizhixin.com/articles/091202) 
- [2] [Cross-entropy loss explanation](https://datascience.stackexchange.com/questions/20296/cross-entropy-loss-explanation) 
- [3] [Understanding Categorical Cross-Entropy Loss, Binary Cross-Entropy Loss, Softmax Loss, Logistic Loss, Focal Loss and all those confusing names](https://gombru.github.io/2018/05/23/cross_entropy_loss/) 
- [4] [Focal Loss理解](https://www.cnblogs.com/king-lps/p/9497836.html) 