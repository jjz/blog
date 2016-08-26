#Android中使用FindBugs 在Android开发中，会出现一些比较不容易发现的bug，比如对于null的判断，出现遗漏的时候会出现'NullPointException',比如下面的代码：

```
 if (ta != null) {
                mPanelHeight = ta.getDimensionPixelSize(R.styleable.SlidingUpPanelLayout_umanoPanelHeight, -1);
                mShadowHeight = ta.getDimensionPixelSize(R.styleable.SlidingUpPanelLayout_umanoShadowHeight, -1);
                mParallaxOffset = ta.getDimensionPixelSize(R.styleable.SlidingUpPanelLayout_umanoParalaxOffset, -1);

            .......
  }

  ta.recycle();
```

开头的时候判断ta不为null的情况,但是在调用`ta.recycle()`的时候是在if之外，但调用这个方法的时候，传入的参数ta为null的时候就会出现`NullPointException`,如果能够通过工具找到这种潜在的bug就最好不过了。

##FindBugs`FindBugs`是一个Java静态分析工具，用来检查类或者jar文件，用来发现可能的问题。检测完成之后会生成一份详细的报告，借助这份报告可以找到潜在的bug，比如`NullPointException`,特定的资源没有关闭，查询数据库没有调用`Cursor.close()`等，如果才用人工的方式很难才能发现类似的bug，或者这些bug永远不会发现，直到运行时才发现，还有可能是一直没有出现，别人调用的时候没有做检查就调用了..... Java的静态分析工具当然可以无难度的在Android上面运行，通过这种FindBugs的检查可以让App的运行更加的稳定。 FindBugs官网地址：http://findbugs.sourceforge.net/ ##Gradle的FindBugs插件

FindBugs在Gradle中是当做一个插件存在的，可以在Android Studio中直接使用：

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

首先引入`FindBugs`的插件。 定义一个`task`任务，这个任务的类型是`FindBugs`，依赖`assembleDebug`母的是先生成.classe文件。`ignoreFailures`：有警告错误的时候也是允许构建。`reportLevel`:报告的级别，`Low`,`Medium`,`High`一般来说我们首先关注的是高级别的报告，再关注低一级别的报告。`classes`和`source`分别是对应的.classe文件夹地址，和源代码文件地址。`repoets`指定报告类型，有两种方式`xml`和`html`，只允许一种输出格式。 在右侧的Gradle的对于的Module可以在Tasks中找到对应的findBugs任务，点击即可运行。

##报告 我们会得到对应的一个类似与这样的报告:

![报告](http://upload-images.jianshu.io/upload_images/22188-8e2f5375d8fdfc54.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

问题发现的例子: 1.NP:`Possible null pointer dereference`,可能出现null的代码。 2.HE：重写对戏那个的equals(）方法，但是没有重写它的hashCode方法。或者相反。 3.SE：serializable错误

更多的内容解读可以点击详情，可以看到错误对应的代码行号，和错误详情，以及相关检测错误的解释。

##运行时机 什么时候运行是一个问题，一般情况下在原有的项目中加入findBugs之后，会检测出以前的代码存在的一些问题，所以在使用findBugs的时候应该做一次全面的检查，解决到对于的问题。

其他运行的时机，我觉得是，在完成一个版本对应功能的时候要运行检查一次，防止写的代码有潜在的bug，另一个时间点就是在每次修复完Bug之后，在运行一次，防止修复Bug的时候，引入新的Bug。
