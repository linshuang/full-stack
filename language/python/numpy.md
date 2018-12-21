# 引言
NumPy(Numerical Python) 是 Python 语言的一个扩展程序库，支持大量的维度数组与矩阵运算，此外也针对数组运算提供大量的数学函数库。NumPy 是一个运行速度非常快的数学库，主要用于数组计算，包含：

- 一个强大的N维数组对象 ndarray
- 复杂的（广播Broadcast）函数
- 整合 C/C++/Fortran 代码的工具
- 线性代数、傅里叶变换、随机数生成等功能

官网：http://www.numpy.org/ <br/>
官方文档：https://docs.scipy.org/doc/ <br/>
github: https://github.com/numpy/numpy <br/>

  
# ndarray对象
NumPy 最重要的一个特点是其 N 维数组对象 ndarray，它是一系列同类型数据的集合，以 0 下标为开始进行集合中元素的索引。

ndarray 对象是用于存放同类型元素的多维数组。

ndarray 中的每个元素在内存中都有相同存储大小的区域。

ndarray 内部由以下内容组成：

- 一个指向数据（内存或内存映射文件中的一块数据）的指针。
- 数据类型或 dtype，描述在数组中的固定大小值的格子。
- 一个表示数组形状（shape）的元组，表示各维度大小的元组。
- 一个跨度元组（stride），其中的整数指的是为了前进到当前维度下一个元素需要"跨过"的字节数。

## 如何创建
numpy.array(object, dtype = None, copy = True, order = None, subok = False, ndmin = 0)
```
object	数组或嵌套的数列
dtype	数组元素的数据类型，可选
copy	对象是否需要复制，可选
order	创建数组的样式，C为行方向，F为列方向，A为任意方向（默认）
subok	默认返回一个与基类类型一致的数组
ndmin	指定生成数组的最小维度
```

## dtype
numpy 的数值类型实际上是 dtype 对象的实例，并对应唯一的字符

## 属性
| 属性 | 说明 |
| ----- | ----- |
| ndarray.ndim |	秩，即轴的数量或维度的数量 |
| ndarray.shape	| 数组的维度，对于矩阵，n 行 m 列 | 
| ndarray.size	| 数组元素的总个数，相当于 .shape 中 n*m 的值 | 
| ndarray.dtype	| ndarray 对象的元素类型 | 
| ndarray.itemsize	| ndarray 对象中每个元素的大小，以字节为单位 | 
| ndarray.flags	| ndarray 对象的内存信息 | 
| ndarray.real	| ndarray元素的实部 | 
| ndarray.imag	| ndarray 元素的虚部 | 
| ndarray.data	| 包含实际数组元素的缓冲区，由于一般通过数组的索引获取元素，所以通常不需要使用这个属性。 | 

## 创建<sup>[2]
- numpy.empty(shape, dtype = float, order = 'C')
- numpy.zeros(shape, dtype = float, order = 'C')
- numpy.ones(shape, dtype = None, order = 'C')
- numpy.full(shape, fill_value[, dtype, order])
- numpy.array(object[, dtype, copy, order, subok, ndmin])
- numpy.arange([start, ]stop, [step, ]dtype=None)  # [start, stop)
- np.linspace(start, stop, num=50, endpoint=True, retstep=False, dtype=None) #线性空间
- np.logspace(start, stop, num=50, endpoint=True, base=10.0, dtype=None) # log空间
- numpy.random.randn(d0, d1, ..., dn)

## 切片和索引
ndarray对象的内容可以通过索引或切片来访问和修改，与 Python 中 list 的切片操作一样。
### 切片函数
```
a = np.arange(10)
s = slice(2,7,2)   # 从索引 2 开始到索引 7 停止，间隔为2
# a[s]=[2  4  6]
```
### 冒号:
通过冒号分隔切片参数 start:stop:step 来进行切片操作
```
a = np.arange(10)  
b = a[2:7:2]   # 从索引 2 开始到索引 7 停止，间隔为 2
# b=[2  4  6]
```
### 省略号 …
使用省略号来使选择元组的长度与数组的维度相同。 如果在行位置使用省略号，它将返回包含行中元素的 ndarray。
```
a = np.array([[1,2,3],[3,4,5],[4,5,6]])  
print (a[...,1])   # 第2列元素 [2 4 5]
print (a[1,...])   # 第2行元素 [3 4 5]
print (a[...,1:])  # 第2列及剩下的所有元素 [[2 3],[4 5],[5 6]]
```
### 整数数组索引  花式索引
```
x = np.array([[1,  2],  [3,  4],  [5,  6]]) 
y = x[[0,1,2],  [0,1,0]]  
print (y)  # [1  4  5]
```
花式索引根据索引数组的值作为目标数组的某个轴的下标来取值。
### 布尔索引
通过布尔运算（如：比较运算符）来获取符合指定条件的元素的数组。
```
x = np.array([[  0,  1,  2],[  3,  4,  5],[  6,  7,  8],[  9,  10,  11]])  
print (x[x >  5]) # [6  7  8  9 10 11]

a = np.array([np.nan,  1,2,np.nan,3,4,5])  
print (a[~np.isnan(a)])  # 非非法 [ 1.   2.   3.   4.   5.]
```




# 参考
[1] [numpy official doc](https://docs.scipy.org/doc/numpy-1.15.4/reference/index.html) <br/>
[2] [Array creation routines](https://docs.scipy.org/doc/numpy-1.15.4/reference/routines.array-creation.html) <br/>