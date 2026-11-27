# b 站 小土堆




# w3cschool

https://www.w3cschool.cn/pytorch/

## 1 深度学习框架简介

贴个图
![[Pasted image 20230726233837.png]]

其他没有啥，主要知道一下排第一第二基本就是
PyTorch 和 TensorFlow

那么这两个框架的区别在哪里呢？作者指出一个重要的区别是“动态图”和“静态图”的区别。

PyTorch——动态图：
如果用图描述模型，那么动态图就是在构建图的引用关系时，同时计算出结果。也就是图的构建和计算是同时进行的。

TensorFlow——静态图：
静态图是先通过描述的方式建立图模型，之后再放到特定环境中获取参数、计算结果。图的构建和计算是分离的。这也意味着一旦开始计算，我们不能进行干预了，图的模型也不可更改了。
![[Pasted image 20230726234500.png]]

那么比较下来，动态图比静态图更方便调试和修改

![[Pasted image 20230726234739.png]]

## 2 功能演示
主要演示了 3 个方面：
- GPU加速
![[Pasted image 20230727093811.png]]
cuda 指代 GPU（是不是想起英伟达）
- 自动求导
![[Pasted image 20230727093748.png]]
- 常用网络层
![[Pasted image 20230727093733.png]]

## 3 安装环境

![[Pasted image 20230727093933.png]]
Anaconda是一个自动下载所需库的包管理工具

> 安装过程中遇到问题：anaconda 打不开，重装后卡在 loading application，通过
> 	conda upgrade anaconda-navigator
> 解决

> pyCharm 读取 anaconda3 的 interpretor 出现问题，找不到 conda 的可执行文件
> ![[Pasted image 20230727214445.png]]
> 解决：采用 anaconda3\\Library\\bin\\conda.bat 作为可执行文件

finally，最终效果：
![[Pasted image 20230727223125.png]]


## 4 入门

PS：感觉看视频效率有点低了，还是直接进入代码部分

### 张量

`Tensor`（张量），`NumPy` 的 `ndarray` ，但还可以在 GPU 上使用来加速计算

```python
#创建一个无初始化的 5*3 矩阵
x = torch.empty(5, 3)

#创建一个随机的 5*3 矩阵
x = torch.rand(5, 3)

#构造一个0填充且数据类型为long的矩阵
x = torch.zeros(5, 3, dtype=torch.long)

#直接从数据构造
x = torch.tensor([5.5, 3])
#x = tensor([5.5000, 3.0000])

#根据现有的tensor构造新的tensor，默认会重用数据属性
x = x.new_ones(5, 3, dtype=torch.double)

x = torch.randn_like(x, dtype=torch.float)#这里不声明类型的话会默认double

#获取张量形状
print(x.size())
#输出：torch.size([5, 3])
#注意：torch.Size 本质上还是 tuple ，所以支持 tuple 的一切操作。

```


### 运算

同一种运算有多种实现方法，下面以加法为示例

```python
#直接加
y = torch. rand(5, 3)
print(x+y)

#torch.add
print(torch.add(x, y))

#给定输出张量
output = torch.empty(5, 3)
torch.add(x, y, out = output)
print(output)

#in_place add
y.add_(x)
print(y)
#注意：任何一个就地改变张量的操作后面都固定一个 `_`
```


索引

```python
print(x[:, 1])
```

改变形状

```python
x = torch.randn(4, 4)
y = x.view(16)
z = x.view(-1, 8)
print(x.size(),y.size(),z.size())
```
PS：-1 是缺省值，会根据元素总数和其他维度的值计算得出。另外 view 本质上是把原来矩阵的值按次序（左到右、上到下）依次插入新矩阵。

取值

```python
x = torch.randn(1)
print(x)
print(x.item())
#tensor([1234])
#1234
```
PS：randn 的 n 是正态分布。。。

>更多运算参考：https://pytorch.org/docs/stable/torch.html#math-operations

### Numpy 桥接

将一个 Torch 张量转换为一个 NumPy 数组是轻而易举的事情，反之亦然。

Torch 张量和 NumPy 数组将共享它们的底层内存位置，因此当一个改变时，另外也会改变。

**将 torch 的 Tensor 转换为 NumPy 数组**
```python
a = torch.ones(5)
print(a)
#`tensor([1., 1., 1., 1., 1.])`
b = a.numpy()
print(b)
#`[1. 1. 1. 1. 1.]`

`a.add_(1)`
#a tensor([2., 2., 2., 2., 2.])
#b [2. 2. 2. 2. 2.]
```


### 将 NumPy 数组转化为Torch张量

看改变 NumPy 分配是如何自动改变 Torch 张量的：

```python
import numpy as np
a = np.ones(5)
b = torch.from_numpy(a)
np.add(a, 1, out=a)
print(a)
print(b)
#[2. 2. 2. 2. 2.]
#tensor([2., 2., 2., 2., 2.], dtype=torch.float64)
```
PS：CPU上的所有张量（ CharTensor 除外）都支持与 Numpy 的相互转换


### CUDA上的张量

张量可以使用 `.to` 方法移动到任何设备（device）上：

```python
# 当GPU可用时,我们可以运行以下代码`
# 我们将使用`torch.device`来将tensor移入和移出GPU``
if torch.cuda.is_available():
    device = torch.device("cuda")          # a CUDA device object
    y = torch.ones_like(x, device=device)  # 直接在GPU上创建tensor
    x = x.to(device)                       # 或者使用.to("cuda")方法
    z = x + y
    print(z)
    print(z.to("cpu", torch.double))       # .to 也能在移动时改变dtype

#tensor([1.0445], device='cuda:0')
#tensor([1.0445], dtype=torch.float64)
```
