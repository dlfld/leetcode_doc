# 剑指 Offer 20. 表示数值的字符串

请实现一个函数用来判断字符串是否表示**数值**（包括整数和小数）。

**数值**（按顺序）可以分成以下几个部分：



1. 若干空格

2. 一个 **小数** 或者 **整数**

3. （可选）一个 `'e'` 或 `'E'` ，后面跟着一个 **整数**

4. 若干空格



**小数**（按顺序）可以分成以下几个部分：

1. （可选）一个符号字符（`'+'` 或 `'-'`）
2. 下述格式之一：
	1. 至少一位数字，后面跟着一个点 `'.'`
	2. 至少一位数字，后面跟着一个点 `'.'` ，后面再跟着至少一位数字
	3. 一个点 `'.'` ，后面跟着至少一位数字

**整数**（按顺序）可以分成以下几个部分：

1. （可选）一个符号字符（`'+'` 或 `'-'`）

2. 至少一位数字

	

**示例 1：**

```
输入：s = "0"
输出：true
```

**示例 2：**
```
输入：s = "e"
输出：false
```

**示例 3：**
```
输入：s = "."
输出：false
```

**示例 4：**
```
输入：s = "    .1  "
输出：true
```

**提示：**
```
+ 1 <= s.length <= 20
+ s 仅含英文字母（大写和小写），数字（0-9），加号 '+' ，减号 '-' ，空格 ' ' 或者点 '.' 。
```

## 思路
这道题一开始看题解的时候说的是**有限状态自动机**，但是看了之后感觉并不是很懂，然后看到另一种理解的方式；

我们可以把合法字符组合穷举出来，（在预处理的时候去掉前后的空格，减少穷举的数量），然后可以得到如下图所示的图；



<img src="剑指 Offer 20. 表示数值的字符串.assets/image-20221010195943320.png" alt="image-20221010195943320" style="zoom:50%;" />

从图中可以看出，**+-**符号后面可以存在数字或者是**.**;**0~9**数字之后可以存在数字和**.**....以此类推，我们简化这张图就可以得到如下图所示的图；

<img src="剑指 Offer 20. 表示数值的字符串.assets/image-20221010200217636.png" alt="image-20221010200217636" style="zoom:50%;" />

最后我们可以初始化图中的这几个节点，同时记录每个节点可以转移到哪些节点上去，构建出一个节点列表；

例如 0～9 可以转换到. E/e 0～9，之后我们再根据输入的字符串来进行状态的转移，如果字符串能够按照图中的状态一直转移，直到遍历完字符串则表示该字符串可以表示为一个数字。反之，则表示该字符串不能够表示为一个字符串。





## 代码

需要注意的是，在添加节点的时候需要注意节点的添加顺序，不然整个网络是错误的。

### Go

```golang
type Node struct {
	Value   string
	CanLast bool
	Childs  []Node
}
func isNumber(s string) bool {
	root := getRoot()
	s = strings.Trim(s, " ")
	for _, item := range root {
		if check(item, s, 0) {
			return true
		}
	}
	return false
}

// 给一个node 和当前的字符 判断当前的字符能不能和当前的node相符合
func check(node Node, s string, i int) bool {
	if i >= len(s) && len(s) > 0 {
		return true
	}

	// s的长度为0       node的value不包含当前字符。   当前字符是string的最后一个，但是这个字符不能作为最后一个
	if len(s) == 0 || !strings.Contains(node.Value, string(s[i])) || (i == len(s)-1) && !node.CanLast {
		// fmt.Printf("%+v\n",!strings.Contains(s,string(s[i])))
		return false
	}

	if i == len(s)-1 && node.CanLast {
		return true
	}
	// fmt.Printf("node.Value = %+v, s[i] = %c\n",node.Value,s[i])
	for _, item := range node.Childs {
		// fmt.Printf("check -> item.Value = %+v, s[i] = %c.   res = %+v\n",item.Value,s[i+1],check(item,s,i+1))
		if  check(item, s, i+1) == true {
			return true
		}
	}
	// fmt.Printf("%s","here")
	return false
}

func getRoot() []Node {
	SIGN := "+-"
	E := "Ee"
	DOT := "."
	NUM := "0123456789"

	root := []Node{}

	node1 := Node{NUM, true, []Node{}}
	node2 := Node{SIGN, false, []Node{}}
	node3 := Node{DOT, false, []Node{}}

	node4 := Node{E, false, []Node{}}
	node5 := Node{DOT, true, []Node{}}
	node6 := Node{NUM, true, []Node{}}

	node7 := Node{SIGN, false, []Node{}}
	node8 := Node{NUM, true, []Node{}}

	node8.Childs = append(node8.Childs, node8)
	node7.Childs = append(node7.Childs, node8)
	node4.Childs = append(node4.Childs, node7, node8)
    node6.Childs = append(node6.Childs, node4, node6)
    node5.Childs = append(node5.Childs, node4, node6)
	
	node3.Childs = append(node3.Childs, node6)
	node1.Childs = append(node1.Childs, node4, node5, node1)
	node2.Childs = append(node2.Childs, node1, node3)
	root = append(root, node1, node2, node3)
	return root
}
```



### java

```java
class Solution {
    public boolean isNumber(String s) {
        return new Graph().isNumber(s);
    }
}
class Graph {
    public static final String SIGN = "+-";
    public static final String E = "Ee";
    public static final String DOT = ".";
    public static final String NUMBER = "0123456789";
    List<Node> root = new ArrayList<>();

    public boolean isNumber(String s){
        for (Node node : root) {
            if (check(node, s.trim(),0)){
                return true;
            }
        }
        return false;
    }

    private boolean check(Node node, String s, int i) {
        if (i >= s.length() && s.length()>0) {
            return true;
        }
        if (s.length() == 0 || node.value.indexOf(s.charAt(i)) == -1 || i == s.length() - 1 && !node.canBeLast) {
            return false;
        }
        for (Node n : node.next) {
            if (check(n, s, i + 1)) {
                return true;
            }
        }
        return false;
    }

    static class Node {
        String value;
        boolean canBeLast;
        List<Node> next = new ArrayList<>();

        public Node(String value, boolean canBeLast) {
            this.value = value;
            this.canBeLast = canBeLast;
        }
    }

    public Graph() {
        Node node1 = new Node(SIGN, false);
        Node node2 = new Node(NUMBER, true);
        Node node3 = new Node(DOT, true);
        Node node4 = new Node(DOT, false);
        Node node5 = new Node(NUMBER, true);
        Node node6 = new Node(E, false);
        Node node7 = new Node(SIGN, false);
        Node node8 = new Node(NUMBER, true);
        node8.next.add(node8);
        node7.next.add(node8);
        node6.next.addAll(Arrays.asList(node7,node8));
        node5.next.addAll(Arrays.asList(node5,node6));
        node4.next.add(node5);
        node3.next.addAll(Arrays.asList(node5,node6));
        node2.next.addAll(Arrays.asList(node2,node3,node6));
        node1.next.addAll(Arrays.asList(node2,node4));
        root.addAll(Arrays.asList(node1,node2,node4));
    }
}

```

