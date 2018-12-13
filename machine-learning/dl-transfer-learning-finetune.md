fine-tune是迁移学习里的一种典型手段，直观上接近于参数迁移方法，且有特征表示迁移方法的味道。fine-tune使用已经在某一领域对某一任务训练好的神经网络迁移至另一领域和任务。<sup>[1]</sup>

深度卷积神经网络里浅层网络的特征往往更具通性，而高层的特征则跟数据集更紧密关联。

# 为什么进行fine-tune<sup>[2]
当数据集过小时，极易过拟合。

# 什么时候进行fine-tune<sup>[2]
通常来讲，假如我们的数据集与预训练模型所用数据集在上下文上并非剧烈不同,我们就可以使用fine-tune。在一个丰富且巨大的数据集上预训练过的网络能够在浅层补货一些通用的特征，例如曲线和边,而这些特征对绝大多相似数任务是有用的。

假如，我们的数据集源自一些非常特定的领域，同时也无法找到这些领域上的模型，那么我们应该从头开始训练模型。

另一问题，假如我们的数据比较小，训练时我们的模型往往更容易过拟合，那么fine-tune往往能够带来更好的结果。

如果，我们的数据集真的非常小，例如小于1000，那么一个更好的办法是拿到中间层的输出作为特征，然后在此之上应用其它机器学习手段例如SVM来解决问题。

# fine-tune技巧<sup>[2]
1. 去除最后一层用新的跟任务相关的层代替。例如1000个分类的softmax替换成10个分类
2. 使用更小的learning rate
3. 冻结前面几层的权重


# 参考
[1] [fine tuning](http://wiki.fast.ai/index.php/Fine_tuning)<br/>
[2] [A Comprehensive guide to Fine-tuning Deep Learning Models in Keras ](https://flyyufelix.github.io/2016/10/03/fine-tuning-in-keras-part1.html)<br/>