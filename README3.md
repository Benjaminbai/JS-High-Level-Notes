# 函数表达式
定义函数声明的方式有两种：1: 函数声明， 2: 函数表达式

1. 函数声明的一个重要特征，就是函数名提升
    - 执行代码之前，会先读取函数声明
    - 意味着，可以把函数声明在调用它的语句的后面
2. 函数表达式声明
    - 声明一个函数，赋值给一个变量的形式，称为匿名函数
    - function关键字后面没有标识符，因此函数的name属性为空字符串
    - 函数表达式和其它表达式一样，使用前必须先赋值
    - 把函数当成返回值的情况，都可以使用匿名函数
3. 递归
    - arguments.callee代表正在执行函数的指针，可以对函数的递归调用进行解耦
    - 严格模式下，不能通过arguments.callee访问，可以使用命名函数的方式
        ```
        var factorial = (function f(num){
            if(num <= 1) {
                return 1
            }else {
                return num * f(num - 1)
            }
        })
        ```
4. 闭包
    - 概念：是指有权访问另一个函数作用域中变量的函数
    - 常见方式：在一个函数内部再创建另一个函数
    - 作用域链对彻底理解闭包，至关重要
        - 当某个函数第一次被调用时，会创建一个执行环境，和作用域链
        - 并把作用域链，赋值给一个特殊的内部属性（\[scope\]）
        - 然后，使用this，arguments和其它命名函数的值来初始化，函数的活动对象
        - 在作用域链中，外部函数的活动对象始终位于第二位
        - 外部函数的外部函数，位于第三，。。。
        - 直到作为作用域链终点的全局环境
        - 缺点：闭包会携带包含它的函数的作用域
        - 因此它比其它函数更占用内存
        - 过多的使用闭包，会导致内存占用过多，应该谨慎使用
        - 副作用：闭包只能取得包含函数中任何变量的最后一次的值
            ```
            function createFunctions() {
                var result = new Array()
                for(var i = 0; i< 10; i++) {
                    result[i] = function() {
                        return i
                    }
                }
                return result
            }
            ```
            - 表面上看，似乎每个函数都应该返回各自的索引值
            - 但实际上，每个函数都返回10
            - 因为每个函数中，都保存着createFunctions函数的活动对象
            - 所以他们引用的都是同一个变量i
            - 当createFunctions函数返回后，变量i的值是10
        - 可以通过创建一个匿名函数，强制使闭包行为符合预期
            ```
            function createFunctions() {
                var result = new Array()
                for(var i = 0; i< 10; i++) {
                    result[i] = function(num) {
                        return function() {
                            return num
                        }
                    }(i)
                }
                return result
            }
            ```
            - 没有把闭包赋值给数组，而是创建了一个匿名函数
            - 匿名函数有个参数num
            - 每次调用匿名参数时，传入了i
            - 由于参数是按值传递的，而匿名函数内部又包含一个访问num的闭包
            - result数组中，每个函数都有自己的num副本，返回的值就不同了
        - this对象
            - this对象是基于函数的执行环境绑定的
            - 在全局函数中，this等于window
            - 当函数作为某个对象的方法时，this等于这个对象
            - 匿名函数的执行具有全局性，this通常指向window
            ```
            var name = "the window"
            var obj = {
                name: "my obj",
                getName: function() {
                    return function() {
                        return this.name
                    }
                }
            }
            alert(obj.getName()())
            ```
            - 每个函数在被调用时，其活动对象都会自动取得两个特殊变量，this和arguments
            - 内部函数在搜索这两个变量时，只会搜索到其活动对象
            - 因此不能直接访问外部函数中的这两个变量
            - 不过把外部作用域中的this对象，保存在闭包能够访问的变量里
            - 就可以让闭包访问该对象
        ```
        var name = "the window"
        var obj = {
            name: "my obj",
            getName: function() {
                        return this.name
                    }
        }
        obj.getName() // "my obj"
        (obj.getName)() // "my obj"
        (obj.getName = obj.getName)() // "the window"
        ```
        - obj.getName = obj.getName 赋值语句结果是函数本身，所以指向了window

5. 匿名函数可以用来模拟块级作用域
    ```
    (function() {
        // 私有作用域
    })()
    ```
    - 这种做法可以减少闭包占用内存的问题
    - 只要函数执行完毕，就可以立即销毁其作用域链了


## BOM
    BOM既是js访问浏览器的接口，又是ECMAScript规定的global对象
1. window
    - 定义全局变量和直接在window对象上定义属性，是有区别的
    - 全局变量不能通过delete操作符删除
    - 直接在window对象上定义的属性可以
    - 如果页面中包含《frames /》，则每个frames都拥有自己的window对象，可通过索引，name属性访问
    - window.open()可以导航到一个特定的url，也可以打开一个新的浏览器窗口
        - 接收四个参数： 1: 要加载的url
        - 2: 窗口目标（窗口或frames名，会在指定窗口中加载url，如果名称不存在，会根据第三个参数，创建一个新窗口或标签）
        - 3: 一个特性字符串（不打开新窗口情况下，回忽略此参数）
        - 4: 一个布尔值，表示新页面是否取代浏览器历史记录中加载的当前页面（只在不打开新窗口的情况下使用）
    - location对象是个特殊的对象，它既是window对象的属性，又是document对象的属性
        - window.location, document.location其实引用的是同一个对象
        - hash：返回url中“#”后的字符串，没有返回空字符串
        - host：返回服务器名称和端口号
        - hostname：返回不带端口号的服务器名称
        - href：返回完整url，和location.toString()返回值相同
        - pathname：返回url中目录和文件名
        - port：返回端口号，没有返回空
        - protocal：返回使用的协议
        - seracch：返回查询字符串，字符串以问号开头
        - 通过改变这些属性，浏览器都会生成一条新记录，要禁用这种行为，可以使用replace()方法
        - reload()重载当前页面
