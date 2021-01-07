## 面向对象
1. 对象是一组无序的属性的集合，属性可以包含基本值，对象，或函数
2. 对象是一组键值对，值可以是数据，或函数
3. 属性类型有两种：
    - 数据属性：包含一个数据值的位置，在这个位置可以读取和写入
        - \[Configurable\]表示能否通过delete删除属性，从而重新定义属性，能否修改属性特性，或者能否修改为访问器属性，默认值是true
        - \[Enumerable\]表示能否通过for-in循环返回属性，默认值true
        - \[Writable\]表示能否修改属性的值，默认值true
        - \[Value\]读取这个属性的时候，从这个位置读取，默认值undefined
        - 要修改属性默认的特性，必须使用Object.defineProperty()
        - 方法接收三个参数，1: 属性所在的对象， 2: 属性名， 3: 描述符对象
        - 描述符属性必须是configurable，enumerable，writable，value
        - 在调用Object.defineProperty()方法时不指定这些属性，默认值都为false
    - 访问器属性：不包含数据值，他们包含一对getter（读取），setter（写入）函数，不过不是必须的
        - 访问器属性有如下4个特性
            - \[Configurable\]：同数据属性
            - \[Enumerable\]：同数据属性
            - \[Get\]：读取时调用的函数，默认值undefined
            - \[Set\]：写入时调用的函数，默认值undefined
            - 访问器属性，必须通过Object.defineProperty()方法来定义
        - 定义多个属性：Object.defineProperties()方法
            - 接收两个对象参数： 1: 要添加或修改的目标对象，2: 第二个是定义，修改对象的内容
    - 读取属性的特性： Object.definePropertyDescriptor()
        - 接收两个参数：1: 属性所在的对象，2: 要读取描述符，的属性，的名称
4. 工厂模式：设计模式之一
    ```
    function createPerson(name, age, job) {
        var o = new Object()
        o.name = name
        o.age = age
        o.job = job
        o.sayName = function() {
            alert(this.name)
        }
        return o
    }
    var person1 = createPerson("ben", 27, "software engineer")
    var person2 = createPerson("benjamin", 18, "CEO")
    ```
5. 构造函数模式： 设计模式之一
    ```
    function Person(name, age, job) {
        this.name = name
        this.age = age
        this.job = job
        this.sayName = function() {
            alert(this.name)
        }
    }
    var person1 = new Person("ben", 27, "software engineer")
    var person2 = new Person("benjamin", 18, "CEO")
    ```
    - 比较：
        - 没有显式的创建对象
        - 直接将属性和方法赋值给了this对象
        - 没有return语句
        - 函数名首字母大写
        - 必须使用new操作符， 实际上经历了4个步骤
            - 创建一个新对象
            - 将构造函数的作用域赋值给新对象
            - 执行构造函数中的代码
            - 返回新对象
        - person1，2都有constructor属性，指向构造函数Person
        - 通过创建自定义的构造函数，意味着可以将它的实例定义为一种特定的类型，这正是构造函数模式，胜过工厂模式的地方
        - 构造函数也是函数，如果不是用new关键字调用，那么它就是普通函数
        - 通过new 关键字调用，都是构造函数
    - 构造函数的问题：
        - 每个方法，都要在实例上创建一遍，改写如下
            ```
            function Person(name, age, job) {
                this.name = name
                this.age = age
                this.job = job
                this.sayName = sayName
            }
            // 单独放在外面，避免了函数每次创建，但是丝毫无封装性可言
            function sayName() {
                alert(this.name)
            }
            var person1 = new Person("ben", 27, "software engineer")
            var person2 = new Person("benjamin", 18, "CEO")
            ```
6. 原型模式：设计模式之一
    - 每一个函数都有prototype这个属性，这个属性是一个指针，指向一个对象
    - 这样做的好处：不必在构造函数中定义实例的信息，而是把这些信息直接添加到原型对象中
        ```
        function Person() {}
        Person.prototype.name = "benjamin"
        Person.prototype.age = 27
        Person.prototype.job = "software engineer"
        Person.prototype.sayName = functoin() {
            alert(this.name)
        }
        var person1 = new Person()
        person1.sayName() // "benjamin"
        var person2 = new Person()
        person2.sayName() // "benjamin"
        person1.sayName == person2.sayName // true
        ```
