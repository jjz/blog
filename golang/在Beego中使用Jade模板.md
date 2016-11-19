[Jade](https://pugjs.org/api/getting-started.html)是一个高性能的HTML模板引擎，它受到[Haml](http://haml-lang.com/)的影响,是使用JavaScript实现的。Jade在客户端也有支持，它的代码比html可读性要高很多，Jade是一个比较常用的HTML模板。
[Beego](https://beego.me/)是一个go语言的web应用程序开源web框架，而Beego从1.7.0开始支持更加复杂的模板引擎，当然也包括了对于jade的支持，支持更复杂模板引擎的PR地址[https://github.com/astaxie/beego/pull/1940](https://github.com/astaxie/beego/pull/1940)。
在介绍Jade的使用之前先来看下Go下面的`html/template`包。

##html/template
在Go语言中，`html/template`包是一个很强大的html模板包，结合`text/template`的模板语法，基本上可以满足大部分的HTML模板需求，无论是Beego中是默认支持的两种模板格式`.tpl`和`.html`,以及`jade`和`ace`都是可以解析为`html/template`中的Template对象使用，就是说我们所使用的html模板最终都是基于`html/template`包实现的。
`html/template`使用实例:
```
package main
import (
    "html/template"
)
type User struct {
    Name string
}
func main() {
    t := template.New("template example")
    t, _ = t.Parse("hello {{.Name}}!")
    p := User{Name: "jjz"}
    t.Execute(os.Stdout, p)
}

```
上面的例子会输出字符串：`hello jjz`。
通过上面的例子我们可以看到，如何新建一个模板，再使用模板函数`Parse()`从字符串中加载模板内容，使用模板函数`Execute()`可以给模板替换字段。替换模板字段的语法,`{{}}`中的字段就是要替换字段,`{{. Name}}`表示需要替换的字段名称。
`Beego`使用复杂模板的方式很简单，增加一个模板引擎函数，在项目运行的时候遍历`views`中的文件，把指定的文件解析为`template.Template`对象，返回该对象提供使用，这个模板引擎函数就是：`beego.AddTemplateEngine
`。
##AddTemplateEngine
`beego.AddTemplateEngine`是一个用来把指定的文件，转换为`template.Template`的对象的函数。它的第一个参数是文件的后缀名，在`views`中的含有此后缀名的文件都会进入该方法进行处理。他的第二个参数是一个函数，用来处理文件的转换，最后会返回一个`template.Template`的对象。有了这个函数，我们还少一个把jade文件解析为`template.Template`的方法，还好有人已经做了jade的Go语言实现。
##jade.go
[jade.go](https://github.com/Joker/jade)是Jade的Go语言实现。
`jade.go`的使用，首先安装jade.go：
>go get github.com/Joker/jade

`jade.go`使用示例:
```
func main() {
    tpl, err := jade.Parse("name_of_tpl", "doctype 5: html: body: p Hello world!")
    if err != nil {
        return
    }
    fmt.Printf( "%s", tpl  )
}
```
输出字符串:
```
<!DOCTYPE html>
<html>
    <body>
        <p>Hello world!</p>
    </body>
</html>
```
有了`jade.go`就可以在Beego中使用，把jade文件转换为Template对象，在beego中添加`jade.go`解析jade模板的方法：
```
func addJadeTemplate() {
	beego.AddTemplateEngine("jade", func(root, path string, funcs template.FuncMap) (*template.Template, error) {
		jadePath := filepath.Join(root, path)
		content, err := utils.ReadFile(jadePath)
		fmt.Println(content)
		if err != nil {
			return nil, fmt.Errorf("error loading jade template: %v", err)
		}
		tpl, err := jade.Parse("name_of_tpl", content)
		if err != nil {
			return nil, fmt.Errorf("error loading jade template: %v", err)
		}
		fmt.Println("html:\n%s",tpl)
		tmp := template.New("Person template")
		tmp, err = tmp.Parse(tpl)
		if err != nil {
			return nil, fmt.Errorf("error loading jade template: %v", err)
		}
		fmt.Println(tmp)
		return tmp, err

	})
}
```
`jade.go`目前只支持使用字符串的方式接卸jade模板，因此需要先把.jade文件的内容以字符串的方式读取出来。读取文件的方法:
```
func ReadFile(path string) (str string, err error) {
	fi, err := os.Open(path)
	defer fi.Close()
	fd, err := ioutil.ReadAll(fi)
	str = string(fd)
	return
}
```

jade.go解析出的结果也是一个字符串，因此在代码中需要把字符串转换为`template.Template`对象，使用了` Template.Parse()`方法。
解析jade模板的引擎方法需要在`main()`中调用，添加完jade的模板转换引擎之后，就可以在`Beego`中使用jade模板了。
##jade模板的使用
首先定义一个页面`home.jade`:
```
doctype html
html
  head
    title pageTitle
  body
    h1 jade
    .content {{.content}}
```
其中`{{.content}}`是需要替换的字段，`Controller`层代码：
```

func (c *MainController) Jade() {
	c.Data["content"] = "this is jade template"
	c.TplName = "home.jade"
}
```
运行之后生成的页面代码：
```
<!DOCTYPE html>
<html>
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<title>pageTitle</title>
 	</head>
  	<body>
        <h1>jade</h1>
        <div class="content">this is jade template</div>  
	</body>
</html>
```
通过添加一个解析模板的引擎就可以在beego中使用jade的模板，beego从1.7.0之后开始支持复杂的模板引擎，不仅仅是对于jade的支持，也包括对于其他模板引擎的支持。
除了jade之外，在这个PR中推荐使用的是[ace](https://github.com/yosssi/ace)HTML模板引擎。


##ace
[ace](https://github.com/yosssi/ace)是一个Go语言的HTML模板引擎，它借鉴了[Slim](http://slim-lang.com/)和[Jade](https://pugjs.org/api/getting-started.html),在Go语言里有超高的人气。
`ace模板`在beego中的使用与使用`Jade`类似，首先安装`ace`的依赖包:
>go get github.com/yosssi/ace

在main函数中添加ace模板解析引擎:
```
func addAceTemplate()  {
	beego.AddTemplateEngine("ace", func(root, path string, funcs template.FuncMap) (*template.Template, error) {
		aceOptions := &ace.Options{DynamicReload: true, FuncMap: funcs}
		aceBasePath := filepath.Join(root, "base")
		aceInnerPath := filepath.Join(root, strings.TrimSuffix(path, ".ace"))
		tpl, err := ace.Load(aceBasePath, aceInnerPath, aceOptions)
		if err != nil {
			return nil, fmt.Errorf("error loading ace template: %v", err)
		}
		return tpl, nil
	})	
}

```
注意使用ace模板需要指定一个`base.ace`，另外使用`ace.Load()`函数可以直接返回一个`template.Template`对象，不需要再做其他转换的工作。
添加完模板引擎之后就可以直接在views中添加.ace文件使用，新建一个`home.ace`文件:
```
= doctype html
html lang=en
  head
    meta charset=utf-8
    title Base and Inner Template
  body
    h1 ace
    .content {{.content}}

```
同样的{{.content}}是也需要在`controller`层本替换成需要的内容,controller层代码:
```
func (c *MainController)Ace() {
	c.Data["content"] = "this is ace template"
	c.TplName = "home.ace"
}
```

示例代码地址：[https://github.com/jjz/go/tree/master/template](https://github.com/jjz/go/tree/master/template)

