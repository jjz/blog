# Mac OS制作Ubuntu安装U盘

采用U盘安装Ubuntu系统是目前比较常见的安装方式之一，在Windows上有制作安装U盘的工具（比如`Universal USB Installer`），那么在`Mac OS`上面如何制作安装U盘呢?
答案是`命令行`!
## hdiutil
第一步,需要到[Ubuntu](http://www.ubuntu.com/download)下载需要的Ubuntu的安装文件。
然后就需要使用第一个命令`hdiutil`。
`hdituil`:是一个Mac OS上面处理镜像文件的[命令](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man1/hdiutil.1.html),可以对镜像文件进行`制作，验证和转换`等...
我们知道`DMG`格式是Mac OS上常用的打包格式文件，需要把下载的Ubuntu安装文件（.iso）转换成(.dmg)格式的文件,方便在Mac OS上面进行操作，转换命令:
>cd Downloads/
>hdiutil convert -format UDRW -o ubuntu.iso  ubuntu-14.04.5-desktop-amd64.iso

`-format`为生成文件的权限,`UDRW `:表示转换成有`read/write`的权限的镜像。
等待转换完成即可~

## diskutil
第二步需要需要对U盘进行操作，而`diskutil`就是用来对Mac OS的磁盘操作的命令。
`diskutil`:操作本地磁盘，可以对磁盘进行`卸载，挂载`等操作。
列出当前挂载的磁盘:
>diskutil list
```
dev/disk0 (internal, physical):
   # :                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *251.0 GB   disk0
   1:                        EFI EFI                     209.7 MB   disk0s1
   2:          Apple_CoreStorage Macintosh HD            250.1 GB   disk0s2
   3:                 Apple_Boot Recovery HD             650.0 MB   disk0s3
/dev/disk1 (internal, virtual):
   # :                       TYPE NAME                    SIZE       IDENTIFIER
   0:                  Apple_HFS Macintosh HD           +249.8 GB   disk1
                                 Logical Volume on disk0s2
                                 45CD1187-14DE-4203-9895-FBB1B3770F1E
                                 Unencrypted
/dev/disk2 (external, physical):
   # :                       TYPE NAME                    SIZE       IDENTIFIER
   0:     Apple_partition_scheme                        *8.1 GB     disk2
   1:        Apple_partition_map                         4.1 KB     disk2s1
   2:                  Apple_HFS                         2.4 MB     disk2s2
```

其中`/dev/disk2`就是U盘。
需要先卸载掉U盘，然后在把安装文件写入到U盘中，这样就需要用到卸载命令：
>diskutil unmountDisk /dev/disk2

再次使用`diskutil list`命令就不会显示出*disk2*了。

## dd

第三步，把安装文件写入U盘，这里需要使用命令`dd`
`dd`:是`Unix`和`类Unix系统`上的命令，作用就是用指定大小的块拷贝一个文件，并在拷贝的同时进行指定的转换。

在进行拷贝之前，还需要做的一件时间,因为使用`hdiutil`转换的文件后缀名为`.dmg`,所以需要把文件重命名为`.iso`，在安装的时候系统才能够更好的识别。

>mv ubuntu.iso.dmg ubuntu.iso

然后把安装文件拷贝到U盘中

>sudo dd if=./ubuntu.iso of=/dev/rdisk2 bs=1m
这行命令必须使用`root`权限，
* `if`:输入的文件名
* `of`:输出的文件名
* `bs`:是块大小，这里使用`1m`的块大小。
漫长的等待....
```
1052+1 records in
1052+1 records out
1104052224 bytes transferred in 249.471583 secs (4425563 bytes/sec)
```

操作完成之后，安全地拔出U盘
>sudo eject /dev/rdisk2

可以使用U盘进行Ubuntu的安装了！
## 销毁安装数据
安装完成之后，U盘上面的安装文件还在，这样会影响我们正常使用U盘。可以把U盘格式化一次，清除数据，也可以使用`dd`命令销毁磁盘数据:
>sudo dd if=/dev/urandom of=/dev/rdisk2

使用随机数填充U盘，可以用来销毁数据，一般用于重要数据否则没有必要使用随机数填充。


