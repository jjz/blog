以前写过一篇[Ubuntu上安装Bitcoin Core](http://www.jianshu.com/p/3767b20856c6)的文章,当前比特币扩容之争还在继续，今天决定把Ubuntu上面的`Bitcoin Core`换成`Bitcoin Unlimited`。下面对安装`Bitcoin Unlimited`的过程做个总结。
##安装前准备
安装前需要先备份好数据。

* 备份好你们的钱包，备份好你的私钥
	
	 >bitcoin-cli backupwallet /tmp/backp.dat
	
* 备份好`~/.bitcon`目录的数据，这里存放的是全部的区块数据，如果区块数据丢失或者损坏，需要重建索引，或者需要重新下载。

* 停止运行`bitcoind`：
>bitcoin-cli stop

备份好数据以及停止了`bitcoind`,下一步需要移除`Bitcoin Core`。
##移除Bitcoin Core
之前有安装Core或者Classic的，需要先移除他们，`Bitcoin Unlimited`也是使用的`bitcoind`，不移除就无法正确安装。
删除客户端:
>sudo apt-get remove bitcoin*

如果使用了的是`PPA`安装的还需要移除对应的bitcoin的`source`,`PPA`源的保存文件在`/etc/apt/sources.list.d`目录下，名称为:`bitcoin-bitcoin-trusty.list`,删除源命令：
>sudo rm /etc/apt/source.list.d/bitcoin-*.*

如果你是使用的源代码安装的`Bitcoin Core`,就需要使用`make`命令卸载：
> cd /path/where/the/code/is/stored
> sudo make uninstall

移除`Bitcoin Core`之后就可以安装`Bitcoin Unlimited`了。
##PPA安装Bitcoin Unlimited
使用`PPA`安装`Bitcoin Unlimited`也是比较简单的，先添加Bitcoin Unlimited的源，然后直接安装即可：
>sudo add-apt-repository ppa:bitcoin-unlimited/bu-ppa-nightly
>sudo apt-get update
>sudo apt-get install bitcoind

安装成功之后,使用和`Bitcoin Core`相同的命令就可以启动`bitcoind`了:
>bitcoind --daemon

使用`bitcoin-cli`可以查看运行情况:
>bitcoin-cli getinfo

上述安装过程我写了一个脚本，地址[https://github.com/jjz/script/blob/master/install_bu.sh](https://github.com/jjz/script/blob/master/install_bu.sh)

