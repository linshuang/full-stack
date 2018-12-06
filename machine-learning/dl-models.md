# 引言
本文档记录了经典的（深度）神经网络模型

# 远古
- [感知机 Perceptron](dl-perceptron.md)
- [多层感知机 Multi-Layer Perceptron](dl-perceptron.md) 
- RBM受限玻尔兹曼机

# 分类模型
## AlexNet
特点：
- 深、宽
- Dropout
```
训练时使用Dropout随机忽略一部分神经元，防止过拟合，增加模型的泛化能力
```
- [ReLU激活函数](dl-activation.md)
- Local Response Normalization(LRN)
```
模仿上午神经系统的侧一直机制，对局部神经元的活动创建竞争机制，使得其中响应比较大的值变得相对更大，并抑制其他反馈较小的神经元，增强了模型的泛化能力
```
- 使用CUDA加速深度卷积网络的训练
## VGG
VGG16
VGG19
## ResNet
ResNet50
ResNet101
## Inception v3
## Xception
## MobileNet
## DenseNet


# 对象检测
## RCNN
RCNN fast-RCNN faster-RCNN  
## Yolo

# 语义分割/实例分割
## FCN
## U-Net
## mask-RCNN
## FCIS（Fully Convolutional Instance-aware Semantic Segmentation）

# NLP模型
## RNN
## LSTM

# 产生式模型
## GAN

# 参考
tbd