7. 理解原型对象
    - 无论何时，只要创建了一个新的对对象，就会根据特定的规则为函数创建一个prototype属性，这个属性指向函数的原型对象
    - 所有原型对象都会自动获得一个constructor属性，这个属性包含一个指向prototype属性所在函数的指针
        ```
        Person(构造)                Person.prototype(原型)
            prototype                   constructor
        person1(实例)               person2(实例)
            [[prototype]]           [[prototype]]
        ```

        - Person.prototype指向了原型对象
        - Person.prototype.constructor 又指回了Person
        - Person的实例person1，person2都包含一个内部属性，该属性仅仅指向了Person.prototype,他们和构造函数之间没有直接关系
        - 虽然在实现中没法直接访问 \[\[prototype\]\]，但是可以通过isPrototypeOf()确定对象之间是否存在这种关系
            ```
            Person.prototype.isPrototypeOf(person1) // true
            ```
        - Object.getPrototypeOf()，这个方法返回\[\[prototype\]\] 的值
            ```
            Object.getPrototypeOf(person1) == Person.prototype // true
            Object.getPrototypeOf(person1).name // "benjamin"
            ```
    - 可以使用hasOwnProperty()方法检测一个属性是否存在于实例中，只有存在于实例中才返回true
8. 原型与in操作符
    - 有两种方法使用in操作符，1: 单独使用， 2: for-in循环
    - 单独使用时，in会在通过对象能够访问给定属性时返回true，无论属性在实例还是原型中
    - 因此in操作符和hasOwnProperty()结合使用，就可以判断指定属性是否存在于原型中
    - 使用for-in循环时，返回的是所有能够通过对象访问的，可枚举的属性，既包括实例中的属性，也包括原型中的属性
    - 要取得对象上可枚举属性的实例属性，使用Object.keys()方法，接收对象作为参数，返回可枚举属性组成的字符串数组
    - 如果想得到当前对象所有的属性，无论是否可枚举，可以使用Object.getOwnPropertyNames()
9. 如果使用对象字面量的方式赋值给Person.prototype, 注意：
    - 会丢失constructor指针，实质上是重写了默认prototype对象，指针会指向Object对象
    ```
    function Person() {

    }
    Person.prototype = {
        constructor: Person,
        name: "benjamin",
        age: "27",
        job: "software engineer",
        sayName: function() {
            alert(this.name)
        }
    }
    ```
    - 注意：以这种方式重设constructor会使它enumerable属性为true，默认情况下，原生的constructor属性是不可枚举的，可以使用Object.definePropoty()
        ```
        Object.definePropoty(Person.prototype, "constructor", {
            enumerable: false,
            value: Person
        })
        ```
10. 原型的动态性
    - new 一个构造函数，会给实例添加\[\[Prototype\]\]指针，指向最初的原型
    - 这时把原型改为另一个对象，就会中断构造函数和最初原型的联系
    - 实例中的指针仅仅指向原型，非构造函数
    ```
    function Person() {}
    var friend = new Person()
    Person.prototype = {
        constructor: Person,
        name: "benjamin",
        age: "27",
        job: "software engineer",
        sayName: function() {
            alert(this.name)
        }
    }
    friend.sayName() // error
    ```
11. 原型对象的问题
    - 对于包含引用类型的属性，会导致数据共享
12. 组合使用构造函数模式和原型模式
    - 构造函数模式用于定义实例属性
    - 原型模式定义方法，和共享属性
    - 这种混合模式还支持向构造函数传递参数
    ```
    function Person(name, age, job) {
        this.name = name
        this.age = age
        this.job = job
        this.friends = ["Shelby", "Court"]
    }
    Person.prototype = {
        constructor: Person,
        sayName: fucntion() {
            alert(this.name)
        }
    }
    var person1 = new Person("benjamin", 27, "CEO")
    var person2 = new Person("ben", 18, "Boss")
    person1.friends.push("van")
    alert(person1.friends) // ["Shelby", "Court", "van"]
    alert(person2.friends) // ["Shelby", "Court"]
    person1.friends === person2.friends // false
    person1.sayName === person2.sayName // true
    ```
