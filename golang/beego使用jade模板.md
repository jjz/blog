#beego使用Jade模板
[Jade](https://pugjs.org/api/getting-started.html)是一个高性能的模板引擎，它受到[Haml](http://haml-lang.com/),使用JavaScript实现。Jade在客户端也有支持，它代码比html更加的可读。Jade是一个比较常用的HTML模板。
而[Beego](https://beego.me/)是一个go语言的web应用程序开源框架，而Beego在1.7.0开始支持更加复杂的模板引擎，其中就包括对于jade的支持，PR地址[https://github.com/astaxie/beego/pull/1940](https://github.com/astaxie/beego/pull/1940)。
beego从1.7.0之后开始支持复杂的模板引擎，包括对于ace和jade的支持。
在介绍Jade的使用之前先来看下Go下面的html/template包。

##html/template
在Go语言中，html/template包是一个很强大的html模板引擎，结合text/template的模板语法，基本上可以满足大部分的需求，无论是beego中是支持的两种模板格式`.tpl`和`.html`以及`jade`和`ace`都可以解析为html/template的对象使用，就是说我们所使用的模板最终都会解析为html/template中的template对象使用.
html/template使用实例:
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
通过上面的例子我们可以看到，如果新建一个模板，再使用t.Parse从字符串中加载模板，使用t.Execute给模板替换字段,{{}}保护的字段就是要替换字段,{{.}}表示当前字段。
beego使用复杂模板的方式很简单，增加一个模板引擎函数，遍历views中的文件，把指定的文件解析为template.Template返回提供程序使用，其中要使用一个重要的函数`beego.AddTemplateEngine
`。
##AddTemplateEngine
`beego.AddTemplateEngine`是一个用来把指定的文件，转换为`template.Template`的对象的函数。它的第一个参数是文件的后缀名，在`views`中的含有此后缀名的文件都会进行该方法中进行处理。他的第二个参数是一个函数，用来处理文件的转换，最后会返回一个`template.Template`的指针。
##jade.go
[jade.go](https://github.com/Joker/jade)是Jade的Go语言实现。
使用jade.go也比较简单，首先安装jade.go：
>go get github.com/Joker/jade

jade.go使用示例:
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
在beego中是jade.go解析jade模板：
``` func addJadeTemplate() { 	beego.AddTemplateEngine("jade", func(root, path string, funcs template.FuncMap) (*template.Template, error) { 		jadePath := filepath.Join(root, path) 		content, err := utils.ReadFile(jadePath) 		fmt.Println(content) 		if err != nil { 			return nil, fmt.Errorf("error loading jade template: %v", err) 		} 		tpl, err := jade.Parse("name_of_tpl", content) 		if err != nil { 			return nil, fmt.Errorf("error loading jade template: %v", err) 		} 		fmt.Println("html:\n%s",tpl) 		tmp := template.New("Person template") 		tmp, err = tmp.Parse(tpl) 		if err != nil { 			return nil, fmt.Errorf("error loading jade template: %v", err) 		} 		fmt.Println(tmp) 		return tmp, err  	}) }
```
因为jade.go目前只支持字符串的方式解决jade模板，因此需要先把文件内容以字符串的方式读取出来。读取文件的方法:
``` func ReadFile(path string) (str string, err error) { 	fi, err := os.Open(path) 	defer fi.Close() 	fd, err := ioutil.ReadAll(fi) 	str = string(fd) 	return  }
```
jade.go解析出的html也是一个字符串，因此需要把字符串再转换为`template.Template`。
该方法需要在`main()`中调用，且必须在`beego.Run()`之前调用。添加完jade的模板转换引擎之后，就可以直接使用jade模板了。

##jade模板的使用
首先定义一个页面`home.jade`:
```
doctype html html   head     title pageTitle   body     h1 jade     .content {{.content}}
```
其中`{{.content}}`是需要替换的字段，Controller层代码：
``` func (c *MainController) Jade() { 	c.Data["content"] = "this is jade template" 	c.TplName = "home.jade" }

```
运行之后可以看到页面代码：
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
通过添加一个解析模板的引擎就可以在beego中使用jade的模板。除了jade之外，在这个PR中推荐使用的是[ace](https://github.com/yosssi/ace)。


##ace
[ace](https://github.com/yosssi/ace)是一个Go中的一个HTML模板引擎，它借鉴了[Slim](http://slim-lang.com/)和[Jade](https://pugjs.org/api/getting-started.html),在Go语言里有超高的人气。`ace模板`在beego中的使用与使用`Jade`类似。
首先安装`ace`的依赖包:
>go get github.com/yosssi/ace
在main函数中添加ace模板解析引擎:
```
func addAceTemplate()  { 	beego.AddTemplateEngine("ace", func(root, path string, funcs template.FuncMap) (*template.Template, error) { 		aceOptions := &ace.Options{DynamicReload: true, FuncMap: funcs} 		aceBasePath := filepath.Join(root, "base") 		aceInnerPath := filepath.Join(root, strings.TrimSuffix(path, ".ace")) 		tpl, err := ace.Load(aceBasePath, aceInnerPath, aceOptions) 		if err != nil { 			return nil, fmt.Errorf("error loading ace template: %v", err) 		} 		return tpl, nil 	}) 	 }
```
注意使用ace模板需要指定一个base.ace，使用`ace.Load()`函数可以直接返回一个`template.Template`对象。
添加完模板引擎之后就可以直接在views中添加.ace文件使用，新建一个home.ace文件:
```
= doctype html html lang=en   head     meta charset=utf-8     title Base and Inner Template   body     h1 ace     .content {{.content}}
```
同样的{{.content}}是需要在controller层替换的字段,controller层代码:
```
func (c *MainController)Ace() { 	c.Data["content"] = "this is ace template" 	c.TplName = "home.ace" }
```
ace的使用比jade的使用更加的简便。


