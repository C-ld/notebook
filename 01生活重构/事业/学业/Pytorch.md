
辅助1：[pytorch 实战](https://www.bilibili.com/video/BV1ov4y1H7GK?spm_id_from=333.788.videopod.episodes&vd_source=83876cd4a75f8c522c8297ad6b626348&p=50)

辅助2：[音频特征提取和预处理](https://www.bilibili.com/video/BV1Px4y1T7QW/?spm_id_from=333.337.search-card.all.click&vd_source=83876cd4a75f8c522c8297ad6b626348)

辅助3：[动手学深度学习](https://zh-v2.d2l.ai/chapter_installation/index.html)

# 课程笔记

[小土堆 b 站链接](https://www.bilibili.com/video/BV1hE411t7RN/?spm_id_from=333.337.search-card.all.click&vd_source=c8e241c7381640b73195e99da6f2222f)

> annaconda 环境下根据官网命令 conda install pytorch。IDE 推荐用 pyCharm 社区版，里面的  python cosole 可以实时看到变量（类似 matlab）很好用。
> 注意，python 依赖讲究一个配套，官网装的 pytorch 就用官网的命令装 tensorboard，不要乱用依赖（尤其是 pip 换源但 conda 没换源的情况）



## 自学法宝 dir() && help()

```python
# 打开，看见
dir()

# 说明书
help()

```

> shift + Enter 跳到下一行

## 几种编程方式对比

python File
以文件为单位运行，适合整体调试

python Console
以语句为单位运行，适合逐行调试
》实时显示变量属性

jupyter 
以自定义块运行，较为灵活
启动方式：conda env 中 jupyter notebook

> 教程中的配合方式就是 利用 IDE 的提示信息和 Console 的即时输出看怎么用，jupyter 做代码记录和看运行结果

## 数据加载相关

- Dataset
	- 提供一种方式去获取数据及其 label
		- 如何获取每个数据及其 label
		- 统计总共有多少个数据
- Dataloader
	- 为网络提供不同的数据形式
 
### 文件读取

具体读取方法要根据数据集格式决定，但是一些常用方法如下：

- 文件
```python
import os
# make all filenames in path a list
os.listdir(path)

# join
os.path.join(a, b, ...)
```

- 图片读取
```python
from PIL import Image
Image.open(fpath)
```

- 音频读取
```python
waveform, sample_rate = torchaudio.load(fp)
# fp can be file path or a file object
```

### Dataset

主要是要重写 \_\_getitem\_\_ 和 \_\_len\_\_ 两个函数，示例如下：

```python
from torch.utils.data import Dataset
class myDataset(Dataset):
	def __init__(self, rt_dir, lb_dir):
		self.rtdir = rt_dir
		self.lbdir = lb_dir
		self.path = os.path.join(rt_dir, lb_dir)
		self.img_names = os.listdir(self.path)
	
	def __getitem__(self, idx):
		img_name = self.img_names[idx]
		img_path = os.path.join(self.path, img_name)
		img = Img.open(img_path)
		label = self.label_dir
		return img, label
	
	def __len__(self):
		return len(self.img_names)

rt_dir = 'aaa'
lb1_dir = 'label_1'
lb2_dir = 'label_2'
ds1 = myDataset(rt_dir, lb1_dir)
ds2 = myDataset(rt_dir, lb2_dir)

# notice this!
dataset = ds1 + ds2


data, label = dataset[index]
```


### 数据集（torchvision自带）
```python
import torchvision

torchvision.datasets.CIFAR10(root: Union[str, Path], train: bool = True, transform: Optional[Callable] = None, target_transform: Optional[Callable] = None, download: bool = False)

train_set = torchvision.datasets.CIFAR10("./data", True, download = True)

```

### DataLoader

[torch.utils.data.DataLoader](https://pytorch.org/docs/stable/data.html#torch.utils.data.DataLoader)

注意，DataLoader 的 num_workers 参数听说 windows 有点问题，就默认为 0 吧，后续如果上 linux 再调整。
```python
from torch.utils.data import DataLoader
test_loader = DataLoader(
	 dataset=myDataset,
	 batch_size=16,
	 shuffle=True,  #random sample order for different epochs
	 drop_last=False
	 )

for epoch in range(2):
	step = 0
	for batch in test_loader:
		datas, labels = batch
		...
		step = step + 1
	

```

### 数据集划分

```python
import torch.utils.data as Data

dataset = Data.TensorDataset(torch.FloatTensor(a), torch.FloatTensor(b))
batch_size = 16
# 将数据集分为训练集和测试集
trainLoader, testLodaer = Data.random_split(
							dataset,
							lengths=[int(0.9 * len(dataset)),
							len(dataset) - int(0.9 * len(dataset))],
							generator=torch.Generator().manual_seed(0))
```


## 可视化相关

### TensorBoard 初步

writer 里面有 add_scalar，add_image，add_audio 函数

#### add_scalar

add_scalar 添加标量，适用于后续对训练过程的一个检测和记录

```python

def add_scalar(self, tag, scalar, step, walltime, new_style)

###########################
from torch.utils.tensorboard import SummaryWriter  
  
writer = SummaryWriter("logs")  
  
for i in range(0, 100):  
    writer.add_scalar("f:y=2x", 2*i, i)  
  
writer.close()

```

启动 tensorboard
```sh
tensorboard --logdir=logs --port=6007
```

#### add_image

添加图片序列，可以用于谱图、图片数据集、可视化效果输出

```python

def add_image(  
    self, tag, img_tensor, global_step=None, walltime=None, dataformats="CHW"  
):
# notice that the dataformats should be consistent with shape of image
# img_tensor (torch.Tensor, numpy.ndarray, or string/blobname): Image data
```


## 预处理相关

### torchvision.transforms

需要注意不同类的 \_\_call\_\_ 的输入输出，尤其在 Compose 构建流水线的时候。
关注参数（在构建类时需要）

```python
from torchvision import transforms

# 调用: transforms.xxx()

Compose: 类似于tensorflow静态图，可以组合多种 transform
	transforms.Compose([
		 transforms.CenterCrop(10),
		 transforms.PILToTensor(),
		 transforms.ConvertImageDtype(torch.float),
	])

ToTensor()
ToPILImage()
Normalize()
Resize()# Resize(512) 会等比例缩放（短边=512）；Resize((512,512))会缩放成正方形

```


## 神经网络搭建（torch.nn）

网络搭建相关的工具主要在 torch.nn 包中，[链接](https://pytorch.org/docs/stable/nn.html)

- Container 容器或者叫骨架，搭建神经网路其实就是往容器中去填一些具体的结构
	- Module 为所有神经网络实现提供的基本 class
	- Sequential
	- ModuleList
	- ModuleDict
	- ParameterList
	- ParameterDict

### torch.nn.Module

搭建网络的基本单元

```python
from torch import nn
import torch.nn.functional as F
import torch

class myModule(nn.Module):
	def __init__(self):
		super(myModule, self).__init__()
		self.conv1 = nn.Conv2d(1,20,5)
		slef.conv2 = nn.Conv2d(20,20,5)
	
	def forward(self, x):
		x = F.relu(self.conv1(x))
		return F.relu(self.conv2(x))

x = torch.Tensor([...])
model = myModule()
y = model(x)


```

### torch.nn.Sequantial

可以不用继承 module 实现快速网络搭建

https://pytorch.org/docs/stable/generated/torch.nn.Sequential.html#torch.nn.Sequential

```python
model = nn.Sequential(
          nn.Conv2d(1,20,5),
          nn.ReLU(),
          nn.Conv2d(20,64,5),
          nn.ReLU()
        )

# Using Sequential with OrderedDict. This is functionally the
# same as the above code
model = nn.Sequential(OrderedDict([
          ('conv1', nn.Conv2d(1,20,5)),
          ('relu1', nn.ReLU()),
          ('conv2', nn.Conv2d(20,64,5)),
          ('relu2', nn.ReLU())
        ]))
```



### 卷积层

通过卷积核提取输入图的特征图，卷积核相当于是网络学习的权重参数
[pytorch中Conv1d、Conv2d与Conv3d详解](https://blog.csdn.net/lsb2002/article/details/134734168?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-1-134734168-blog-142464867.235^v43^control&spm=1001.2101.3001.4242.2&utm_relevant_index=4)

[pytorch Conv2d 官方文档](https://pytorch.org/docs/stable/generated/torch.nn.Conv2d.html#torch.nn.Conv2d)

### 池化层

主要用于降采样
[pytorch MaxPool2d 官方文档](https://pytorch.org/docs/stable/generated/torch.nn.MaxPool2d.html#torch.nn.MaxPool2d)

### 非线性激活

[pytorch](https://pytorch.org/docs/stable/nn.html#non-linear-activations-weighted-sum-nonlinearity)

relu，sigmoid。。。。
注意输入参数的格式 (N, * )，以及参数 inplace 的使用，默认是 False

### 正则化层

正则化层可以加快模型收敛速度，主要是 BatchNorm 这种

### 循环层

RNN、LSTM、GRU 等

### Transformer 层

封装了 transformer 架构

### 线性层（全连接层）

nn.Linear(input_f, output_f)

本质上还是对特征的映射或者说重表达。

### Dropout 层（遗忘层）

主要为了防止网络过拟合

### Sparse 稀疏层

Embedding 和 EmbeddingBag ，说是主要 NLP 用


### 距离函数

余弦相似度和矩阵对应元素的欧氏距离（pairwise）

### 损失函数

常用：
- L1Loss
- MSE
- CrossEntropyLoss（多分类问题）


## 训练网络（含验证）

### 反向传播

```python
result_loss = loss(output, ...)
result_loss.backward()
```

### 优化器 torch.optim

```python
optimizer = optim.SGD(model.parameters(), lr=0.01, momentum=0.9)
optimizer = optim.Adam([var1, var2], lr=0.0001)


for input, target in dataset:
    optimizer.zero_grad()
    output = model(input)
    loss = loss_fn(output, target)
    loss.backward()
    optimizer.step()
```

### \*\*模型训练套路

- 准备数据集（Dataset）
- 准备数据导入（DataLoader）
- 搭建神经网络
- 创建网络模型
- 确定损失函数
- 确定优化器
- 确定训练参数
	- 训练次数 train_step
	- 测试次数 test_step
	- 训练轮数 epoch
- 训练部分
	- 获取数据
	- 得到模型输出
	- 得到损失
	- 优化器优化（反向传播）
	- 测试/验证（记录正确率和Loss，可以输出到 tensorboard）
	- 保存模型

见 [torchLearn](E:\VS_Code\proj\torchLearn)

### 利用 GPU 加速

Google colab 每周 30 小时免费的 16G 显存显卡使用时间

方法一（较麻烦，不常用）
- 找到：
	- 网络模型
	- 数据（输入、标注）
	- 损失函数
- 然后调用其 cuda() 即可

方法二（常用）

```python
device = torch.device("cpu")
device = torch.device("cuda")
device = torch.device("cuda:0")

device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
```

```python
# 模型、数据、标签、损失函数
model.to(device)
loss.to(device)

inputs = inputs.to(device)
labels = labels.to(device)
```


## 库中已有网络修改

参照官网的新写法：
可以在网络模型或者其子模块上进行修改

```python
from torchvision.models import resnet50, ResNet50_Weights

# Old weights with accuracy 76.130%
resnet50(weights=ResNet50_Weights.IMAGENET1K_V1)


import torchvision
vgg = torchvision.models.vgg11()
print(vgg) # Print the structure of vgg

vgg.add_module("fc", torch.nn.Linear(1000, 10)) # add a new linear layer 
print(vgg)

vgg.classifier.add_module("fc", torch.nn.Linear(1000, 10))
print(vgg)

vgg.classifier[6] = torch.nn.Linear(4096, 10) # modify
```

## 网络模型存取

m1：同时保存模型和参数（存在陷阱：load 时必须引入网络模型的类定义，否则报错）

```python
vgg = torchvision.models.vgg11()
# 保存模型和参数
torch.save(vgg, "vgg_method1.pth")
# 加载模型和参数
model = torch.load("vgg_method1.pth")
```

m2：仅保存参数 dict

```python
vgg = torchvision.models.vgg11()
# 保存参数（官方推荐）
torch.save(vgg.state_dict(), "vgg_method2.pth")
# 加载参数
vgg2 = torchvision.models.vgg11()
vgg2.load_state_dict(torch.load("vgg_method2.pth"))
```



