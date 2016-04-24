#Ubuntu安装MariaDB
MariaDB是MySQL的一个分支，主要是用来替换MySQL的，采用GPL授权许可,开源,完全兼容MySQL,包括API和命令行。由MySQL的创始人`Michael Widenius`主导开发。为什么要开发MariaDB？因为MySQL早就卖给了SUN，而SUN被甲骨文收购了，也就是说MySQL的所有权在Oracle手里。
Maria是`Michael Widenius`女儿的名字。
MariaDB的包并没有在Ubuntu仓库包中，下面对安装MariaDB做个记录，方便下次查看。

##设置仓库
首先安装`add-apt-repository`的命令，已经安装的可以忽略:
>sudo apt-get install software-properties-common

获取key:
>sudo apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xcbcb082a1bb943db

添加MariaDB的`repository`:
>sudo add-apt-repository 'deb http://sfo1.mirrors.digitalocean.com/mariadb/repo/10.0/ubuntu trusty main'

更新本地的仓库:
>sudo apt-get update

##安装MariaDB
>sudo apt-get install mariadb-server
安装的时候会要求输入**root**密码:

![图片描述][1]
从命令行中进入MariaDB:
>mysql -uroot -p
![图片描述][2]

启动mysql服务:
>sudo /etc/init.d/mysql start
```
 * Starting MariaDB database server mysqld
   ...done.
 * Checking for corrupt, not cleanly closed and upgrade needing tables.
```
停止mysql服务:
>sudo /etc/init.d/mysql stop

```
* Starting MariaDB database server mysqld
   ...done.
 * Checking for corrupt, not cleanly closed and upgrade needing tables.
```






参考： [http://www.linuxtechi.com/install-mariadb-in-ubuntu/](http://www.linuxtechi.com/install-mariadb-in-ubuntu/)


  [1]: /img/bVuXsT
  [2]: /img/bVuXsU

