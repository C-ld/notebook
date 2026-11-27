当前问题Issue：

## Oceanbase

OBClient启动：
```
./deps/3rd/u01/obclient/bin/obclient -h127.0.0.1 -P10000 -uroot -Doceanbase -A -p4yAwyNYh0CZl1XCSlCIT
```


测试租户
```
|      1002 | test_t1     | USER        | 2024-04-09 18:36:18.172787 | 2024-04-09 18:36:56.458663 | zone1        | FULL{1}@zone1 | NULL              | MYSQL              | NORMAL | NO            | NO     | PRIMARY     | NORMAL            |                0 | 1712659044722292001 | 1712659044722292001 | 1712659044722292000 | 4611686018427387903 | NOARCHIVELOG | DISABLED                   |        1 | 4.2.3.0    |      1001 |
```
root密码 123

登录租户
```
./deps/3rd/u01/obclient/bin/obclient -h127.0.0.1 -P10000 -uroot@test_t1 -A -p123 [-Ddb1]
```

运行oblogminer
```
oblogminer -c "127.0.0.1:10000" -u "root@test_t1" -p "123" -s "1712659018000000" -o "file:///root/oblog/logminer_output/" -L "ALL.*:WARN;PALF.*:WARN;SHARE.SCHEMA:WARN"
```

基本SQL操作
https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000639757

测试结果：
修改后
```
    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
 406800 root      20   0 1132992 307828  68784 R  80.0   6.1   0:00.12 oblogminer      
 406800 root      20   0 3436544   1.1g  86420 R  90.0  22.0   0:01.02 oblogminer      
 406800 root      20   0 3545892   1.1g  88668 S  20.0  23.4   0:01.22 oblogminer      
 406800 root      20   0 3706172   1.2g  89396 S  13.0  24.4   0:01.35 oblogminer      
 406800 root      20   0 3762748   1.2g  90144 S  11.0  25.1   0:01.46 oblogminer      
 406800 root      20   0 3785532   1.2g  90144 S  11.9  25.8   0:01.58 oblogminer      
 406800 root      20   0 3785532   1.2g  90144 S   7.0  25.8   0:01.65 oblogminer      
 406800 root      20   0 3785532   1.2g  90144 S   6.0  25.8   0:01.71 oblogminer      
 406800 root      20   0 3785532   1.2g  90188 S   7.0  25.8   0:01.78 oblogminer      
 406800 root      20   0 3785532   1.2g  90188 S   7.0  25.8   0:01.85 oblogminer      
 406800 root      20   0 3785532   1.2g  90188 S   6.9  25.8   0:01.92 oblogminer      
 406800 root      20   0 3785532   1.2g  90188 S   7.0  25.8   0:01.99 oblogminer      
 406800 root      20   0 3785532   1.2g  90188 S   7.0  25.8   0:02.06 oblogminer      
 406800 root      20   0 3785532   1.2g  90188 S   7.0  25.8   0:02.13 oblogminer      
 406800 root      20   0 3785532   1.2g  90188 S   7.0  25.8   0:02.20 oblogminer      
 406800 root      20   0 3785532   1.2g  90188 S   5.9  25.8   0:02.26 oblogminer      
 406800 root      20   0 3785532   1.2g  90188 S   6.0  25.8   0:02.32 oblogminer      
 406800 root      20   0 3785532   1.3g  90188 S   7.0  26.0   0:02.39 oblogminer      
 406800 root      20   0 3785532   1.3g  90188 S   5.0  26.0   0:02.44 oblogminer      
 406800 root      20   0 3785532   1.3g  90188 S   4.0  26.0   0:02.48 oblogminer      
 406800 root      20   0 3785532   1.3g  90188 S   4.0  26.0   0:02.52 oblogminer      
 406800 root      20   0 3780156   1.3g  90724 S   3.0  26.0   0:02.55 oblogminer      
 406800 root      20   0 3777084   1.2g  90784 S   2.0  25.9   0:02.57 oblogminer
```

