# Meteor:jade 
##jade是什么？
Jade是一个高性能的模板引擎，语法简洁，它深受Haml影响，它是用JavaScript实现的，并且可以供Node使用。
Jade[官网][(http://jade-lang.com/)给出的例子:
```
doctype html
html(lang="en")
  head
    title= pageTitle
    script(type='text/javascript').
      if (foo) {
         bar(1 + 5)
      }
  body
    h1 Jade - node template engine
    #container.col
      if youAreUsingJade
        p You are amazing
      else
        p Get on it!
      p.
        Jade is a terse and simple
        templating language with a
        strong focus on performance
        and powerful features.
```
翻译成Html:
```
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Jade</title>
    <script type="text/javascript">
      if (foo) {
         bar(1 + 5)
      }
    </script>
  </head>
  <body>
    <h1>Jade - node template engine</h1>
    <div id="container" class="col">
      <p>You are amazing</p>
      <p>
        Jade is a terse and simple
        templating language with a
        strong focus on performance
        and powerful features.
      </p>
    </div>
  </body>
</html>
```
##Meteor中使用jade
在项目目录中直接添加Meteor中对应的包
>meteor add mquandalle:jade




