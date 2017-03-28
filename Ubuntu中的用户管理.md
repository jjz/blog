#Ubuntu中的用户管理
在使用Linux系统的时候，要坚持`最小权限原则`.`最小权限原则(least priviledge)`是指Linux通常希望用户或者进程只拥有足够完成其工作的权限，而系统不赋予其更多的特权。
最高权限的用户通常是`root`用户，`root`用户想做什么都可以（代表着最大权限）。如果都使用每个进程都用`root`权限，这对于系统来说是一个巨大的安全漏洞，因此不能使用`root`用户部署服务，降低部署进程的权限。使用一个特定用户用来做部署特定的服务是一种比较常用的方式。对于该用户要收缩其所享有的特权，以防权限的滥用。因此在Ubuntu中需要管理好相关用户。
## 新建用户
使用命令`useradd`可以新建一个用户:
>sudo useradd jjz

adduser会自动创建`用户目录`和`shell`,并且自动创建`分组`。
使用passwd命令可以为用户设置密码：
>sudo passwd jjz

	sudo: unable to resolve host
	Enter new UNIX password:
	Retype new UNIX password:
	passwd: password updated successfully
	
这样就可以给用户**jjz**设置一个密码了。
使用`id`命令可以查看用户的信息:
>sudo id jjz

	uid=1000(jjz) gid=100(users) groups=100(users)

使用finger命令可以查看用户的目录，shell和输入的地址等详细信息:
>sudo finger jjz

	Login: jjz            			Name:
	Directory: /home/jjz                	Shell: /bin/sh
	Never logged in.
	No mail.
	No Plan.
## 修改和删除用户
命令usermod用来修改用户信息，修改用户的登录名:
>usermod -l jjz jjz1

也可以用来把用户加入到分组中例如:
>usermod -g users jjz

还可以用来修改用户的用户目录:
>usermod -d /user/jjz jjz

如果想删除用户可以使用命名userdel：
>userdel jjz

删除用户的时候，同时删除用户的工作目录可以使用命令：
>userdel -r jjz

## 用户与root用户的切换
使用非root用户登录之后，有时需要执行一些具有root权限的操作，比如安装系统级别的软件，修改系统文件等，经常需要用sudo权限，这个时候我们也可以切换到root用户进行操作,切换`root`用户可以使用命令：
>sudo su

切换到root用户需要验证当前用户密码。
使用完root用户之后，可以使用命令切换到登录其他用户:
>su jjz

或者直接使用`exit`即可退出root用户，回到登录用户。
## 给用户赋予执行sudo权限
当新用户执行`sudo`的时候会提示`xxx is not in the sudoers file. This incident will be reported.`,也就是说当前用户是没有执行sudo权限的。如果我们想让该用户拥有执行sudo的权限，需要给用户授权，`sudo`权限的授权需要修改文件`/etc/sudoers`。
首先进入root用户模式:
>su

添加文件的写权限:

>chmod u+w /etc/sudoers

编辑`/etc/sudoers`:
>vim /etc/sudoers

找到`root ALL=(ALL)ALL`
在它的下面添加：
	
	jjz ALL=(ALL)ALL

这样`jjz`即可执行`sudo`。
如果要撤销文件的写权限可以使用命令:
>chmod u-w /etc/sudoers


## 禁用和启用root用户
为了系统的充分执行最小授权原则，我们也可以禁止root用户登录，禁用root用户的命令:
>sudo passwd -l root

这样就禁用了root用户登录，但是root的密码还保存着。
再次启动root登录，执行命令:
>sudo passwd -u root


