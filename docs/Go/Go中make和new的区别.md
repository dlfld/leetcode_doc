# Go中make和new的区别

**[Go 语言](https://haicoder.net/golang/golang-tutorial.html)** 中 new 和 make 是两个内置 **[函数](https://haicoder.net/golang/golang-func.html)**，主要用来创建并分配内存。Golang 中的 new 与 make 的区别是 new 只分配内存，而 make 只能用于 **[slice](https://haicoder.net/golang/golang-slice.html)**、**[map](https://haicoder.net/golang/golang-map.html)** 和 **[channel](https://haicoder.net/golang/golang-channel.html)** 的初始化。



## new和make主要区别

- make 只能用来分配及初始化类型为 slice、map、chan 的数据，而 new 可以分配任意类型的数据。
- new 分配返回的是指针，即类型 *Type。make 返回引用，即 Type。
- new 分配的空间被清零。make 分配空间后，会进行初始化。



## new函数

`func new(Type) *Type`

new 函数只接受一个参数，这个参数是一个 Golang 的 **[数据类型](https://haicoder.net/golang/golang-datatype.html)**，并且返回一个指向该类型内存地址的指针。同时 new 函数会把分配的内存置为零，也就是类型的零值。它返回的永远是类型的指针，指向分配类型的内存地址。

在程序中，如果对于指针类型的变量，我们不对其分配内存空间的话，后面再对其使用就会报错（野指针），因此就可以使用new方法来为变量分配内存空间。比如正确的写法应该是这样：

```go
func main() {
    var i *int
    i=new(int)
    *i=10
    fmt.Println(*i)

}
```

如果不分配内存空间的话就会报错：

```go
package main

import (
    "fmt"
)

func main() {
    var i *int
    *i=10
    fmt.Println(*i)

}
```

## make函数

`func make(t Type, size ...IntegerType) Type`

`make`也是用于内存分配的，但是和`new`不同，它只用于`chan`、`map`以及切片的内存创建，而且它返回的类型就是这三个类型本身，而不是他们的指针类型，因为这三种类型就是引用类型，所以就没有必要返回他们的指针了。

注意，因为这三种类型是引用类型，所以必须得初始化，但是不是置为零值，这个和`new`是不一样的。





## **二者异同**

所以从这里可以看的很明白了，二者都是内存的分配（堆上），但是`make`只用于slice、map以及channel的初始化（非零值）；而`new`用于类型的内存分配，并且内存置为零。所以在我们编写程序的时候，就可以根据自己的需要很好的选择了。

`make`返回的还是这三个引用类型本身；而`new`返回的是指向类型的指针。

