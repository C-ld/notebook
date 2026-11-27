# windows pytorch安装
https://learn.microsoft.com/en-us/windows/ai/windows-ml/tutorials/pytorch-installation

# torch.utils.data.Dataset

https://pytorch.org/tutorials/beginner/basics/data_tutorial.html

## Creating a Custom Dataset for your files

A custom Dataset class must implement three functions: \_\_init\_\_, \_\_len\_\_, and \_\_getitem\_\_. Take a look at this implementation; the FashionMNIST images are stored in a directory `img_dir`, and their labels are stored separately in a CSV file `annotations_file`.

In the next sections, we’ll break down what’s happening in each of these functions.

```python
import os
import pandas as pd
from torchvision.io import read_image

class CustomImageDataset(Dataset):
    def __init__(self, annotations_file, img_dir, transform=None, target_transform=None):
        self.img_labels = pd.read_csv(annotations_file)
        self.img_dir = img_dir
        self.transform = transform
        self.target_transform = target_transform

    def __len__(self):
        return len(self.img_labels)

    def __getitem__(self, idx):
        img_path = os.path.join(self.img_dir, self.img_labels.iloc[idx, 0])
        image = read_image(img_path)
        label = self.img_labels.iloc[idx, 1]
        if self.transform:
            image = self.transform(image)
        if self.target_transform:
            label = self.target_transform(label)
        return image, label
```



# 损失函数 torch.nn.functional.*

## mse_loss

`torch.nn.functional.mse_loss` 函数的返回值是一个 `torch.Tensor` 类型的对象。这个返回的张量是一个标量（也就是它包含一个单一的元素），表示计算出的均方误差损失值。

例子：
```python
import torch 
# 输入和目标 
input = torch.randn(3, 5, requires_grad=True) 
target = torch.randn(3, 5) 
# 计算MSE Loss loss = torch.nn.functional.mse_loss(input, target) 
print(type(loss)) 
# 输出：<class 'torch.Tensor'> print(loss.item()) 
# 输出实际的损失值，是一个Python float类型
```

Q：现在我对于mse_loss传入的真实值和预测值维度都是(1,20,512,512,1)然后我发现返回的值也是一个维度相同的张量而非一个标量，为什么会这样?

>`torch.nn.functional.mse_loss`的默认行为是返回一个标量，对应了输入和目标之间的均方误差。然而，如果你在`torch.nn.functional.mse_loss`中设置`reduction='none'`，那么函数将返回一个新的张量，其中每个元素是输入和目标张量对应元素的均方误差，而不再是一个标量。这种情况下，返回的张量与输入和目标张量具有相同的尺寸。
>
>如果你想要一个总体的损失值（一个标量），你可能需要考虑再次对你的损失结果进行平均或求和，如使用`torch.mean(loss)`或`torch.sum(loss)`。

hin好，现在可以 train 了，记录一下：
![[b2a4603fa270209ac4d2b64dff5ca15.png]]

但是 train 完超参数没存下来，那么问题来了，怎么存呢？

看了一下能用的函数，果然只能用框架现成的 `Trainer::save_checkpoint()` 才行，里面有专门的格式封装。干脆就直接在 `on_train_end` 里实现了，搞定。

```python
	def on_train_end(self):
        self.trainer.save_checkpoint(epoch=self.current_epoch)
```

