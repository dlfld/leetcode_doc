# Python AST 模块

## NodeTransformer 和 NodeVisitor

**NodeTransformer**类用于取不同类型并根据我们的要求进行修改。ast 模块还提供了**NodeVisitor**类，它帮助我们在每次遍历树时调用访问函数。

简单的来说，**NodeTransformer**主要是遍历和修改AST，**NodeVisitor**主要是遍历AST。

下面我们给一个例子来说明：

```python
import ast
class Visitor(ast.NodeVisitor):
    def visit_Str(self, node):
        print('String Node: "' + node.s + '"')

class MyTransformer(ast.NodeTransformer):
    def visit_Str(self, node):
        return ast.Str('str: ' + node.s)

parsed = ast.parse("print('Hello World')")
MyTransformer().visit(parsed)
Visitor().visit(parsed)
```

从例子中我们可以看到，**NodeVisitor**的访问方法是没有返回值的，不需要return只需要遍历就行了，然而**NodeTransformer**是需要有返回值的，我们可以先遍历到指定的节点，然后对指定的节点进行修改操作，修改完成之后再返回该节点，就完成了该节点的修改操作。

