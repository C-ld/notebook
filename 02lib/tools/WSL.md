
## 配置wslconfig
https://learn.microsoft.com/en-us/windows/wsl/wsl-config#wslconfig

## 虚拟磁盘压缩
https://superuser.com/questions/1606213/how-do-i-get-back-unused-disk-space-from-ubuntu-on-wsl2
- `wsl.exe --shutdown`
- cd into ext4.vhdx folder that typically is `cd C:\Users\<user>\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu20.04onWindows_79rhkp1fndgsc\LocalState\`
- `optimize-vhd -Path .\ext4.vhdx -Mode full` (Make sure the ext4.vhdx is not clicked on by mouse, because that would lead to the error of the the file is being used by another process in powershell)



