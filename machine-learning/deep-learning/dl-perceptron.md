# Perceptron感知机
## 普通感知机<sup>[1][2]
假设输入空间$X$，输出空间$Y$。输入$x\in X$表示实例的特征向量，输出$y\in Y$表示实例的类别。由输入空间到输出空间的函数$f(x)=sign(wx+b)$称为感知机。其中，w和b为感知机模型参数，w叫做权值或权值向量，b叫做偏置（bias），sign为负号函数（输入>=0输出+1，否则输出-1）。

特点：
- 线性
- 全连接
- 前向传播

缺陷：
- 无法处理非线性任务
## Multi-Layer Perceptron多层感知机<sup>[3]
MLP（Multi-Layer Perceptron），即多层感知器，是一种前向结构的人工神经网络，映射一组输入向量到一组输出向量。MLP至少包含三层：一个输入层，一个隐藏层和一个输出层。除了输入节点，每个节点都是一个带有非线性激活函数的神经元（或称处理单元）。采用反向传播技术进行训练。由于多层以及非线性神经元的缘故，MLP能够处理非线性任务。

![avatar](https://gss1.bdstatic.com/9vo3dSag_xI4khGkpoWK1HF6hhy/baike/c0%3Dbaike80%2C5%2C5%2C80%2C26/sign=a7de1807f0faaf5190ee89eded3dff8b/aec379310a55b319820132544ea98226cffc1712.jpg)


特点：
- 非线性
- 全连接
- 前向传播

缺陷：
- 表达能力有限


## 参考
[1] [从感知机到深度网络](https://www.cnblogs.com/xiaowanyer/p/3701944.html) <br/>
[2] [感知机](https://blog.csdn.net/sinat_30537123/article/details/74141220) <br/>
[3] [多层感知机wiki] (https://en.wikipedia.org/wiki/Multilayer_perceptron)