修改前：
```
    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
 428346 root      20   0 1132992 388704  75528 R  73.3   7.7   0:00.11 oblogminer 
 428346 root      20   0 3520020   1.1g  95312 R  93.0  22.7   0:01.04 oblogminer 
 428346 root      20   0 3546092   1.1g  96624 S   5.0  23.5   0:01.09 oblogminer  
 428346 root      20   0 3746052   1.2g  97396 S  95.0  24.6   0:02.04 oblogminer 
 428346 root      20   0 3788544   1.2g  98120 S 102.0  25.2   0:03.07 oblogminer  
 428346 root      20   0 3811072   1.2g  98184 S 106.0  26.0   0:04.13 oblogminer 
 428346 root      20   0 3811072   1.2g  98184 S  99.0  26.0   0:05.12 oblogminer  
 428346 root      20   0 3811072   1.2g  98184 S 102.0  26.0   0:06.14 oblogminer  
 428346 root      20   0 3811072   1.2g  98184 S 101.0  26.0   0:07.15 oblogminer   
 428346 root      20   0 3811072   1.2g  98184 S 104.0  26.0   0:08.19 oblogminer  
 428346 root      20   0 3811072   1.2g  98184 S 104.0  26.0   0:09.24 oblogminer  
 428346 root      20   0 3811072   1.3g  98184 S 103.0  26.0   0:10.27 oblogminer  
 428346 root      20   0 3811072   1.3g  98184 S  98.0  26.0   0:11.25 oblogminer 
 428346 root      20   0 3811072   1.3g  98184 S 102.0  26.0   0:12.27 oblogminer  
 428346 root      20   0 3811072   1.3g  98184 S 102.0  26.0   0:13.30 oblogminer  
 428346 root      20   0 3811072   1.3g  98184 S 102.0  26.0   0:14.32 oblogminer 
 428346 root      20   0 3811072   1.3g  98184 S 102.0  26.0   0:15.35 oblogminer 
 428346 root      20   0 3811072   1.3g  98184 S 102.0  26.2   0:16.37 oblogminer
 428346 root      20   0 3811072   1.3g  98184 S 103.0  26.2   0:17.40 oblogminer      
 428346 root      20   0 3811072   1.3g  98184 S 102.0  26.2   0:18.42 oblogminer      
 428346 root      20   0 3811072   1.3g  98184 S 103.0  26.2   0:19.45 oblogminer      
 428346 root      20   0 3811072   1.3g  98184 S 101.0  26.2   0:20.46 oblogminer      
 428346 root      20   0 3811072   1.3g  98184 S  15.8  26.2   0:20.62 oblogminer      
 428346 root      20   0 3805440   1.3g  99260 S   2.0  26.2   0:20.64 oblogminer      
 428346 root      20   0 3575240   1.2g  99308 R  79.0  25.8   0:21.43 oblogminer      
 428346 root      20   0 3575240   1.2g  99308 R  99.0  25.8   0:22.42 oblogminer      
 428346 root      20   0 3575240   1.2g  99308 R 100.0  25.8   0:23.42 oblogminer
```
# 1 安装

https://docker.easydoc.net/doc/81170005/cCewZWoN/lTKfePfP#nav_6

https://www.cnblogs.com/luzhuangzhi/p/17869210.html

报错：refused actively
troubleshoot -> restart

# 2 运行

官方 Tutorial
https://learn.microsoft.com/zh-cn/visualstudio/docker/tutorials/docker-tutorial?WT.mc_id=vscode_docker_aka_helppanel

首先，Container是进程，那么生成进程需要镜像image，镜像通过 docker pull \<imageName\>
或 docker build -t \<DockerFile\> 得到

然后 docker run --privileged -it -d --name tomcat01 -p 1216:8080 tomcat:centos


## 保存容器到镜像+修改端口 or 目录映射

https://blog.csdn.net/qq_39523365/article/details/136316531?spm=1001.2101.3001.6650.3&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EYuanLiJiHua%7EPosition-3-136316531-blog-130917053.235%5Ev43%5Epc_blog_bottom_relevance_base2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EYuanLiJiHua%7EPosition-3-136316531-blog-130917053.235%5Ev43%5Epc_blog_bottom_relevance_base2&utm_relevant_index=6

## ubuntu换源



# Troubleshoot

## 当前报错
oblogminer 启动失败
init cdc instance failed，怀疑是时间戳过小
https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000509841
应该是未开启归档的，那么时间戳最小值是（要大于它）。同时要是微秒（末三位为0）
`1712659016352887`



## 本地Log检索
```powershell
code $Env:LOCALAPPDATA\Docker\log
```



## docker run 后 容器一直 exit(0) 解决方法：
> https://blog.csdn.net/qq_47346664/article/details/119887402?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-119887402-blog-136102125.235^v43^pc_blog_bottom_relevance_base2&spm=1001.2101.3001.4242.1&utm_relevant_index=3

## 重启后连接不上Docker 报错 500，Make sure the docker deamon is 
检查Docker Server是否整，可通过 docker version 命令检查。如果出现错误则通过 Docker--troubleshoot--restart

## 在docker内运行make或其他指令时，内存超限导致docker serever crash
解决方案：配置wslconfig

## docker 20.04 换清华源 apt get 报错 no release
换阿里源
PS：注意源的版本是否对应，docker 20.04 是 focal 不是 debian
https://blog.csdn.net/m0_58745995/article/details/124103612



## \*docker make 时会导致 backend 崩溃
https://github.com/docker/for-win/issues/13022
破案了：是内存不够了，docker server直接crash情有可原
配置wslconfig:
https://learn.microsoft.com/en-us/windows/wsl/wsl-config#wslconfig


## Extra

https://blog.csdn.net/m0_58172370/article/details/128632589



[
'https://zenodo.org/api/records/3577451/files/2012.zip/content', 
'https://zenodo.org/api/records/3577451/files/2013.zip/content', 
'https://zenodo.org/api/records/3577451/files/2011.zip/content', 
'https://zenodo.org/api/records/3577451/files/2014.zip/content', 
'https://zenodo.org/api/records/3577451/files/2015.zip/content', 
'https://zenodo.org/api/records/3577451/files/2016.zip/content', 
'https://zenodo.org/api/records/3577451/files/daily_weather_report.csv/content', 
'https://zenodo.org/api/records/3577451/files/2010.zip/content'
]