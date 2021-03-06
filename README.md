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
1. 会在不支持js的情况下展示信息，但条件如下：
    - 浏览器不支持javascript脚本
    - 浏览器禁用js脚本

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

## Object类型
1. Object实例都具备下列属性和方法
    - Constructor：保存着用于创建当前对象的函数
    - hasOwnProperty()：用于检查给定的属性，在当前实例中是否存在，参数必须以字符串形式指定
    - isPropertyOf()：检查传入的对象，是否是另一个对象的原型
    - propertyIsEnumerable()：用于检查传入的属性是否能用for-in的语句来枚举，参数必须以字符串形式指定
    - toLocalString()：返回对象的字符串表示，字符串于执行环境地区对应
    - toString()：返回字符串的表示
    - valueof()：返回字符串的表示，和toString()方法的返回值相同

## 操作符
1. 执行‘前置’递增和递减操作，变量的值都是在语句被求值之‘前’改变
2. 执行‘后置’递增和递减操作，变量的值都是在语句被求值之‘后’改变

## 位操作符
1. 对数值应用位操作符时，后台转换过程如下：
2. 64位数值转换为32位数值，然后进行操作
3. 然后将32位结果转换成64位
4. 按位与：只在两个数值对应位都是1时，才返回1
5. 按位或：两个数值对应位有一个是1，就返回1
6. 按位异或：只在两个数值对应位只有一个为1时才返回1，两个都是1或都是0，则返回0
7. 左移 <<：将数值位数左移指定位数，右侧空出补0，左移不会影响操作数对符号位
8. 有符号对右移 >>：位数向右移动，但是会保留符号位
9. 有符号右移 >>>： 会影响负数数值的位
10. 布尔操作符
    - 逻辑非：！
    - 逻辑与：&&
        - 可以应用于任何类型，在有一个操作数不是bool值的情况下，返回的就不一定是bool值
        - 如果第一个操作数是对象，则返回第二个操作：
            ```
            {} && "123" // 返回 “123”
            ```
        - 如果第二个操作数是一个对象，则只有在第一个操作数结果为true的情况下才返回该对象
        - 如果两个操作数都是对象，则返回第二个
        - 如果有一个操作数是null/undefined/NaN，则返回null/undefined/NaN
    - 逻辑或：||


## label/break/continue
1. 使用label语句可以在代码中添加标签，方便日后使用
2. break会立即推出循环，强制执行循环后面的语句
3. continue也是立即推出循环，但会从循环顶部继续执行循环
4. break/continue语句都可以和label搭配使用，从而返回代码中特定的位置，多发生在循环嵌套的情况
5. with语句，将作用域设置到一个特定的对象中，大量使用with会导致性能下降，不推荐使用，严格模式下会报语法错误
6. switch语句：可以忽略break，达到合并使用的目的
    ```
    switch(i) {
        case 25:
        case 35:
            alert("25, 35")
            break;
        default:
            alert("end")
    }
    ```

## 函数
1. 如过出现同名函数，下面的会覆盖上面的函数
2. js的函数不能重载

## 变量，作用域，内存
1. 基本数据类型Null, Undefined, Boolean, Number, String
2. 函数的参数是按值传递的，即使参数是引用类型
    ```
    function setName(obj) {
        obj.name = "ben"
        obj = new Object()
        obj.name = "benjamin"
    }
    var person = new Object()
    setName(person)
    alert(person.name)
    ```
3. 类型检测
    - typeof
        ```
        typeof "123"             // string
        typeof ""                // string
        typeof 123               // number
        typeof true              // boolean
        typeof undefineed        // undeefined
        typeof null              // object
        typeof new Object()      // object
        typeof new Function() {} // function
        ```
    - instanceof
        ```
        语法： result = variable instanceof constructor
        ```
4. 垃圾回收
    - 垃圾回收器要追踪变量的使用情况，对于不再有用的变量打上标记，一边将来收回被占用的内存，标记通常有两个策略
    - 标记清除：1 垃圾回收器会给在内存中的所有变量打上标记
    - 2 它会去掉环境中的变量和被环境中变量引用的变量的标记
    - 3 剩余的被标记的变量被视为准备删除的变量
    - 4 垃圾回收器完成变量清除工作，回收内存空间

    - 引用计数： 追踪记录每个值被引用的次数
    - 1 当声明了变量并赋值引用类型的值时，这个值的引用次数就是1
    - 2 当同一个值又被赋值给又一变量时，引用次数+1
    - 3 相反，这个变量被赋予了新的值，引用次数-1
    - 4 当引用变成0时，就没办法访问这个值了
    - 5 垃圾回收器在下次执行时，就会释放引用为0的变量所占有的内存
    - 会导致的问题：可能存在变量之间相互引用的问题，导致内存得不到回收

    - 性能问题：垃圾回收器是周期性运行的，频繁的触发会导致浏览器性能问题
    - 浏览器中也提供了触发垃圾回收的方法，如window.Collectgarbage()

