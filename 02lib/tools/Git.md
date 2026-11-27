# 基本配置

https://blog.csdn.net/FangsTun/article/details/139899874

## 配置用户名和邮箱

> git config --global user.name "用户名" 
> 
> git config --global user.email "邮箱"
> 
> 查看配置是否正确：git config --list

## SSH部分
### 创建秘钥
ssh-keygen -C 'github账户绑定的邮箱' -t rsa



一些小练习（挺有趣的）
https://www.w3schools.com/git/exercise.asp?filename=exercise_help1

合并 commit：
https://blog.csdn.net/qq_46106285/article/details/130459829

代理加速（http和ssh都有）
https://zhuanlan.zhihu.com/p/481574024
俺的梯子（old）：
![[Pasted image 20240411144114.png]]


## 本地创建仓库

### create a new repository on the command line
```sh
echo "# worldQuant" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:C-ld/worldQuant.git
git push -u origin main
```

### push an existing repository from the command line

```sh
git remote add origin git@github.com:C-ld/worldQuant.git
git branch -M main
git push -u origin main
```



