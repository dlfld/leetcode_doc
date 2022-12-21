# Python import包原理

**要了解python import包的机制，首先需要了解python import的方式**

## 1. python import的方式

### 1.1 模块和包的基本概念

Python 只有一种模块对象类型，所有模块都属于该类型，无论模块是用 Python、C 还是别的语言实现。 为了帮助组织模块并提供名称层次结构，Python 还引入了 [包](https://docs.python.org/zh-cn/3.8/glossary.html#term-package) 的概念。

**模块（module）**：其实就是一个py文件，里面定义了各种变量，函数，类。简单的来说就是我们日常看到的.py文件其实都是一个module

**包（package）**：一个有层次结构的文件目录，里面包含了模块和一些子包，要求包中必须带有一个__init__.py文件。

要注意的一个重点概念是所有包都是模块，但并非所有模块都是包。 或者换句话说，包只是一种特殊的模块。 特别地，任何具有 `__path__` 属性的模块都会被当作是包。

所有模块都有自己的名字。 子包名与其父包名以点号分隔，与 Python 的标准属性访问语法一致。 例如你可能看到一个名为 [`sys`](https://docs.python.org/zh-cn/3.8/library/sys.html#module-sys) 的模块，以及一个名为 [`email`](https://docs.python.org/zh-cn/3.8/library/email.html#module-email) 的包，这个包又有一个名为 [`email.mime`](https://docs.python.org/zh-cn/3.8/library/email.mime.html#module-email.mime) 的子包和该子包中的名为 `email.mime.text` 的子包。

**包的划分**

+ 常规包

	常规包通常以一个包含 `__init__.py` 文件的目录形式实现。 当一个常规包被导入时，这个 `__init__.py` 文件会隐式地被执行，它所定义的对象会被绑定到该包命名空间中的名称。`__init__.py` 文件可以包含与任何其他模块中所包含的 Python 代码相似的代码，Python 将在模块被导入时为其添加额外的属性。

	也就是我们常常看到的带有init文件的包。

	例如，以下文件系统布局定义了一个最高层级的 `parent` 包和三个子包:

	```python
	parent/
	    __init__.py
	    one/
	        __init__.py
	    two/
	        __init__.py
	    three/
	        __init__.py
	```

	

+ 命名空间包

​		命名空间包是由多个 [部分](https://docs.python.org/zh-cn/3.8/glossary.html#term-portion) 构成的，每个部分为父包增加一个子包。各个部分可能处于文件系统的不同位置。 部分也可能处于 zip 文件中、网络上，或者 Python 在导入期间可以搜索的其他地方。 命名空间包并不一定会直接对应到文件系统中的对象；它们有可能是无实体表示的虚拟模块。

### 1.2 模块的导入方法

**import module_name**

**from module_name import module_element**



## 2. 搜索

为了开始搜索，Python 需要被导入模块（或者包，对于当前讨论来说两者没有差别）的完整 [限定名称](https://docs.python.org/zh-cn/3.8/glossary.html#term-qualified-name)。（这些名称就是import语句当中的一些参数）

此名称会在导入搜索的各个阶段被使用，它也可以是指向一个子模块的带点号路径，例如 `foo.bar.baz`。 在这种情况下，Python 会先尝试导入 `foo`，然后是 `foo.bar`，最后是 `foo.bar.baz`。 如果这些导入中的任何一个失败，都会引发 [`ModuleNotFoundError`](https://docs.python.org/zh-cn/3.8/library/exceptions.html#ModuleNotFoundError)。



### 2.1 模块的查找顺序

`import` 执行时，会尝试使用以下顺序查找 module：

1. 解析器首先尝试搜索自身内置的 module
2. 如果找不到，就会根据 `sys.path` 的顺序查找 
	1. `py` 执行文件本身所在文件夹； 
	2. `PYTHONPATH` 环境变量；
	3. python 默认的安装依赖位置

### 2.2 模块缓存

在开始搜索之前，首先会被检查的地方是 [`sys.modules`](https://docs.python.org/zh-cn/3.8/library/sys.html#sys.modules)，这个映射起到缓存之前导入的所有模块的作用（包括其中间路径）（以map的方式缓存了之前导过的包，如果有的话就直接从缓存里面拿了）。  因此如果之前导入过 `foo.bar.baz`，则 [`sys.modules`](https://docs.python.org/zh-cn/3.8/library/sys.html#sys.modules) 将包含 `foo`, `foo.bar` 和 `foo.bar.baz` 条目。 每个键的值就是相应的模块对象。



### 2.3 查找器和加载器

如果指定名称的模块在 [`sys.modules`](https://docs.python.org/zh-cn/3.8/library/sys.html#sys.modules)（缓存中） 找不到，则将发起调用 Python 的导入协议以查找和加载该模块。 此协议由两个概念性模块构成，即 [查找器](https://docs.python.org/zh-cn/3.8/glossary.html#term-finder) 和 [加载器](https://docs.python.org/zh-cn/3.8/glossary.html#term-loader)。 查找器的任务是确定是否能使用其所知的策略找到该名称的模块。 同时实现这两种接口的对象称为 [导入器](https://docs.python.org/zh-cn/3.8/glossary.html#term-importer) —— 它们在确定能加载所需的模块时会返回其自身。















# Reference

+ [python 官方文档](https://docs.python.org/zh-cn/3.8/reference/import.html)