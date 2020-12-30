# JS-High-Level-Notes
JS-High-Level-Notes

## <script />元素的6个属性

1. async 表示立即下载脚本，但并不妨碍页面中但其他操作

2. charset 表示通过src属性指定但代码的字符集

3. defer 表示脚本可以延迟到文档完全被解析和显示之后在执行

4. language 表示编写代码的脚本语言，已经废弃

5. src 表示包含要执行代码的外部文件

6. type 可以看成是language的替代

## async 和 defer
1. 都代表非阻塞执行。
2. async代表异步执行，立即加载脚本
3. defer代表立即加载脚本，但等页面加载完毕后才执行
4. 区别在于defer会按照顺序一次执行，而async则不一定，因此最好不要有脚本等相互依赖关系

## <noscript>元素
1. 会在不支持js的情况下展示信息，但条件
2. 浏览器不支持javascript脚本
3. 浏览器禁用js脚本

## var也有块级作用域
1. 但只在function(){}中有效，在流程控制for(){}, if(){}中无效
2. 非严格模式下声明块级作用域，可省略var，从而达到声明全局变量但目的，但在严格模式下会报错
3. for(var i =0; i<2; i++){}中但var也是全局变量

## undefined类型
1. 变量声明没赋值，默认为undefined
```
    undefine === undefined // true
    typeof undefined === ‘undefined’ // true
```

## null类型
1. null是一个空对象指针
```
    typeof null === ‘object’ //true
    undefined == null // true
    undefined === null //false
```

## boolean类型
```
    Boolean(""|null|undefined|0|NaN) // false
    Boolean(非空字符串|非0数字|对象) // true
```

## number类型
1. 八进制第一位必须是0，否则会被当成十进制解析
2. 八进制字面量在严格模式下是无效的，javascript引擎会抛出错误
3. 十六进制前两位必须是0x
4. 在进行算术运算时，八进制，十六进制都会转换成十进制
5. js浮点数最高精度是小数点后17位
```
    0.1 + 0.2 == 0.3 // false 0.30000000000000004
```
6. NaN和任何值都不下相等，包括它自己
7. NaN == NaN // false
8. isNaN() 用来判断一个值是否是NaN
```
    isNaN(true) // false true会被转换成1
```