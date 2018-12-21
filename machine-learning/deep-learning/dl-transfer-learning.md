# 迁移学习
## 概念
给定一个源领域$D_S$和学习任务$T_S$，一个目标领域$D_T$以及学习任务$T_T$，迁移学习旨在通过利用$D_S$和$T_S$上的知识，帮助提高在$D_T$上的目标预测函数$f_T(·)$的学习。这里源和目标的领域以及任务均不同。

## 实例迁移方法instance-transfer approach
为了在目标域中使用，对源域中一些标记的数据重新加权
## 特征表示迁移方法feauture-representation-transfer approach
找到一个“好的”特征表示，减少源和目标域之间的差异，减少分类和回归模型的错误
## 参数迁移方法parameter-transfer approach
发现源域模型和目标域模型之间的共享的利于转移学习的参数或先验

## 相关知识迁移方法relation-knowledge-transfer approach
构建源域和目标域之间的关系知识映射。这两个域是相关域和i.i.d(独立同分布)假设在每个域中都是不严格的

## 参考
[1] [A Survey on Transfer Learning](thesis/A_Survey_on_Transfer_Learning.pdf)<br/>