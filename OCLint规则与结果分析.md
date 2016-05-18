#OCLint规则与结果分析
OCLint是用来做c,c++和Objective-c的静态代码分析工具。在[OCLint安装和使用]（https://segmentfault.com/a/1190000005150573）介绍了OCLint的安装和是使用。那个运行结果之后，首先要读懂检查的结果，第二个会遇到一些不需要检查的规则，如何禁止忽略一些不需要的规则。
##OCLint规则
最新的OCLint中有67个检查的规则http://docs.oclint.org/en/stable/rules/index.html，
主要对nil值的检查，cocoa的obj检查，类型转换，空值的检查，简洁语法的检查，参数，size,和不使用的参数和变量的检查。
之前介绍的的我们可以使用命令`oclint-json-compilation-database`的**-e**参数忽略指定的文件夹，比如:
>oclint-json-compilation-database -e Pods -- -o=report.html
还可以还用**-rc**改变检查规则的默认值大小：
>oclint-json-compilation-database  -- -rc=LONG_LINE=200 -o=report.html
如果我想禁止某一个规则的使用可以使用命令:
>oclint-json-compilation-database -disable-rule=LongLine
这样就可以禁止在Size中对LongLine的检查。

##OCLint结果分析
OCLint的静态分析结果，警告的级别是从P1,P2,P3依次降低的，可以根据生成的结果找到对应的规则，以及修改建议。
比如下面是一条生成的警告信息:
```
..../.../SuperLoggerPreviewView.m:165:5:  bitwise operator in conditional [basic|P2] 
```
`..../.../SuperLoggerPreviewView.m:165:5`,这个信息的内容是警告产生的文件以及对应的行号。

`bitwise operator in conditional `,警告的描述信息。
` [basic|P2] `这个信息中*convention*是指检查规则的类型。

可以在[Rule Index](http://oclint-docs.readthedocs.io/en/latest/rules/) 的第一个分类`Basic`中可以找到`bitwiseoperatorinconditional`,就是需要了解的规则信息:

##禁止OCLint的检查
有的时候我们知道一段代码会产生OCLint的警告，我们又不想修改代码，或者还没有找到更好的修改方式的时候，可以在代码中禁止OCLint的检查。
1.注解的方式
可以使用注解的方法禁止OCLint的检查,语法:
>__attribute__((annotate("oclint:suppress[unused method parameter]")))

比如我们知道一个参数没有使用，而又不想产生警告信息就可以这样写:
```
- (IBAction)turnoverValueChanged:
    (id) __attribute__((annotate("oclint:suppress[unused method parameter]"))) sender
{
    int i; // won't suppress this one
    [self calculateTurnover];
}
```
对于方法的标注可以这样写:
```
bool __attribute__((annotate("oclint:suppress"))) aMethod(int aParameter)
{
    // warnings within this method are suppressed at all
    // like unused aParameter variable and empty if statement
    if (1) {}

    return true;
}
```

2.!OCLint
也可以通过`//!OCLint`的方式，不让OCLint检查。
比如:
```
void a() {
    int unusedLocalVariable; //!OCLINT
}
```
注释要写在对应的行上面，比如对于空的if/else禁止检查的注释为:
```
if (true) //!OCLint goes here
{
    // it is empty
}
```