13. 动态原型模式
    ```
    function Person(name, age, job) {
        this.name = name
        this.age = age
        this.job = job
        if(typeof this.sayName != "function") {
            Person.prototype.sayName = function() {
                alert(this.name)
            }
        }
    }
    var person1 = new Person("benjamin", 27, "CEO")
    friend.sayName()
    ```
    - 只在sayName不存在的情况下才会添加到原型中
    - 这段代码只会在初次调用时才会执行
    - 此后，原型已经初始化完成，不需要做修改了
    - 使用动态原型模式，就不能使用字面量方式重写原型，会切断原型和构造函数的关系
14. 寄生构造函数模式
    - 和工厂模式很像
    - 不同：调用的时候使用了new关键字，工厂模式没有
    ```
    function Person(name, age, job) {
        var o = new Object()
        o.name = name
        o.age = age
        o.job = job
        o.sayName = function() {
            alert(this.name)
        }
        return o
    }
    var person1 = new Person("ben", 27, "software engineer")
    ```
    - 这个模式可以在特殊情况下，用来为对象创建构造函数
    - 假设需要创建拥有特殊方法的数组，由于不能不能直接修改Array构造函数，可以使用寄生模式
    ```
    function Special() {
        var value = new Array()
        value.push.apply(value, arguments)
        value.toPipedString = fucntion() {
            return this.join("|")
        }
        return value
    }
    var colors = new Special("red", "blue", "yellow")
    colors.toPipedString() // red|blue|yellow
    ```
15. 稳妥构造函数模式
    - 适合在一些安全的环境中（禁用this，new的环境）
    - 类似寄生模式
    - 不同： 不使用this，不使用new
    - 此模式下构造函数，和原型直接没什么联系，因此instanceof这些操作符对这种对象没什么意义

## 继承
1. 概念
    - oo语言支持两种继承方式：接口继承，实现继承
    - ECMAScript只支持实现继承，而且主要依靠原型链
    - 基本思想：利用原型让一个引用类型，继承另一个信用类型的属性和方法
    - 原型链的基本概念
        - 每个构造函数都有一个原型对象
        - 原型对象包含一个指向构造函数的指针
        - 而实例包含一个指向原型对象的内部指针
        - 把原型对象等于另一个类型的实例
        - 此时的原型对象包含指向另一原型的指针
        - 相应的，另一原型包含着指向另一构造函数的指针
        - 假如另一原型，又是另一类型的实例
        - 如此层层递进，即构成了实例与原型的链条，就是原型链
    ```
    function SuperType() {
        this.property = true
    }
    SuperType.prototype.getSuperValue = function() {
        return this.property
    }
    function SubType() {
        this.subproperty = false
    }
    SubType.prototype = new SuperType()
    SubType.prototype.getSubValue = function() {
        return this.subproperty
    }
    var instance = new SubType()
    alert(instance.getSuperValue) // true
    ```
2. 原型链的问题
    - 问题来自引用类型值的原型
    - 第二个问题，创建子类型的实例，无法不能向超类型的构造函数中传递参数
    - 实践中很少单独使用原型链

3. 借用构造函数
    - 基本思想：在子类型的构造函数中，调用超类型的构造函数
    - 通过apply，call方法
    ```
    function SuperType() {
        this.colors = ["red", "blue"]
    }
    function SubType() {
        SuperType.call(this)
    }
    var instance1 = new SubType()
    instance1.colors.push("yellow")
    alert(instance1.colors) // ["red", "blue", "yellow"]

    var instance1 = new SubType()
    alert(instance1.colors) // ["red", "blue"]
    ```
    - 一大优势，可以给超类传参
    - 问题：方法也在构造中定义了，复用无从谈起
    - 超类原型中的属性，方法也不可见，只能访问超类构造

4. 组合继承
    - 将原型链继承，和借用构造函数继承结合起来
    - 原型链实现原型属性，方法的继承
    - 构造函数实现实例属性的继承
    ```
    function SuperType(name) {
        this.name = name
        this.colors = ["red", "blue"]
    }
    SuperType.prototype.sayName = function() {
        alert(this.name)
    }
    function SubType(name, age) {
        SuperType.call(this, name)
        this.age = age
    }
    SubType.prototype = new SuperType()
    SubType.prototype.sayAge = function() {
        alert(this.age)
    }
    var instance1 = new SubType("ben", 18)
    instance1.colors.push("black")
    alert(instance1.colors) // ["red", "blue", "balck"]
    instance1.sayName() // "ben"
    instance1.sayAge() // 18

    var instance2 = new SubType("benjamin", 27)
    alert(instance1.colors) // ["red", "blue"]
    instance1.sayName() // "benjamin"
    instance1.sayAge() // 27
    ```
