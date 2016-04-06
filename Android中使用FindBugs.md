#Android中使用FindBugs
在java开发中，会出现一些比较不容发现的bug，比如对于null的判断，出现遗漏的时候会出现'NullPointException',比如下面的代码：

```
 if (ta != null) {
                mPanelHeight = ta.getDimensionPixelSize(R.styleable.SlidingUpPanelLayout_umanoPanelHeight, -1);
                mShadowHeight = ta.getDimensionPixelSize(R.styleable.SlidingUpPanelLayout_umanoShadowHeight, -1);
                mParallaxOffset = ta.getDimensionPixelSize(R.styleable.SlidingUpPanelLayout_umanoParalaxOffset, -1);

            .......
            }

            ta.recycle();
```
开头的时候判断ta不为null的情况,但是在调用` ta.recycle()`的时候是在if之外，但调用这个方法的时候，传入的参数ta为null的时候就会出现`NullPointException`,如果能够通过工具找到这种潜在的bug就最好不过了。

##FindBugs
`FindBugs`是一个Java静态分析工具，用来检查类或者jar文件，用来发现可能的问题。检测完成之后会生成一份详细的报告，借助这份报告可以找到潜在的bug，比如`NullPointException`,特定的资源没有关闭，查询数据库没有调用`Cursor.close()`等，如果才用人工的方式很难才能发现，或者永远不会发现，知道运行时才发现。
通过这种检查可以让App的运行更加的稳定。
官网地址：http://findbugs.sourceforge.net/
##Gradle的FindBugs插件

FindBugs作为Gradle的一个插件存在，可以在Android Studio中直接使用：
```
apply plugin: "findbugs"

task findbugs(type: FindBugs,dependsOn:'assembleDebug') {

    ignoreFailures= true
    effort= "default"
    reportLevel= "high"
    println( "$project.buildDir")
    classes = files("$project.buildDir/intermediates/classes")
    source= fileTree("src/main/java/")
    classpath= files()
    reports{
        xml.enabled=false
        html.enabled=true
        xml {
            destination "$project.buildDir/findbugs.xml"
        }
        html{
            destination "$project.buildDir/findbugs.html"
        }
    }
}
```
首先引入`FindBugs`的插件。
定义一个`task`任务，这个任务的类型是`FindBugs`，依赖`assembleDebug`先生成.classe文件。
`ignoreFailures`：有警告的时候也是允许构建。
`reportLevel`:报告的级别，`Low`,`Medium`,`High`一般来说我们首先关注的是高级别的报告，再关注低一级别的报告。
`classes`和`source`分别是对应的.classe文件夹地址，和源代码文件地址。
`repoets`指定报告类型，`xml`和`html`值支持一种输出格式。
在右侧的Gradle的对于的Module可以在Tasks中找到对应的findBugs任务，点击即可运行。

##报告
我们会得到对应的一个类似与这样的报告:


问题发现的例子:
	1.NP:`Possible null pointer dereference `,可能出现null的代码。
	2.HE：重写对戏那个的equals(）方法，但是没有重写它的hashCode方法。或者相反。
	3.SE：serializable错误
	
更多的内容解读可以点击打开可以看到对应的行数，相关检测错误的解释。

##运行时机
什么时候运行是一个问题，一般情况下在原有的项目中加入findBugs之后，会检测出以前的代码存在的一些问题，所以在使用findBugs的时候应该做一次全面的检查。

其他运行的时机，我觉得是，在完成一个版本对应功能的时候要运行检查一次，防止写的代码有潜在的bug，另一个时间点就是在每次修复完Bug之后，在运行一次，防止修复Bug的时候，引入新的Bug。

