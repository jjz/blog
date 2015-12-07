golang 中 cannot use ** (type interface {}) as type **解决方案
在beego中从session中取值的时候，取出来的是intergace{}，但是我先返回的值是int型，或者是string，这个时候会出现一个错误：
`cannot use ** （变量）(type interface {}) as type **（类型）

错误代码：

    func CurrentId(ctx *context.Context) int {
        userStr := ctx.Input.CruSession.Get("user_id")
        return userStr
    }

从session中取出来的是一个`interface`类型，无法直接转换，我在给user_id赋值的时候是给的int类型。
因此直接对userStr进行转换即可。

正确代码如下：

    func CurrentId(ctx *context.Context) int {
        userStr := ctx.Input.CruSession.Get("user_id")
        return userStr.(int)
    }
