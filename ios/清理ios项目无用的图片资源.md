# 清理ios项目无用的图片资源 
项目经过需求的变更，产品迭代，再多次的改版之后，有些不再使用的图片不一定能够及时的清理掉，这些无用的图片一方面让项目图片资源的结构更加的复杂，另一方面会导致`ipa`包的体积变大。 
及时的清理掉不用的图片资源很有必要，在Android项目中使用Lint可以轻松的完成这个任务，而在ios上没有找到比较好的清理无用图片的工具。
参考网上的例子，写了一个python清除ios项目中无用的图片资源的脚本。首先介绍下ios中使用图像资源的方式。
## Images Assets
ios中使用 `Images Assets`管理图片，项目的图片资源一般都放在这`Image Assets`里面，图片会放到`*.imageset`目录里，对应的有`1x`,`2x`和`3x`的图,还有一个`Contents.json`的描述文件。 清除图片资源需要把`*.imageset`清除掉,包括图片文件和json文件。 想要清除掉不用的图片资源，就需要知道图片资源怎么被引用的，再查找到未被引用的图片资源就可以清除掉了。 
在`ios代码`中调用图片的方法一般是:
>[UIImage imageNamed:@"image"]

在`xib`中调用图片的方法是用xml的方式描述,图片资源作为image的名称:
```
 <imageView  image="image" id="Rb9-sl-eQM">                
 </imageView>
```
因此清理的思路就是搜索项目中的图片文件，如果找到对于的`"name"`在`xib`或者`ios代码`中存在，说明该图片被使用了，不然的话该图片即可被清理掉。
先看看如何方便的搜索图片文件有没有被使用吧！

# 搜索文件 
搜索文件的方式可以使用`grep`,`ack`都是不错的工具，但是有一种更快，更好的搜索文件内容的方式:`The Silver Searcher`，`The Silver Searcher`使用起来更方便，更快，更简单,项目地址：[https://github.com/ggreer/the_silver_searcher](https://github.com/ggreer/the_silver_searcher)。 直接安装`The Silver Searcher`的命令:
>brew install the_silver_searcher

使用`ag`命令就可以进行文本搜索:
>ag "image" './'

这个命令的意思是搜索到该目录下以及其子目录下的所有含有`"image"`的文件。
使用这个命令就需要在python中执行bash命令。

# bash命令 
使用`bash`命令来进行一些搜索文本，删除文件的工作，在python中可以直接运行`bash`命令。 在python中直接使用`bash`命令:
>os.popen('echo $PATH')

在执行`bash`命令的时候，可以通过`os.popen('echo $PATH').read()`执行并读取执行的结果。 也可以直接执行`bash`命令不用关系执行的结果,例如: 
>os.system('rm -rf dir')

需要用到的`bash`命令有`ag "image" './'`和`rm -rf './'`,前者用来搜索文本有没有被使用，后者用来删除文件夹。
下面就看下完整的实现。
## 清除无用的`imageset`资源
完整的清理无用的图片资源的代码:
```
path = 'ios'
images = glob.glob('ios/images.xcassets/*/*.imageset')
def find_un_used():
    img_names = [os.path.basename(pic)[:-9] for pic in images]
    unused_imgs = []
    for i in range(0, len(images)):
        pic_name = img_names[i]
        command = 'ag "%s" %s' % (pic_name, path)
        result = os.popen(command).read()
        if result == '':
            unused_imgs.append(images[i])
            print 'remove %s' % (images[i])
            os.system('rm -rf %s' % (images[i]))


    text_path = 'unused.txt'
    tex = '\n'.join(sorted(unused_imgs))
    os.system('echo "%s" > %s' % (tex, text_path))
    print 'unuse res:%d' % (len(unused_imgs))
    print 'Done!'

```

这段代码是从当前文件夹里，遍历所有的`.imageset`文件，找到图片使用的图片名称，在文件夹中搜索该**名称**的文件有没有被使用。如果没有被使用的名称就可以清除掉该资源。
执行这段之后发现有一些不应该清除图片资源也被清除了，这里有个例外的情况没有考虑到。
# ignores
有时候会给一些资源文件动态的赋值，使用这样的代码：
```
for (int i = 1; i <= 10; ++i) {
    NSString *imageName = [NSString stringWithFormat:@"image_%d", i];
    UIImage *image = [UIImage imageNamed:imageName];
    ......
}
```

上面的代码中查找 *image1*时，发现这个图片并没有使用，该图片资源就会被清除掉，但是该图片在项目中是动态赋值的并不应该被清除。 
我采用`ignore`的方式来解决这个问题，也就是先提前准备好一个`ignore`的列表，内容采用正则表达式来定义，被匹配到的文件资源就不进行清除处理,例如`ignores`为：
>ignores = {r'image*\d+'}

然后在定义一个检查`ignores`的方法:
```
def is_ignore(str):
    for ignore in ignores:
        if re.match(ignore, str):
            return True
    return False
```

如果一个文件名在`ignores`中出现就会忽略该文件的清理检查，这样就不会清理掉一些动态赋值的图片资源了。
源码地址：[https://github.com/jjz/script/blob/master/un_used.py](https://github.com/jjz/script/blob/master/un_used.py)

请自行设置好目录，执行命令： 
>python un_used.py


