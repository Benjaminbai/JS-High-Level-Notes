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

## js事件循环机制
1. 宏观任务：
    - 宿主（浏览器/node）发起的任务
        - setTimeout
        - setInterval
        - setImmediate （node实现）
        - requestAnimationFrame
        - I/O
        - UI rendering
    - js引擎发起的任务
        - process.nextTick（node实现）
        - Promise
        - Object.observer
        - Mutation.oberver
2. 单线程和异步
    - js的任务分为同步，和异步两种
    - 同步任务是直接在主线程上排队执行的
    - 异步任务则会被放到异步任务队列中
    - 若有多个异步任务，则要在队列中等待
    - 任务队列类似一个缓冲区，任务的下一步会被移到调用栈
    - 然后主线程执行调用栈的任务

    - 单线程是指js引擎中负责解析执行代码的线程只有一个
    - 总而言之，检查调用栈是否为空
    - 以及确定把哪个task加入调用栈
    - 这个过程就是事件循环
    - js实现异步的核心，就是事件循环
3. 调用栈和任务队列
    - 调用栈是一个栈结构
    - 函数调用会形成一个栈帧
    - 帧中包含了当前执行函数的参数，和局部变量等上下文信息
    - 函数执行完后，它的执行上下文会从栈中弹出
4. 运行机制
    - 执行一个宏任务（栈中没有，就从事件队列中获取）
    - 执行过程中如果遇到微任务，就添加到异步任务队列
    - 宏任务执行完毕，栈中空了，开始调用异步任务队列的任务
    - 执行完毕，开始渲染
5. 浏览器的渲染进程是多线程的
    - gui渲染线程
    - js引擎线程
    - 事件触发线程
    - 定时器线程
    - 异步http请求线程

## js事件循环机制 版本二
1. 
    - 事件循环是js运行的核心
    - js是单线程的
    - js的异步事件就是依赖于事件循环机制
2. 事件循环
    - 主线程： 也就是js引擎执行的线程，这个线程只有一个，页面循环，函数处理都在这个线程上执行
    - 工作线程，幕后线程： 这个线程可能存在于浏览器，js引擎内，与主线成分开，处理文件读取，网络请求等异步事件
    - js代码是有一个执行栈的，所有的代码都在栈中执行
    - 在代码执行过程中，如果遇到一些异步代码，以及用户点击等操作，浏览器就会将其放到另一个线程去执行
    - 主线成继续执行栈中的代码
    - 当幕后线程执行完成后，该线程就会将它的回调函数放到任务队列中等待执行
    - 当主线程执行完栈中的所有代码后，它会检查任务队列是否有任务要执行
    - 如果有，就执行，如果任务队列为空了，他就会一直循环，等待任务的到来
    - 任务队列
        - js有两个任务队列
        - 宏任务和微任务
        - 由于执行microsoft任务的时候，以有当为微任务队列为空时，才会进入下一个事件循环
        - 因此，如果源源不断的生成新微任务，就会导致主线程一直被占用
        - 而没办法执行宏任务
    - 定时器问题
        - 并不真正的准确
        - 只是确保任务在100毫秒后进入任务队列
        - 并不意味着他会立刻执行
        - 可能主线程正在进行一个耗时的操作
    - 阻塞还是非阻塞
        - 同步阻塞：小明一直盯着下载进度条，到 100% 的时候就完成。
        - 同步非阻塞：小明提交下载任务后就去干别的，每过一段时间就去瞄一眼进度条，看到 100% 就完成。（轮询）
        - 异步阻塞：小明换了个有下载完成通知功能的软件，下载完成就“叮”一声。不过小明仍然一直等待“叮”的声音（看起来很傻，不是吗最蠢）
        - 异步非阻塞：仍然是那个会“叮”一声的下载软件，小明提交下载任务后就去干别的，听到“叮”的一声就知道完成了。（最机智）
    - async await可以理解为promise的封装
        - 因此在遇到await可以适当进行改写，比较容易做题