5. 原型式继承
    - 借助原型，可以给予已有的对象，创建新对象，同时不必因此创建自定义类型
    ```
    function object(o) {
        function F() {}
        F.prototype = o
        return new F()
    }
    var person = {
        name: "benjamin",
        friends: ["1", "2", "3"]
    }
    var anotherPerson = object(person)
    anotherPerson.name = "greg"
    anotherPerson.friends.push("Rob")

    var yetAnotherPerson = object(person)
    yetAnotherPerson.name = "Linda"
    yetAnotherPerson.friends.push("Barbie")

    alert(person.friends) // ["1","2","3","Rob","Barbie"]
    ```
    - ECMAScript5通过 Object.create() 规范了原型式继承
    - 接收两个参数： 1: 一个用做新对象原型的对象
    - 2: 一个为新对象定义额外属性的对象
    - Object.create()与object()方法行为相同
    ```
    var person = {
        name: "benjamin",
        friends: ["1", "2", "3"]
    }
    var anotherPerson = Object.create(person)
    anotherPerson.name = "greg"
    anotherPerson.friends.push("Rob")

    var yetAnotherPerson = object(person)
    yetAnotherPerson.name = "Linda"
    yetAnotherPerson.friends.push("Barbie")

    alert(person.friends) // ["1","2","3","Rob","Barbie"]
    ```
    - Object.create()第二个参数，与Object.defineProperties()方法的第二个参数相同
    - 每个属性都是根据自己的描述符定义的
    - 以这种方式定义的同名属性，会覆盖原型对象上的属性
    - 如果只是想让一个对象保持和另一个对象类似的情况，没必要创建构造函数，原型式继承完全可以胜任

6. 寄生式继承
    - 寄生式和原型式是紧密相关的一种思路
    - 寄生式继承思路，和寄生构造函数，工厂模式类似，即创建一个仅用于封装继承过程的函数
    ```
    function createAnother(original) {
        var clone = object(original)
        clone.sayHi = function() {
            alert("hi")
        }
        return clone
    }
    var person = {
        name: "benjamin",
        friends: ["1", "2", "3"]
    }
    var anotherPerson = createAnother(person)
    anotherPerson.sayHi()
    ```
    - 缺点： 不能做到函数复用，降低效率，和构造函数模式类似

7. 寄生组合式继承
    - 组合继承最大的问题：无论什么情况下，都会调用两次超类型构造函数
    - 一次是在创建子类型的时候
    - 一次是在自类型构造函数内部调用
    - 子类型始终都会包括超类的所有实例属性，我们不得不再调用子类型构造函数时重写这些属性
    ```
    function SuperType(name) {
        this.name = name
        this.colors = ["red"]
    }
    SuperType.prototype.sayName = functioin() {
        alert(this.name)
    }
    function SubType(name, age) {
        SuperType.call(this, name) // 第二次调用
        this.age = age
    }
    SubType.prototype = new SuperType() // 第一次调用
    SubType.prototype.constructor = SubType
    SubType.prototype.sayAge = function() {
        alert(this.age)
    }
    ```
    - 所谓寄生组合继承，通过借用构造函数继承属性，通过原型链继承方法
    - 思路：不必为了指定子类型的原型而调用超类型的构造函数
    - 本质上： 就是使用寄生式继承来继承超类型的原型，然后将结果指定给子类型的原型
    ```
    function inheritPrototype(SubType, SuperType) {
        var prototype = object(SuperType.prototype)
        prototype.constructor = SubType // 我理解：这块等同于prototype.prototype.constructor = SubType; 因为原型式object()创建了一个实例，而实例和构造没有直接联系，实例没有constructor属性，所以会往原型上找，能找到就行
        SubType.prototype = prototype
    }
    ```
    - 高效：只调用了一次超类型构造函数
    - 避免了在子类原型上创建重复的属性
    - 原型链保持不变
    - 能够正常使用instanceof(),isPrototypeOf()
    - 寄生组合式继承，是最理想的继承方式


