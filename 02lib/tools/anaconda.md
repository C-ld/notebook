# 默认环境安装位置

> 注意，conda 创建新环境时，默认 env_dir 在 C 盘，修改 .condarc 如果不生效，查看 base environment 是否是 read only，如果是则修改权限
# conda指定目录安装环境
https://blog.csdn.net/XC___XC/article/details/134013893


# Anaconda指定虚拟环境目录、常用操作
https://blog.csdn.net/kalimera/article/details/127408786


# 没有找到 mkl_rt，openblas，lapack 的库

这个报错通常表示你的系统或者Python环境中没有找到名为`mkl_rt`、`openblas`或`lapack`的数学库。这些库通常用于加速线性代数运算，特别是矩阵运算和数值计算。

解决方法：

1. 如果你使用的是Anaconda发行版的Python，可以尝试通过conda来安装相关的包：

```
conda install mkl-service
```

或者，如果你需要openblas：

```
conda install -c conda-forge openblas
```

对于LAPACK，通常它会作为其他库的依赖被自动安装，但如果需要，可以尝试：

```
conda install lapack
```


# VSCode 使用指定conda python环境
https://blog.csdn.net/m0_47199826/article/details/134367070

- 打开工作区
- `Ctrl + Shift + P`
- 输入 Python: Select Interpreter
- 选择 conda 环境即可

# 换源
https://blog.csdn.net/adreammaker/article/details/123396951?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-1-123396951-blog-135087773.235^v43^pc_blog_bottom_relevance_base5&spm=1001.2101.3001.4242.2&utm_relevant_index=4


# VSCode 同时出现两个环境

如下：
```bash
(uw) (base) [ld@gpu underWater]$ conda deactivate
```

解决方法：
```bash
conda config --set auto_activate_base False
```
