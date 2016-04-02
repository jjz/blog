#清理ios项目不用的图片资源

项目经过需求的变更，产品本身的迭代，会经过多次的改版，有一些以前的图片不一定能及时的清理掉，这些图片一方面让项目图片资源的结构更加的复杂，另一方便会直接导致上线之后的ipa包的体积大，所以需要清理不用的图片资源，在Android项目中使用Lint可以轻松的完成这个任务，在ios上没有找到比较好的工具，因此借助网上的例子，写了一个python清除Xcode不用的图片资源的脚本。
先简单说下ios中使用图像资源的方式，ios中使用 `Images Assets`管理图片，图片资源都是放在这里面，项目中的图片资源会放到`*.imageset`目录中，里面对应1x,2x和3x的图,还有一个Contents.json的描述文件。清除图片资源需要把这个目录清除。
在代码中调用图片的方法一般是:
>[UIImage imageNamed:@"image"]

在xib中调用图片的方法是用xml的方式描述,图片资源作为image的名称:
```
 <imageView  image="image" id="Rb9-sl-eQM">                
 </imageView>
```
所以需要搜索在项目中文件的文件，如果找到对于的`"name"`，说明该图片是被使用的，不然的话该图片即可被清理掉。 

#搜索文件
搜索文件的方式可以使用`grep`,`ack`都是不错的工具，但是有一种更快，更好的搜索文件内容的方式:`The Silver Searcher`，项目地址：https://github.com/ggreer/the_silver_searcher。
`The Silver Searcher`使用方便，更快，更简单。
安装The Silver Searcher:
>brew install the_silver_searcher

使用ag搜索:
>ag "image" './'

就是搜索到该目录下以及其子目录下的所有保护`image`的文件。


#bash命令
需要使用一些bash命令来简单一些搜索，删除的工作，在python中可以直接运行bash命令。
在python中可以使用:
>os.popen('echo $PATH')
执行bash命令，可以通过`os.popen('echo $PATH').read()`读取执行的结果。
也可以直接执行bash命令:
>os.system('rm -rf dir')
需要用到的bash命令是`ag "image" './'`和`rm -rf './'`,后者用来删除文件夹。
#查找不使用的`imageset`
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
这段代码从当前文件夹里，找到所有的`.imageset`文件，并只使用没有后缀名的文件名，在文件夹中找到该名称的文件有没有被使用。如果没有被使用的就清除资源。
执行这段之后发现有一些不应该清除图片资源也被清除了，因为有一种例外的没有考虑到。

#ignores

有的时候我们需要动态的给一些控制赋值会使用这样的方式：
```
for (int i = 1; i <= 10; ++i) {
    NSString *imageName = [NSString stringWithFormat:@"image_%d", i];
    UIImage *image = [UIImage imageNamed:imageName];
    ......
}
```
比如上面的代码中查找 *iamge_1*时，发现这个图片并没有使用，就会清除该图片，但是改图片并不应该被清除。
采用`ignore`的方式来解决这个问题，也就是我会提前准备好一个ignore的列表，内容采用正则表达式来定义，比如这样：
>ignores = {r'image_\d+'}
然后在定义一个检查ignore的方法:
```
def is_ignore(str):
    for ignore in ignores:
        if re.match(ignore, str):
            return True
    return False
```
如果一个文件名在ignore中就会忽略清除该文件。

python源码地址：https://github.com/jjz/script/blob/master/un_used.py

请自行设置目录，执行命令：
>python un_used.py



