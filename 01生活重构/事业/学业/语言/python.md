
# 类成员

方法和变量的访问控制类似，默认 public ，单下划线 protected（实际不起作用），双下划线 private（起作用，类外访问会引起 attributeError）



# Matplotlib

## plt.subplots()的基本用法

[plt: subplots()详解](https://blog.csdn.net/2301_77444219/article/details/140455714)

plt.subplots()返回一个包含figure和axes对象的元组，通常可以将其分解为fig和ax两个变量。例如： 
fig, ax = plt.subplots()  
在这个例子中，fig是整个图形，ax是子图。

```python
x = np.linspace(-1, 1, 100)
print(type(x))
y = x ** 2
plt.figure(1)
 
plt.subplot(2, 2, 1)
plt.plot(x, y)
 
plt.subplot(2, 2, 2)
plt.plot(x, y)
 
plt.subplot(2, 1, 2)
plt.plot(x, y)
 
plt.show()
```

![[Pasted image 20241102151333.png]]

```python
import matplotlib.pyplot as plt
import numpy as np
 
# 创建一个2x2的网格
fig, axs = plt.subplots(2, 2)
 
# 在每个子图中绘制一个图形
for ax in axs.flat:
    # 随机生成一些数据
    x = np.random.rand(100)
    y = np.random.rand(100)
    # 绘制散点图
    ax.scatter(x, y)
 
# 显示图形
plt.show()
```

![[Pasted image 20241102151403.png]]
