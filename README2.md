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
        