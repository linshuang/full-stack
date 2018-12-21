# 模型训练
## 引言
本文档记录了模型训练相关的一些概念和技术手段。

## 训练批次
- epoch
迭代次数，1个epoch等于使用训练集中的全部样本训练一次；
- batch size 批大小。采用SGD训练时，即每次训练在训练集中取batchsize个样本训练
- steps per epoch
Total number of steps (batches of samples) to yield from generator before declaring one epoch finished and starting the next epoch. 
## 优化
### 梯度下降法

### 批量梯度下降法BGD(Batch gradient descent)
每次迭代使用所有的样本。一般说的梯度下降都是指批梯度下降。batch指的是，每次更新θ的时候都需要所有的数据集。
优点：
- 全局最优
缺陷：
- 数据集很大时，训练过程计算量太大；
- 需要得到所有的数据才能开始训练；
- 训练慢，难收敛。
### 随机梯度下降SGD（Stochastic gradientdescent）
每次迭代随机抽取一组样本进行训练，并以此计算loss跟梯度。因为每批里的样本数目往往远小于总样本数，通过多批次随机可以达到接近多轮批梯度下降的效果，而批梯度下降要达到相同的效果则需要多轮，所以训练快。

特点：
- 每轮训练量大幅减少；
- 不需要得到所有的数据就可以进行训练；

和批量梯度下降的差异<sup>[4]：

- 标准梯度下降是在权值更新前对所有样例汇总误差，而随机梯度下降的权值是通过考查某个训练样例来更新的；
- 在标准梯度下降中，权值更新的每一步对多个样例求和，需要更多的计算；
- 标准梯度下降，由于使用真正的梯度，标准梯度下降对于每一次权值更新经常使用比随机梯度下降大的步长；
- 如果标准误差曲面有多个局部极小值，随机梯度下降有时可能避免陷入这些局部极小值中；

#### 冲量
在普通的梯度下降法x += v中，每次x的更新量v为v = - dx * lr，其中dx为目标函数func(x)对x的一阶导数，。
当使用冲量时，则把每次x的更新量v考虑为本次的梯度下降量- dx * lr与上次x的更新量v乘上一个介于[0, 1]的因子momentum的和，即v = - dx * lr + v * momemtum。

### Adam （adaptive moment estimation）
### 小批量梯度下降MBGD（Mini-batch gradient descent）

## 特征缩放 FatureScaling
## 均值归一化 mean normalizaion
[1] [从梯度下降到Adam方法](https://www.sohu.com/a/149921578_610300)<br/>
[2] [优化入门1](https://blog.csdn.net/weixin_42398658/article/details/84502215#comments)<br/>
[3] [优化入门2](https://blog.csdn.net/weixin_42398658/article/details/84525917)<br/>
[4] [梯度下降](https://blog.csdn.net/pipisorry/article/details/23692455)<br/>