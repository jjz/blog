# Homebrew 安装 openssl
Mac系统自带Openssl,但是我想通过Homebrew 安装Openssl,因为系统级的保护无法删除系统自带的openssl，我们只能利用环境变量和link让两个openssl共存。
##系统自带的openssl
先看系统自带的openssl
> whereis openssl

```
/usr/bin/openssl
```
系统自带的openssl安装在/usr/bin目录下面。
再看下openssl版本：
>openssl
>version
```
OpenSSL 0.9.8zg 14 July 2015
```
系统自带的OpeenSSl版本比较低

##通过Homebrew安装openssl
用brew install 命令安装最新版本的openssl:
>brew install openssl 

```
==> Summary
🍺  /usr/local/Cellar/openssl/1.0.2e_1: 465 files, 11.9M
```
最新版本的openssl为`1.0.2e_1`.
在查看当前系统的openssl
>openssl
>version

```
OpenSSL 0.9.8zg 14 July 2015
```

##link 
尝试link openssl
>brew link openssl
```
Warning: openssl is keg-only and must be linked with --force
Note that doing so can interfere with building software.
```
link 失败加上 --force在尝试一次
>brew link openssl --force
```
inking /usr/local/Cellar/openssl/1.0.2e_1... 1556 symlinks created
```
link成功！！
##设置$PATH
link成功openssl如果还不是brew安装最新的版本的话，就是你的$PATH变量设置的不对 ,/usr/local/bin必须在最前面。
查看当前的shell
>echo $SHELL
```
/usr/local/bin/fish
```
我使用的是fish 
进入fish的配置文件`~/.config/fish/config.fish`
设置命令为：
>et -gx PATH  /usr/local/bin   $PATH

bash的设置文件为:~/.bash_profile
命令为：
export PATH=/usr/local/bin:$PATH

再次查看openssl版本
```
OpenSSL 1.0.2e 3 Dec 2015
```
设置成功！！！


