# 超参数
## 概念
在机器学习的上下文中，超参数是在开始学习过程之前设置值的参数。通常情况下，需要对超参数进行优化，给学习机选择一组最优超参数，以提高学习的性能和效果。 相反，其他参数的值通过训练得出。

超参数：
- 定义关于模型的更高层次的概念，如复杂性或学习能力。
- 不能直接从标准模型培训过程中的数据中学习，需要预先定义。
- 可以通过设置不同的值，训练不同的模型和选择更好的测试值来决定

## 示例：
- 树的数量或树的深度
- 矩阵分解中潜在因素的数量
- 学习率（多种模式）
- 深层神经网络隐藏层数
- k均值聚类中的簇数

- layers：神经网络层数
- hidden units：各隐藏层神经元个数
- learning rate decay：学习因子下降参数
- mini-batch size：批量训练样本包含的样本个数
- $\beta_1$,$\beta_2$,$\varepsilon$ ：Adam算法参数

## 快速搜索
sklearn包提供了一组用于快速搜索超参的工具，包括：
- sklearn.model_selection.GridSearchCV
- sklearn.model_selection.ParameterGrid
- sklearn.model_selection.RandomizedSearchCV
- sklearn.model_selection.fit_grid_point