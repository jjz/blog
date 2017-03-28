频繁登录Linux服务器时，使用`ssh <username>@<host>`的方式登录，但是每次都需要输入密码是件很麻烦的事。我们还可以使用私钥/公钥对的方式在免密码登录服务器。
首先需要在远程服务器中安装`ssh-server`服务，才可以使用ssh登录。如果没有的话可以使用命令直接安装`ssh-server`,可以以Ubuntu为例安转`ssh-server`:
>sudo apt-get install openssh-server


## 生成私钥/公钥对
使用命令`ssh-kengen`可以生成私钥/公钥对，私钥/公钥对的生成算法有两种RSA和DSA。
>RSA是非对称加密算法，可以用来加密和签名
>DSA(Digital Signature Algorithm)只可以用来数字签名的算法

这里使用*RSA*算法生成私钥/公钥对。需要确认`.ssh`目录是否存在，如果不存在的话创建该目录:
>mkdir ~/.ssh
>chmod 700 ~/.ssh

然后生成私钥/公钥对:
>ssh-keygen  -b 1024 -t rsa

     Generating public/private rsa key pair.
     Enter file in which to save the key (/Users/user/.ssh/id_rsa):
     Enter passphrase (empty for no passphrase):
     Enter same passphrase again:
     Your identification has been saved in /Users/user/.ssh/id_rsa.
     Your public key has been saved in /Users/user/.ssh/id_rsa.pub.
     The key fingerprint is:
     SHA256:gh0yqSZhpP9ERlhFSKwgy3sTBZwPAT0InLBJ73zcNt8                         user@user-ios.local
     The key's randomart image is:
     +---[RSA 1024]----+
     |*+*BB+o          |
     |**oBoo           |
     |B+ o@ .          |
     |o++=.*..         |
     |. =o+oo+S        |
     | + =. ..o .      |
     |  . o    . E     |
     |                 |
     |                 |
     +----[SHA256]-----+
其中`-b`的参数是用来设置私钥的长度1024的长度已经可以满足我们对于安全的需求了,不输入任何文件名会在`.ssh`目录下得到两个文件:`id_rsa`和`id_rsa.pub`。

## 上传公钥到对应的服务器
使用命令`ssh-copy-id`可以将认证文件加载到对应的服务器上：
>ssh-copy-id -i ~/.ssh/id_rsa.pub <username>@<host>

     /usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "id_rsa.pub"
     /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
     /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
     user@host's password:
     Number of key(s) added:        1
     Now try logging into the machine, with:   "ssh 'user@host'"
     and check to make sure that only the key(s) you wanted were added.

这里会要求我们输入远程服务器的密码。

## 修改服务器的ssh配置文件
`ssh-server`配置文件位于:`/etc/ssh/sshd_config`中,需要设置`ssh-server`允许使用私钥/公钥对的方式登录，打开配置文件:
>vim /etc/ssh/sshd_config

增加设置:

	RSAAuthentication yes
	PubkeyAuthentication yes
	# AuthorizedKeysFile     %h/.ssh/authorized_keys
	
然后重启ssh-server：
>sudo /etc/init.d/ssh restart

设置完成之后就可以使用命令`ssh <username>@<host>`直接登录服务器了，不需要再输入密码了。

## 其他

1.  如果公钥丢失的情况，可以使用私钥再次生成公钥,使用私钥生成公钥的命令：

>ssh-keygen -y -f ~/.ssh/id_rsa > ~/.ssh/id_rsa.pub

2.  使用`ssh-copy-id`上传公钥到服务器之后，公钥是存放在服务器的`~/.ssh/authorized_keys`中。它的存在格式是一行一个公钥，也可以手动把公钥的内容直接复制到服务器的authorized_keys中。使用命令`cat id_rsa.pub`可以获取到公钥的内容。

