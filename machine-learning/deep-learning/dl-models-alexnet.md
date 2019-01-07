# AlexNet
## 引言
 AlexNet由Alex Krizhevsky于2012年提出，夺得2012年ILSVRC比赛的冠军，top5预测的错误率为16.4%，远超第一名。AlexNet采用8层的神经网络，5个卷积层和3个全连接层(3个卷积层后面加了最大池化层)，包含6亿3000万个链接，6000万个 参数和65万个神经元。

## 结构

![avatar](images/dl-alexnet/architecture.jpg)

## 特点：
- 深、宽
- Dropout
```
训练时使用Dropout随机忽略一部分神经元，防止过拟合，增加模型的泛化能力
```
- [ReLU激活函数](dl-activation.md)
- Local Response Normalization(LRN)
```
模仿生物神经系统的侧一直机制，对局部神经元的活动创建竞争机制，使得其中响应比较大的值变得相对更大，并抑制其他反馈较小的神经元，增强了模型的泛化能力
```
- 在CNN中使用重叠的最大池化(步长小于卷积核)
- 使用CUDA加速深度卷积网络的训练
- 使用数据增强

## 参考
- [1] [原论文](./thesis/imagenet-classification-with-deep-convolutional-neural-networks.pdf)
- [2] [AlexNet介绍](https://blog.csdn.net/u013181595/article/details/80972594) 
