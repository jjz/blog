以前写过一篇[Ubuntu上安装Bitcoin Core](http://www.jianshu.com/p/3767b20856c6),当前情况下比特币扩容之争还在继续，今天决定把Ubuntu上面的`Bitcoin Core`换成`Bitcoin Unlimited`。
##安装前准备
安装前需要备份好数据

* 备份好你们的钱包，备份好你的私钥
	
	bitcoin-cli backwallet /tmp/backp.dat
	
* 备份好`~/.bitcon`目录，就是存在区块数据的目录,如果区块数据丢失或者损坏，需要重建索引，或者重新下载。

停止运行`bitcoind`：
>bitcoin-cli stop



##移除Bitcoin Core
如果你之前安转了Core,或者Classic，就需要移除他们。
删除客户端:
>sudo apt-get remove bitcoin*

如果使用了`ppa`安装的还需要移除对应的bitcoin的`source`,ppa源的保存文件在`/etc/apt/sources.list.d`目录下面，名称为:`bitcoin-bitcoin-trusty.list`,删除源命令：
>sudo rm /etc/apt/source.list.d/bitcoin-*.*

##PPA安装Bitcoin Unlimited
使用PPA安装Bitcoin Unlimited是比较简单，先添加Bitcoin Unlimited的源，然后直接安装即可：
>sudo add-apt-repository ppa:bitcoin-unlimited/bu-ppa-nightly
>sudo apt-get update
>sudo apt-get install bitcoind

安装成功之后,使用和Bitcoin Core相同的命令，就可以启动bitcoind:
>bitcoind --daemon

使用`bitcoin-cli`可以查看运行情况:
>bitcoin-cli getinfo

上述内容我写了一个脚本，地址[https://github.com/jjz/script/blob/master/install_bu.sh](https://github.com/jjz/script/blob/master/install_bu.sh)








