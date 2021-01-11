## 高级技巧

1. 安全的类型检测
    - 任何值上调用Object的原生toString()方法，都会返回一个[object NativeConstructorName]格式的字符串
    - 由于原生构造函数名和全局作用域无关，使用tostring可以返回一致的值
    ```
    function isArray(value) {
        return Object.prototype.toString.call(value) == "[object Array]"
    }
    function isFunction(value) {
        return Object.prototype.toString.call(value) == "[object Function]"
    }
    function isRegExp(value) {
        return Object.prototype.toString.call(value) == "[object RegExp]"
    }
    // JSON
    var isNativeJSON = window.JSON && Object.prototype.toString.call(value) == "[object JSON]"
    ```
2. 作用域安全的构造函数
    - 问题： 如果没有使用new调用，构造函数的this会绑定到window，覆盖原生属性
    - 方法： 创建作用域安全的构造函数
    ```
    function Person(name, age, job) {
        if(this instanceof Person) {
            this.name = name
            this.age = age
            this.job = job
        }else {
            return new Person(name, age, job)
        }
    }
    var person1 = Person("benjamin", 27, "boss")
    alert(window.name)
    alert(person1.name)

    function Reactangle(width, height) {
        Person.call(this, 2)
    }
    var rect = new Reactangle(5, 10)
    alert(rect.name) // undefined
    ```
    - 如果继承采用借用构造函数模式，会导致无法创建属性
    - 方法：可以结合使用原型链继承
    ```
    Rectangle.prototype = new Person()
    ```
3. 惰性载入函数
    - 方式一：
        - 在函数被调用时处理函数，不同条件下函数会被重新赋值
    - 方式二：
        - 使用自执行函数方式包裹，返回匿名函数
4. 函数柯里化
    - 如函数绑定密切相关的是函数柯里化
    - 它用于创建已经设置好一个或多个参数的函数
    - 使用一个闭包返回一个函数
    - 和函数绑定区别在于，柯里化在调用时，还需要传入一些参数
    - 创建
    ```
    function curry(fn) {
        var arg = Array.prototype.slice.call(arguments, 1)
        return function() {
            var innerArg = Array.prototype.slice.call(arguments)
            var finalArg = arg.concat(innerArg)
            return fn.apply(null, finalArg)
        }
    }
    ```

## 离线应用与客户端存储
1. 