## 引用类型
1. 构造函数本身是一个函数，只不过它是出于创建新对象而定义的
2. 数组：
    - push(),pop(),shift(),unshift(会改变原数组
    - concat(),slice()会返回新数组
    - splice()方法会返回一个新数组，但同时会改变原数组，返回的新数组中包含删除的项，如果没有删除，返回空数组
        - splice()可以接收N个参数，1: startIndex, 2: 删除个数，3: 要添加/替换的项，4，5，6...
    - indexOf(),lastIndexOf()都接收两个参数：1: 要查找的项，2: 起点索引
        - indexOf()从数组的开头向后查找
        - lastIndexOf()从数组的末尾开始向前查找
        - 都会返回要查找的项在数组中首次出现的位置，没找到的情况下返回-1
    - reduce(),reduceRight()
        - reduce()从数组的第一项开始，逐渐遍历的最后
        - reduceRight()从数组的最后一项开始，逐渐遍历到第一项
        - 二者都接收两个参数，1: 作用在每一项上的函数， 2: 作为缩小基础的初始值
        - 函数有接收四个参数：1: 前一个值， 2: 当前值，3: 项的索引，4: 数组对象
        - 这个函数返回的任何值都会作为参数，传递给下一项
            ```
            var values = [1,2,3,4,5]
            var sum = values.reduce(function(prev, cur, index, array) {
                return prev+cur
            })
            ```
3. RegExp:
    - 模式：g：表示全局模式
    - i：表示不区分大小写
    - m：表示多行模式
    - 三种模式可以组合使用

4. Function类型
    - 没有重载，重名函数只会被覆盖
    - js解析器在执行时，会优先读取函数声明，也叫函数声明提升
    - 函数的内部属性，有两个特殊的对象：arguments和this
    - arguments是一个类数组对象，包含着函数的所有参数，有一个callee的属性，这个属性是一个指针，指向拥有arguments对象的函数
        ```
        function factorial(num) {
            if(num <=1 ) {
                return 1
            }else {
                return num * arguments.callee(num -1)
            }
        }
        ```
    - this: 是函数执行的环境的对象
    - caller: 这个属性中保存着调用当前函数，的函数，的引用，如果调用的函数是全局作用域，caller指向null
    - caller和callee在严格模式下都会报错，非严格模式caller始终是undefined，并且不能给caller赋值（？？？那有啥用？）
    - 函数的属性和方法
        - 每个函数都包含两个属性：length，prototype
            - length表示函数希望接收的命名参数的个数
                ```
                function sum(num1, num2) {
                    alert(num1+num2)
                }
                console.log(sum.length) // 2
                ```
            - prototype: 是保存他们所有实例方法的真正所在
            - prototype属性是不可枚举的，因此使用for-in无法发现
            - 每个函数都包含两个非继承而来的两个方法：apply()，call()
                - apply()方法接收两个参数：1: 一个是在其中运行函数的作用域， 2: 另一个是参数数组
                - call()方法和apply类似，只是第二个参数：需要逐个列举
                - call，apply真正的用途是扩充函数赖以运行的作用域
                - bind()方法会创建一个函数的实例，其this会绑定给传递给bind()方法的函数
5. 基本包装类
    - 引用类型与基本包装类行的主要区别在于对象的生存期
    - 使用new创建的引用类型的实例，在执行流离开当前作用域之前都一直保存在内存中
    - 而自动创建的基本包装类对象，只存在于一行代码的执行瞬间，然后立即被销毁
        ```
        var s1 = "red"
        s1.color = "blue"
        alert(s1.color) // undefinied
        ```
    - 把字符串传给Object()构造函数，就会生成String()的实例，传入数值，就会创建Number类型，传入布尔值，创建Boolean类型
    - 使用new 调用基本包装类型的构造函数，与直接调用同名的转型函数是不一样的
        ```
        var value = "25"
        var number = Number(value)
        alert(typeof number) // number

        var obj = new Number(value)
        alert(typeof obj) // object
        ```
6. boolean类型
    - typeof 操作符，对于基本类型boolean值，返回boolean
    - 对new 创建的基本包装类，返回object
    - 所以instanceof 对于new 的基本包装类，返回true
        ```
        new Boolean(false) instanceof Boolean  // true
        ```
    - 而测试基本类型的boolean值，为false
        ```
        false instanceof Boolean   // false
        ```
7. Number类型
    - toExponential()方法，返回以指数表示法，展示的数值，的字符串形式
    - toPrecision()方法，接收参数代表用几位数表示，会返回一个最接近的数值的字符串形式

8. String类型
    - String类型的每一个实例都有length属性
    - 访问字符串中特定字符的方法：charAt(), charCodeAt(),都接受1个参数，就是基于0字符的位置
    - charAt()会返回字符
    - charCodeAt()会返回字符的编码
    - concat()方法，会将字符串拼接起来，返回一个新的字符串
    - slice(), substr(), substring() 方法都接收两个参数，并且不会改变原字符串(空字符也算入位置)
        - 1: 开始位置
        - 2: 结束位置， substr()的第二个参数代表继其实位置后，返回的个数，如果第二个参数不指定，则以字符串长度作为结束位置
        - 在传递的参数是负值情况时，他们的行为就不同
            - slice()会把负值和字符串长度相加
            - substr()会把负的第一个参数和字符串长度相加，而将负的第二个参数转换成0
            - substring()会把负值都转换成0
    - indexOf(), lastIndexOf(), 从字符串中搜索指定的字符串，返回字符串的位置，没有则返回-1
    - 不同在于一个从头，一个从末尾
    - 也都接收第二个参数，代表起始位置
    - replace()接收两个参数，1: regexp对象，或是字符串， 2: 字符串，或者是一个函数

9. 单体内置对象Global，Math
    - global对象是ECMAScript中特殊的对象，不属于任何对象的属性和方法，最终都属于global对象的，
    - ECMAScript中没有直接指出如何直接访问global对象，但web浏览器都是把global对象作为window对象的一部分加以实现
    - 因此，全局作用域中声明的变量和函数，最终都成了windows对象的属性


    


    