# JS-High-Level-Notes
文档是《javascript高级程序设计》一书的阅读笔记
旨在记录js的细节知识点，方便温故知新

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
9. isNaN也适用于对象，会先调用对象的valueof()，然后确定是否能够转换成数值，如果不能，则基于这个返回值再调用toString()方法，在检查是否能转换成数值
10. valueOf() 方法可返回 String 对象的原始值，valueOf() 方法通常由 JavaScript 在后台自动进行调用，而不是显式地处于代码中
```
    var str="Hello world!"
    str.valueOf() // Hello world!
```
11. Number()数值转换
    - 如果是boolean值，true/false将会被转换成1/0
    - 如果是null，返回0
    - 如果是undefined，返回NaN
    - 如果是字符串：
        - 空字符，返回0
        - 字符串表示的有效十六进制，返回十进制结果
        - 浮点数，返回浮点数，会忽略前导0
        - 整数，返回整数，会忽略前导0，（我理解八进制也会忽略0）
    - 如果是对象
        - 调用对象的valueof()方法
        - 如果返回是NaN,在调用toString()方法
```
    var num1 = Number("hello world") // NaN
    var num2 = Number("") // 0
    var num3 = Number("0011") // 11
    var num4 = Number(true) // 1
```
12. parseInt()数值转换
    - 会检查是否符合数值模式，它会忽略字符串前面的空格，直到找到第一个非空字符
    - 如果第一个字符不是数字，或负号，返回NaN
    ```
        parseInt("") //返回NaN
        Number("") //返回0
    ```
    - 如果第一个字符是数值，会继续解析，直到遇到非数字字符
    - parseInt()也可以识别八进制，十六进制
    ```
        var num1 = parseInt("1234blue") // 1234
        var num2 = parseInt("") // NaN
        var num3 = parseInt("0xA") // 10（十六进制）
        var num4 = parseInt(22.5) // 22
        var num5 = parseInt("070") // 56 (八进制)
        var num6 = parseInt("70") // 70 (十进制)
    ```
    - parseInt()支持第二个参数
    ```
        var num1 = parseInt("0xAF", 16) // 175
        var num2 = parseInt("AF", 16) // 175 指定了第二个参数，可以省略0x
    ```
13. parseFloat()数值转换
    - 和parseInt()类似，但parsefloat只解析十进制，因此没有第二个参数
    ```
        var num1 = parseFloat("1234blue") // 1234
        var num2 = parseFloat("0xA") // 0
        var num3 = parseFloat("22.5") // 22.5
        var num4 = parseFloat("22.5.5.5") // 22.5
        var num5 = parseFloat("0987") // 987
        var num6 = parseFloat("") // NaN
    ```

## string类型
```
    var str = "java"
    str = str + "script"
```
1. 后台首先创建了能容纳10个字符的新字符，储存了javascript，然后销毁原来的字符串java，和script，这也是旧版本浏览器拼接字符串速度很慢的原因
2. 数值，布尔值，对象，字符串都有toString()方法，该方法返回字符串的一个副本
3. null和undefined没有这个方法
4. toString()一般不需要传参数，但可以传递一个参数，toString()支持任意有效进制，返回对应进制的结果
```
var num = 10
num.toString() // "10"
num.toString(2) // "1010"
num.toString(8) // "12"
num.toString(10) // "10"
num.toString(16) // "a"
```
5. 在不确定要转换的值是null/undefined情况下，可以使用String()方法，可以将任何类型转换成字符串
    - 如果值有toString()方法，则调用
    - 如果值是nulll， 返回"null"
    ```
        String(null) // "null"
    ```
    - 如果值是undefined， 返回"undefined"
    ```
        String(undefined) // "undefined"
    ```

    