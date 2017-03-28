#Atom配置go开发环境
一直没有找到一个比较好用的golang的IDE，虽然IntelliJ还不错，但是收费版的实在是太贵了，免费版的又是各种功能缺失，今天尝试了一下在Atom上面配置Golang的开发环境，发现效果很不错，记录下配置环境的步骤。
首先要先安装下Atom,Atom是github打造的文本编辑器:https://atom.io/,开源，美观，有各种强大的插件，可以直接下载安装。
安装Go，Mac上面的安装：https://segmentfault.com/a/1190000003933557

## go-plus插件
go-plus是Atom上面的一款开源的go语言开发环境的的插件，项目地址:https://github.com/joefitzgerald/go-plus
他需要依赖下面的go语言工具:
	1.autocomplete-go ：gocode的代码自动提示
	2.gofmt ：使用goftm,goimports,goturns
	3.builder-go:go-install 和go-test,验证代码，给出建议
	4.gometalinet-linter:goline,vet,gotype的检查
	5.navigator-godef:godef
	6.tester-goo :go test
	7.gorename :rename
	
## 安装go-plus
在Atom中的`Preference`中可以找到install菜单,输入`go-plus`:
![图片描述][1]

点击:install，就会开始安装`go-plus`，`go-plus`插件会自动安装对应的依赖插件，如果没有安装对应的go的类库会自动运行:`go get`安装。

## 快捷键设置
每个人对于自己熟悉的快捷键都不太一样，Atom以及其插件对于的快捷键并不是我习惯的快捷键，因此需要对快捷键进行修改，打开`Preference`,选中`Keybindings`：
可以找到你需要的快捷键的命令点击左边的copy按钮，可以copy对应的keymap配置，然后点击`your keymap file`可以看到`keymap.cson`：
```
'atom-text-editor[data-grammar~="go"]:not([mini])':
  'alt-r': 'golang:gorename'
```
可以修改为自己熟悉的快捷键:
```
'atom-text-editor[data-grammar~="go"]:not([mini])':
  'cmd-r': 'golang:gorename'
```
keymap.cson就是自己的atom的快捷键配置文件

## 命令行
Atom没有编译工具,可以在命令中直接运行go程序，需要安装`atom-terminal-panel`，在install中直接输入`atom-terminal-panel`安装。

直接使用快捷键**control+\`**就可以呼出terminal，个人感觉-习惯了`fish shell`还是用不惯atom里面的termianl。

评论里面有人建议使用`Terminal Plus`,今天试用了下，非常满意，对于fish的支持很好，快捷键是`cmd+shift+t`


  [1]: /img/bVuQLe


