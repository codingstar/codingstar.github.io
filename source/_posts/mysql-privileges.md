title: mysql访问权限控制命令
date: 2016-03-30 10:48:51
tags: [积累,技术]
---

为了保证安全性，mysql默认都是只允许localhost访问的，但是实际操作中，会需要远程访问。可以用下面的命令：

## 授权法
``` bash
mysql> GRANT ALL PRIVILEGES ON dbname.* TO 'myuser'@'%' IDENTIFIED BY 'mypassword' WITH GRANT OPTION;
flush PRIVILEGES;
```
表示将dbname数据库的所有表的权限 授权给 任意ip 以myuser用户名和mypassword密码登录。然后立即刷新，使授权生效。

还有另一种方法是改表法，命令比较多，实际用起来不如授权法方便，这里就不写了～

## 参考资料
1、[解决MySQL不允许从远程访问的方法](http://www.xshell.net/database/1206.html)
