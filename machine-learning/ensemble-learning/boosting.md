# Boosting算法簇
## 概念
Boosting是一族可将弱学习器提升为强学习器的算法。
## 基础boosting<sup>[5]

主要思想是“三个臭皮匠顶个诸葛亮”。一般来说，找到弱学习算法要相对容易一些，然后通过反复学习得到一系列弱分类器，组合这些弱分类器得到一个强分类器。Boosting算法要涉及到两个部分，*加法模型*和*前向分步*算法。

*加法模型*就是说强分类器由一系列弱分类器线性相加而成。一般组合形式如下：

$F_M(x;P)=∑_{m=1}^nβ_mh(x;a_m)$

其中，$h(x;a_m)$ 就是一个个的弱分类器，$a_m$是弱分类器学习到的最优参数，$β_m$就是弱学习在强分类器中所占比重，P是所有$a_m$和$β_m$的组合。这些弱分类器线性相加组成强分类器。

*前向分步*就是说在训练过程中，下一轮迭代产生的分类器是在上一轮的基础上训练得来的。也就是可以写成这样的形式：

$F_m(x)=F_m−1(x)+β_mh_m(x;a_m)$

由于采用的损失函数不同，Boosting算法也因此有了不同的类型，AdaBoost就是损失函数为指数损失的Boosting算法。

## Adaboost
Adaboost是一种迭代算法，其核心思想是针对同一个训练集训练不同的分类器(弱分类器)，然后把这些弱分类器集合起来，构成一个更强的最终分类器(强分类器)。其算法本身是通过改变数据分布来实现的，它根据每次训练集之中每个样本的分类是否正确，以及上次的总体分类的准确率，来确定每个样本的权值。将修改过权值的新数据集送给下层分类器进行训练，最后将每次训练得到的分类器最后融合起来，作为最后的决策分类器。使用adaboost分类器可以排除一些不必要的训练数据特征，并放在关键的训练数据上面。<sup>[4]</sup>

基于Boosting的理解，对于AdaBoost，我们要搞清楚两点<sup>[5]</sup>：

- 每一次迭代的弱学习$h(x;a_m)$有何不一样，如何学习？
- 弱分类器权值$β_m$如何确定？
  
对于第一个问题，AdaBoost改变了训练数据的权值，也就是样本的概率分布，其思想是将关注点放在被错误分类的样本上，减小上一轮被正确分类的样本权值，提高那些被错误分类的样本权值。然后，再根据所采用的一些基本机器学习算法进行学习，比如逻辑回归。

对于第二个问题，AdaBoost采用加权多数表决的方法，加大分类误差率小的弱分类器的权重，减小分类误差率大的弱分类器的权重。这个很好理解，正确率高分得好的弱分类器在强分类器中当然应该有较大的发言权。

更多详细内容可见参考资料[5]

## Gradient Boosting(GB)
Gradient boosting<sup>[1]</sup>的思想是迭代生多个（M个）弱的模型，然后将每个弱模型的预测结果相加，后面的模型$F_{m+1}(x)$基于前面学习模型的$F_m(x)$的效果生成的，关系如下：
$F_{m+1}(x) = F_m(x) + h(x)$。$h(x)$拟合的是梯度而不是残差。

输入：训练集$\{(x_i,y_i)\}^n_{i=1}$,可微损失函数$L(y,F(x))$，迭代次数$M$ 
算法如下<sup>[2]</sup>: 
1. 使用常量初始化模型：
$$F_0(x)=argmin_γ∑_{i=1}^nL(y_i,γ).$$

2. For m = 1 to M: 
   1. 使用损失函数的负梯度在当前模型$F_{m−1}(x)$上的值近似代替残差：<br/>
$r_{im}=−\left[ \frac{∂L(yi,F(xi))}{∂F(xi)}\right]_{F(x)=Fm−1(x)} for i=1,…,n.$

    2. 使用基学习器$h_m(x)$拟合近似的残差值，即用训练集${(x_i,r_{im})}^n_{i=1}$进行拟合 
    3. 解一维优化问题计算乘子$γ_m$:<br/>
$γ_m=argmin_γ∑_{i=1}^nL(y_i,F_{m−1}(x_i)+γh_m(x_i)).$

    4. 更新模型：<br/>
$F_m(x)=F_{m−1}(x)+γ_mh_m(x).$

1. 输出$F_M(x)$

### 为什么不用残差，而用梯度<sup>[3]</sup>
看起来前者更科学一点，有绝对最优方向不学，为什么舍近求远去估计一个局部最优方向呢？原因在于灵活性。前者最大问题是，由于它依赖残差，cost function一般固定为反映残差的均方差，因此很难处理纯回归问题之外的问题。而后者求解方法为梯度下降，只要可求导的cost function都可以使用。

## Gradient Boosting Decision Tree(GBDT)

## Xgboost
## 参考
[1] [一步一步理解GB、GBDT、xgboost](https://www.cnblogs.com/wxquare/p/5541414.html)<br/>
[2] [梯度提升算法介绍](https://blog.csdn.net/wutao1530663/article/details/71235727)<br/>
[3] [GBDT 梯度提升决策树的简单推导](https://blog.csdn.net/shenxiaoming77/article/details/62045963)<br/>
[4] [adaboost百科](https://baike.baidu.com/item/adaboost/4531273)<br/>
[5] [adaboost原理详解](https://www.cnblogs.com/ScorpioLu/p/8295990.html)<br/>
[6] [GB原论文](thesis/GreedyFuncApproxSS.pdf)