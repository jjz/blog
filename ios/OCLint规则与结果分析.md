#OCLint规则与结果分析`OCLint`是用来做c,c++和Objective-c的静态代码分析工具。在[OCLint安装和使用](https://segmentfault.com/a/1190000005150573)中介绍了`OCLint`的安装和是使用。运行`OCLint`之后，要根据规则读懂结果,分析和修改代码。 有的时候代码出现的一些警告，并不需要立刻修改，但是有一个警告在哪里看着还是挺别扭的，这个时候会就需要了解如何禁止或者忽略一些规则，来消除警告。 ##OCLint规则 最新的OCLint中有67个检查的规则 http://docs.oclint.org/en/stable/rules/index.html， 主要对针对nil值的检查，cocoa的obj检查，类型转换，空值的检查，简洁语法的检查，参数，size和不使用的参数和变量的检查。 主要分为9大类:

```
    Basic
    Cocoa
    Convention
    Empty
    Migration
    Naming
    Redundant
    Size
    Unused
```

之前介绍的的我们可以使用命令:`oclint-json-compilation-database`的**-e** 参数忽略指定的文件，比如忽略Pods文件夹下面的检查: >oclint-json-compilation-database -e Pods -- -o=report.html

还可以还用 **-rc** 改变检查规则的默认值： >oclint-json-compilation-database -- -rc=LONG_LINE=200 -o=report.html

如果想禁止某一个规则的使用可以使用命令`-disable-rule`: >oclint-json-compilation-database -disable-rule=LongLine

这样就可以禁止`Size`中对`LongLine`的检查。

##OCLint结果分析 OCLint的静态分析结果，警告的级别是从P1,P2,P3依次降低的，可以根据生成的报告找到对应的规则，以及修改建议。 比如下面是一条生成的警告信息:

```
..../.../SuperLoggerPreviewView.m:165:5:  bitwise operator in conditional [basic|P2]
```

`..../.../SuperLoggerPreviewView.m:165:5`：的意思是产生警告的文件以及对应的行号。

`bitwise operator in conditional`：描述信息。`[basic|P2]`这个信息中*basic*是指检查规则的类型,对应检查规则的9大类别，`P2`是警告的级别

可以在[Rule Index](http://oclint-docs.readthedocs.io/en/latest/rules/) 的第一个分类`Basic`中可以找到`bitwiseoperatorinconditional`,就是需要了解的规则信息:

![Rule Index](http://upload-images.jianshu.io/upload_images/22188-d21c3ff8bfec6cd4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##禁止OCLint的检查 有的时候在已知的情况下一段代码会产生OCLint的警告，但是因为其他的一些原因，我们又不能修改代码，或者还没有找到更好的修改方式的时候，可以在代码中禁止**OCLint**的检查。

1.注解

可以使用注解的方法禁止OCLint的检查,语法是:

> **attribute**((annotate("oclint:suppress[unused method parameter]")))

比如我们知道一个参数没有使用，而又不想产生警告信息就可以这样写:

```
- (IBAction)turnoverValueChanged:
    (id) __attribute__((annotate("oclint:suppress[unused method parameter]"))) sender
{
    int i; // won't suppress this one
    [self calculateTurnover];
}
```

对于方法的注解可以这样写:

```
bool __attribute__((annotate("oclint:suppress"))) aMethod(int aParameter)
{
    // warnings within this method are suppressed at all
    // like unused aParameter variable and empty if statement
    if (1) {}

    return true;
}
```

2.!OCLint 也可以通过`//!OCLint`注释的方式，不让OCLint检查。 比如:

```
void a() {
    int unusedLocalVariable; //!OCLINT
}
```

注释要写在对应的行上面才能禁止对应的检查，比如对于空的if/else禁止检查的注释为:

```
if (true) //!OCLint
{
    // it is empty
}
```
