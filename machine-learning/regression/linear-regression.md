# 线性回归
## 模型
$$h_w(x^i)=w_0+w_1x_1+w_2x_2+...+w_nx_n$$
$$h_W(X)=W^TX$$
$$X= \left[
  \begin{matrix}
   1  \\
   x_1 \\
   ... \\
   x_n
  \end{matrix}
  \right] 
  W= \left[
  \begin{matrix}
   w_0  \\
   w_1 \\
   ... \\
   w_n
  \end{matrix}
  \right] 
  $$
## 损失函数
### 均分误差（MSE）
$$Loss(W)=\frac{1}{2N}\sum_{i=1}^{M}(h_w(x^i)-y^i)^2=\frac{1}{2N}(XW-Y)^T(XW-Y)$$

## 学习算法
## 满秩
直接求导可以得到结果
$W=(X^TX)^{-1}X^TY$
## 不满秩
梯度下降算法


## 参考
- [1] [线性回归](https://www.jianshu.com/p/1aa60f618f80)
- [1] [机器学习——线性回归浅谈（Linear Regression）](https://www.cnblogs.com/GuoJiaSheng/p/3928160.html)