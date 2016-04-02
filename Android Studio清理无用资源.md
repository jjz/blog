#Android Studio清理无用资源

Andorid Lint是SDK Tools 16(ADT 16)之后提供的一个工具，可以对于Android项目的源代码进行扫描和检查，发现潜在的问题，其中的一个功能是`UnuserdResources`用来清理不用的资源文件。

##运行lint
在Android Studio中的右边的菜单栏中可以找Gradle的工具拦，展看`verification`,可以到lint选项：

运行之后会生成:
`lint-results.xml`和`lint-results.html`文件。
也可以使用命令行:
> lint --check "UnusedResources" ./  > result.txt
会导出一个tex的文件，如果想使用html的结果文件可以使用:
>lint --check "UnusedResources" ./  --html result.html
会导出一个html格式的文档
查看为使用资源的结果:



##android-resource-remover
`android-resource-remover`是一个开源的python库，可以根据`Android Lint`的结果，从项目中删除掉不再使用的资源。
环境要求:
	Python >=2.7
	ADT >= 16
通过pip可以安装`android-resource=remover`:
>pip install android-resource-remover

找到使用Lint生成的`lint-results.xml`,运行命令:
>android-resource-remover --xml build/outputs/lint-results.xml
可以直接清除不用使用的android资源

