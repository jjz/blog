在开发Android的过程中，会出现一些比较不容易发现的`Bug`，比如没有对`null`做判断，会出现'NullPointException'的崩溃,下面的代码就会出现崩溃：

```
 if (ta != null) {
                mPanelHeight = ta.getDimensionPixelSize(R.styleable.SlidingUpPanelLayout_umanoPanelHeight, -1);
                mShadowHeight = ta.getDimensionPixelSize(R.styleable.SlidingUpPanelLayout_umanoShadowHeight, -1);
                mParallaxOffset = ta.getDimensionPixelSize(R.styleable.SlidingUpPanelLayout_umanoParalaxOffset, -1);

            .......
  }

  ta.recycle();
```
开头的时候判断ta不为`null`,但是在调用`ta.recycle()`的时候是在if之后，在使用的时候，如果传入的参数ta为`null`的话就会出现`NullPointException`的Bug,当然好的代码编写习惯，以及进行`code review`,还有充分的测试都可以避免这种Bug的出现。如果换一种思路能够通过工具检查出这种潜在的`Bug`就最好不过的。还好有一种工具可以解决这个问题:`FindBugs`。

## FindBugs
`FindBugs`是一个Java静态分析工具，用来检查类或者jar文件，查找代码可能存在的问题。FindBugs官网地址：[http://findbugs.sourceforge.net/](http://findbugs.sourceforge.net/)。
检测完成之后会生成一份详细的报告，借助这份报告可以找到潜在的`Bug`，比如前面说到的`NullPointException`,还可以检查`特定的资源没有关闭`,例如：查询数据库没有调用`Cursor.close()`等。
如果采用人工的方式很难发现类似的bug，或者有一些`Bug`不会发现，直到运行时才出现，还有可能是一直没有出现，别人调用的时候没有做检查就直接使用了.....
`FindBugs`可以自动化化的分析代码，帮助开发者提高代码质量，当然它可以无难度的在`Android`上面运行，通过`FindBugs`的检查可以让App的运行更加的稳定。

## Gradle插件

`FindBugs`在*Gradle*中做一个插件存在的，可以在Android Studio中直接使用：
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
代码解释：
*  引入`FindBugs`的插件：`apply plugin: "findbugs"
`。
*  定义一个`task`任务，这个任务的类型是`FindBugs`，指定依赖`assembleDebug`是为了先生成.classe文件，才能对代码进行静态分析。
* `ignoreFailures`：有警告错误的时候也是允许构建。
* `reportLevel`:报告的级别，`Low`,`Medium`,`High`一般来说我们首先关注的是高级别的报告，再关注低一级别的报告。
* `classes`和`source`分别是对应的.classe文件夹地址，和源代码文件地址。
* `repoets`指定报告类型，有两种方式`xml`和`html`，只允许一种输出格式。

定义完成之后，同步下`Gradle`，之后在右侧的`Gradle`的菜单中找到对于的`Module`,就可以在Tasks中找到对应的**findBugs**任务，点击即可运行。

## FindBugs报告
运行完成之后，会得到对应的一个类似下图的报告:

![报告](https://user-gold-cdn.xitu.io/2017/4/5/0e3de10ded232264ea169796604a9282)
更多的内容解读可以点击详情，看到错误对应的代码行号，和错误详情，以及相关检测错误的解释。
常见的`FindBugs`的警告:
* NP:`Possible null pointer dereference `,可能出现null的代码。
* HE：重写对戏那个的equals(）方法，但是没有重写它的hashCode方法，或者相反的情况。
* SE：serializable错误
* ...其他常见错误可以参考文档


## 什么时候运行？
什么时候运行是一个问题，一般情况下在原有的项目中加入`FindBugs`之后，可以检测出一些以前的代码存在的问题，所以在刚刚使用`FindBugs`的时候应该做一次全面的检查，解决掉对应的问题。
之后的运行，一般可以在完成一个版本对应功能开发完成之后可以检查一次，防止新修改的代码有潜在的bug，另一个时间点就是在每次修复完`Bug`之后，再运行一次，防止修复`Bug`的时候，造成了新的`Bug`。

