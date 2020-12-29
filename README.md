# JS-High-Level-Notes
JS-High-Level-Notes

# <script />元素的6个属性

1. async 表示立即下载脚本，但并不妨碍页面中但其他操作

2. charset 表示通过src属性指定但代码的字符集

3. defer 表示脚本可以延迟到文档完全被解析和显示之后在执行

4. language 表示编写代码的脚本语言，已经废弃

5. src 表示包含要执行代码的外部文件

6. type 可以看成是language的替代

# async 和 defer
1. 都代表非阻塞执行。
2. async代表异步执行，立即加载脚本
3. defer代表立即加载脚本，但等页面加载完毕后才执行
4. 区别在于defer会按照顺序一次执行，而async则不一定，因此最好不要有脚本等相互依赖关系

# <noscript>元素
1. 会在不支持js的情况下展示信息，但条件
2. 浏览器不支持javascript脚本
3. 浏览器禁用js脚本

# var也有块级作用域
1. 但只在function(){}中有效，在流程控制for(){}, if(){}中无效
2. 非严格模式下声明块级作用域，可省略var，从而达到声明全局变量但目的，但在严格模式下会报错
3. for(var i =0; i<2; i++){}中但var也是全局变量

# undefined类型
1. 变量声明没赋值，默认为undefined
2. undefine === undefined // true
3. typeof undefined === ‘undefined’ // true

# null类型
1. null是一个空对象指针
2. typeof null === ‘object’ //true
3. undefined == null // true
4. undefined === null //false

# boolean类型
1. Boolean(""|null|undefined|0|NaN) // false
2. Boolean(非空字符串|非0数字|对象)