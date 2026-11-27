
# Mac

https://blog.csdn.net/Y525698136/article/details/135689813

brew安装（有可能会很慢，因为依赖国外节点）

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

安装

```
brew install mysql
```

> 注意 mysql 9.0 开始会禁用老的加密方式，但我用nodejs写后端连数据库的时候发现会报错，目前能解决的方法就是回退版本，而且要删干净数据才行

参考资料：
https://developer.aliyun.com/article/1556765
https://stackoverflow.com/questions/77663611/cant-start-brew-installed-mysql-after-downgrading-from-8-2-to-8-0-35
The following worked for me:

1 - Make sure original mysql files are removed:

sql

`brew uninstall mysql brew cleanup rm -rf /opt/homebrew/etc/my.cnf rm -rf /opt/homebrew/var/mysql`

2 - Install mysql 8.0

sql

`brew install mysql@8.0`

3 - Disable the service and remove the launchdaemon and fix ownership of homebrew files [Source explaining this part] : ([Homebrew MySQL 8.0.18 on macOS 10.15 Catalina won't run as service](https://stackoverflow.com/questions/58466272/homebrew-mysql-8-0-18-on-macos-10-15-catalina-wont-run-as-service/58487534#58487534))

sql

`# stop and unload the launchdaemon     sudo launchctl unload -w /Library/LaunchDaemons/homebrew.mxcl.mysql@8.0.plist # remove the lauchdaemon file sudo rm -f /Library/LaunchDaemons/homebrew.mxcl.mysql@8.0.plist sudo rm -f /tmp/mysql.sock /tmp/mysqlx.sock sudo chown -R "$(whoami):admin" /opt/homebrew/`

Now start mysql should work:

sql

`brew services start mysql`