2. navigator对象
    - 可用于检测浏览器插件
3. screen对象
4. history对象
    - 使用go()方法可以在啊历史记录中任意跳转
    - back(), forward()，后退，前进

## DOM
每个节点都有nodeType属性，用于表明节点类型
有12个node类型，任何类型必居其一
1. Node类型
    - Node.ELEMENT_NODE                         [1]
    - Node.ATTRIBUTE_NODE                       [2]
    - Node.TEXT_NODE                            [3]
    - Node.CDAT_SECTION_NODE                    [4]
    - Node.ENTITY_REFERENCE_NODE                [5]
    - Node.ENTITY_NODE                          [6]
    - Node.PROCESSING_INSTRUCTION_NODE          [7]
    - Node.COMMENT_NODE                         [8]
    - Node.DOCUMENT_NODE                        [9]
    - Node.DOCUMENT_TYPE_NODE                   [10]
    - Node.DOCUMENT_FRAGMENT_NODE               [11]
    - Node.NOTATION_NODE                        [12]
2. 每一个节点都有一个childNodes属性
    - 它保存着一个nodeList对象
    - nodelist对象也有length属性，但它并不是Array的实例
    - 它实际上是DOM结构动态查询的结果
    - 可以使用Array.prototype.slice()方法，将其转换成数组
3. 每个节点都有parentNode属性，指向父级节点
4. 每个节点都有previousSibling, nextSibling属性，不存在则为null
5. 父节点和子节点，有firstChild, lastChil属性
6. 所有节点都有一个属性ownerDocument,指向表示整个文档的文档节点
7. appendChild()
    - 用于向childNodes列表的末尾添加一个节点
    - 返回新增的节点
8. 如果需要把节点放在childNodesl列表中某个特定的位置，可以使用insertBefore()
    - 接收两个参数， 1: 要插入的节点，2: 参照节点
    - 被插入的节点，将会变成参照节点的前一个兄弟节点
9. replaceChild(),接收两个参数：1: 要插入的节点， 2: 要替换的节点
10. 如果只想移除，而非替换，可以使用removeChild()
    - 接收一个参数，要移除的节点
    - 返回被移除的节点
11. 有两个方法是所有类型节点都有的：
    - cloneNode() 用于创建节点的一个副本
        - 接收一个参数，布尔值，表示是否深拷贝
        - 为true则表示复制整个节点及其子节点树
        - false则表示只复制节点本身
        - 节点上js事件不会得到复制
    - normalize()方法，唯一的作用就是处理文档中的文本节点
        - 如果是空文本节点，则删除
        - 如果是两个相邻的文本节点，则合并
12. document类型
    - 特征：
        - nodeType值为 9
        - nodeName值为 #document
        - nodeValue值为 null
        - parentNode值为 null
        - ownerDocument值为 null
    - document对象有一个body属性，指向body元素
    - documentType可以通过doctype访问
    - 查找元素
        - getElementById(), getElementByTagName()
        - 可以给getElementByTagName()指定“*”参数，可以查询页面中所有元素
        - HTMLDocument类型才有的getElementsByName(), 返回有name属性的所有元素
    - 文档写入
        - write()会原样写入
        - writeIn会在末尾添加一个换行符\n
        - 
13. Element类型
    - getAttribute()
    - setAttribute()
    - removeAttribute()
14. Text类型
    - 创建文本节点： document.createTextNode()
15. documentFragment类型
    - 可以保存将来要添加到文档中的节点
    - 文档片段继承了Node的所有方法

## DOM操作技术
1. 动态脚本
2. 动态样式
3. 操作表格


## DOM拓展
1. querySelector()方法
    - 接收一个css选择符，返回匹配的第一个元素
2. querySlectorAll()
    - 返回一个nodeList
3. 元素遍历
    - childElementCount  返回子元素的个数
    - firstElementChild  指向第一个子元素
    - lastElementChild   指向最后一个子元素
    - previousElementSibling 指向前一个兄弟元素
    - nextElementSibling 指向后一个兄弟元素

## HTML5
1. 于类相关的扩充
    - getElementsByClassName()
    - classList属性
    - document.activeElement()会引用获得了焦点的元素，也新增了hasFocus()方法，用于判断是否获得了焦点
2. HTMLDocument变化
    - readState属性
    - 兼容模式 compatMode
    - head属性
    - 字符集属性
    - 自定义数据属性，以data-开头
    - 插入标记
        - innerHTML
        - outerHTML
        - children属性
        - contains方法
        - innerText插入文本
        - outerText

## DOM2 AND DOM3
DOM1级主要定义了html，xml文档的底层结构，dom2级和dom3级引入了更多的交互
1. 元素大小
    - 偏移量
        - offsetHeight: 元素在垂直空间上占用的大小
        - offsetWidth: 元素在水平空间上占用的大小
        - offsetLeft: 元素的左外边框，到包含元素的，左内边框的距离
        - offsetTop: 元素的上外边框，到包含元素的，上内边框的距离
    - 客户区大小
        - 元素的内容，及内边距占用的空间
        - clientWidth: 内容区+左右内边距
        - clientHeight: 内容区+上下内边距
    - 滚动大小
        - scrollHeight: 元素没有滚动的情况下，内容区的高度
        - scrollWidth: 元素没有滚动的情况下，内容区的宽度
        - scrollLeft: 滚动情况下，左侧隐藏的长度
        - scrollTop: 滚动情况下，上方隐藏的长度

## ... 还有很多，DOM,BOM看不下去了
