
官方下载器
https://zenodo.org/records/1261813

![[Pasted image 20240408131731.png]]

# Dataset download
未验证：添加代理
https://blog.csdn.net/qq_38463737/article/details/124343401
未验证：curl
https://github.com/zenodo/zenodo/issues/1888

Here are the steps to download the files  
from commandline itself

1- Accquire the personal token (which you probably already have)  
2- run the following commands

`curl --cookie zenodo-cookies.txt "https://zenodo.org/record/1117372?token=<your token here>"`  
replace that record number 1117372 with your appropriate one

Once you have generated the cookie file ,  
now just get on your browser , right click on the download button and copy its url

it should look something like [https://zenodo.org/record/1117372/files/musdb18.zip?download=1](https://zenodo.org/record/1117372/files/musdb18.zip?download=1)
now ,

just run this command on your terminal ,

```
curl --cookie zenodo-cookies.txt "https://zenodo.org/record/1117372/files/musdb18.zip?download=1" --output musdb18.zip
```

replace the download link with your particular download url  
and it should start downloading ,

and replace the filename on --output musdb18.zip

with the appropriate file name you want