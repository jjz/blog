# 清理不的Android项目资源

`Andorid Lint`是*SDK Tools 16(ADT 16)*之后提供的一个工具，可以对于Android项目的源代码进行扫描和检查，发现潜在的问题，其中的一个功能`UnuserdResources`用来检查不用的资源文件。根据`UnserdResources`生成的内容，可以清理掉不再使用的项目文件。

## 运行Lint 在Android Studio中的右边的菜单栏中可以找**Gradle**的工具拦，展看`verification`,可以到lint的选项：

![lint](http://upload-images.jianshu.io/upload_images/22188-625560dac3b1f5d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击运行之后会生成两个结果文件:* `lint-results.xml`* `lint-results.html`

这两个文件所在的目录为：**/Users/.../android-crop/crop/build/outputs** 也可以使用命令行: > lint --check "UnusedResources" ./ > result.txt

会导出一个tex的文件，如果想导出*.html*的结果文件可以使用命令: >lint --check "UnusedResources" ./ --html result.html

这样就会导出一个html格式的文档。 查看未使用的资源:

![UnusedResources](http://upload-images.jianshu.io/upload_images/22188-bdbab2eee85c8697.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## android-resource-remover`android-resource-remover`是一个开源的python库，可以根据`Android Lint`生成的结果，从项目中删除掉不用的资源。

安装环境要求:

-	Python >=2.7
-	ADT >= 16

通过pip安装`android-resource=remover`: >pip install android-resource-remover

找到使用Lint生成的`lint-results.xml`,运行命令: >android-resource-remover --xml build/outputs/lint-results.xml

就可以直接清除不用的Android资源。
