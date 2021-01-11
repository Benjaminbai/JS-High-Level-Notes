## 事件
1. 事件流
    - 事件冒泡
    - 事件捕获
2. dom事件流
    - 分为三个阶段：
    - 事件捕获阶段
    - 处于目标阶段
    - 事件冒泡阶段
3. html事件处理程序
    - 某个元素上触发了事件，会调用函数，这个函数会有局部变了event，也就是事件对象
    - 通过event变量，可以直接访问事件对象
    - 在这个函数中，this值等于事件的目标元素
4. dom0级事件处理程序
5. dom2级事件处理程序
    - addEventListener()
    - removeEventListener()
    - 都接受3个参数
        - 事件名
        - 处理函数
        - bool值，true表示事件捕获阶段执行，false表示事件冒泡阶段执行，默认false
6. 事件对象
    - 在事件处理对象内部，this指向currentTarget，而target只包含事件的实际目标
    - 阻止事件的默认行为，使用preventDefault()方法
    - stopPropagation() 立即停止事件在dom中的传播，取消进一步的事件捕获或冒泡
    - 事件对象的eventPhase属性，可以用来确定事件处于当前事件流的阶段
        - 1: 捕获阶段
        - 2: 处于目标阶段
        - 3: 冒泡阶段
7. dom3级事件
    - ui事件
    - 焦点事件
    - 鼠标事件
    - 滚轮事件
    - 文本事件
    - 键盘事件
    - 合成事件
    - 变动事件：dom结构发生改变使触发

### 。。。 然后就是各种事件，设备介绍，看不下去

8. 事件委托
    - 利用了事件冒泡
    - 优点： 由于只需要添加一种类型，大大节省了内存，提高了性能
    - 后续添加的元素也能自动绑定上事件


## ajax

1. XMLHttpRequest
    - var xhr = new XMLHttpRequest()
    - xhr.open()接收三个参数： 
        - 1: 发送请求的类型
        - 2: 请求地址
        - 3: 是否异步发送请求的布尔值
    - open()并不会发送请求,只会启动一个请求，准备发送
    - 要想发送请求，要使用send()
        - 接收一个参数，即要为请求主体发送的是数据
        - 如果不需要请求主体发送数据，必须传入null
    - 响应的数据会自动填充xhr的属性
        - responseText: 作为响应体被返回的文本
        - responseXML: 如果响应的类型是“text/xml”，或”aplication/xml“，这个属性中将保存着响应数据的xml dom文档
        - status： 响应的http状态
        - statusText: http的状态说明
        - readyState对象
            - 0: 未初始化，能调用open()
            - 1: 启动，已调用open(),但尚为调用send()方法
            - 2: 发送，已调用send()，但尚未接到响应
            - 3: 接收，已经接收到部分响应数据
            - 4: 完成
            - 只要readyState属性发送变化，就会触发readyStateChange事件
        ```
        var xhr = XMLHttpRequest()
        xhr.onreadystatechange = function() {
            if(xhr.readyState === 4) {
                if(xhr.status >= 200 && xhr.status < 300 || xhr.status === 304) {
                    alert(xhr.responseText)
                }else {
                    alert("unsucccessful" + xhr.status)
                }
            }
        }
        xhr.open("get", "...", true)
        xhr.send(null)
        ```
    - 在接收到响应之前，可以使用abort()方法来取消
2. http头部信息
    - Accept: 浏览器能够处理的内容类型
    - Accept-Charset: 浏览器能够显示的字符集
    - Accept-Encoding: 浏览器能够处理的压缩编码
    - Accept-Language: 浏览器当前设置的语言
    - Connection: 浏览器与服务器之间的链接类型
    - Cookie: 当前页面设置的任何cookie
    - Host: 发出请求的页面所在的域
    - Referer: 发出请求的页面的url
    - User-Agent: 浏览器的用户代理字符串
    - 使用setRequestHeader()可以设置请求头信息
        - 要想成功发送请求头信息，必须在open()方法之后，send()方法之前调用
    - 使用getResponseHeader(),传入字段名，可以取得对应的头部信息
    - 调用getAllResponseHeader(),可以获取到所有头部信息的长字符串
3. XMLHttpRequest 2级
    - FormData
        - append()方法，可以添加一组键值对
        - 通过创建FormData的实例，可以直接传给send()方法
4. overrideMimeType()
    - 比如，浏览器返回的mime类型是text/plain，但数据实际包含的是xml
    - 可以调用overrideMimeType()，保证响应当作xml处理，而非纯文本
    - 调用必须在send()方法之前
5. 进度事件
    - loadStart: 在接收到数据的第一个字节时触发
    - progress: 在接收响应期间持续不断的触发
    - error: 在请求发生错误时触发
    - abort: 调用abort，中止链接时触发
    - load: 接收到完整的响应数据时触发
    - loadend: 在通信完成，或触发error，abort，load事件后触发
6. 跨资源共享
    - 设置请求头的Origin属性
    - 如果服务器认为请求可以接受，就在响应头中的Access-Control-Allow-Origin中，返回相同的信息
    - 如果不匹配，浏览器会驳回请求
    ```
    Origin: http://www.baidu.com
    Access-Control-Allow-Origin: http://www.baidu.com
    ```
7. JSONP
    - 由两部分组成，回调函数，和数据
    - 回调函数的名字在请求中指定
    - 数据就是传递到回调函数中的json数据
    - jsonp通过script元素，指定src属性
8. Comet 服务器推送
    - 有两种实现啊comet方式：长轮询和流
        - 短轮询：浏览器定期向服务求发送请求，看有没有更新的数据
        - 长轮询把短轮询位置颠倒，页面发送一个请求到服务器，然后服务器一直打开，直到有数据可发送
        - 发送完数据之后，浏览器关闭链接
        - 随即在发送新请求，这一过程在页面打开期间，一直持续不断
    - http流，不同于轮询，它的生命周期只使用一个http链接
        - 浏览器发送一个请求
        - 服务器保持连接
        - 然后周期性的给浏览器发送数据
        - 思路：
            - 通过监听readystatechange事件
            - 判断readyState的值是否为3
            - reponseText就会保存接收到的所有数据
            - 比较从什么位置开始取得最新的数据
            ```
            function createStreamClient(url, progress, finished) {
                var xhr = new XMLHttpRequest()
                var received = 0
                xhr.open("get", url, true)
                xhr.onreadystatechange = function() {
                    var result
                    if(xhr.readyState === 3) {
                        result = xhr.responseText.substring(received)
                        received += result.length
                        progress(result)
                    }else if(xhr.readyState === 4) {
                        finished(xhr.responseText)
                    }
                }
                xhr.send(null)
                return xhr
            }
            var client = createStreamClient("...", function(data){
                alert("received" + data)
            }, function() {
                alert("done")
            })
            ```
9. 服务器发送事件 SSE 
    - 用于创建到服务器的单向连接
    - 服务器通过这个连接，可发送任意数量的数据
    - 服务器的响应mime类型必须是text/event-stream
    - SSE支持，短轮询，长轮询，和http流
    - 首先，创建一个新的EventSource对象
        - var source = new EventSource("// url")
        - 传入的url必须和创建sse对象的页面同源
        - 实例有个readyState属性，0代表正式连接到服务器， 1表示打开了连接， 2表示关闭了连接
        - 还有三个事件
            - open: 在建立连接时触发
            - message：从服务器接收到新事件时触发
            - error：在无法建立连接时触发
        - 默认情况下，event source会保持与服务器的连接，如果断开，还会重新连接
        - 如果想断开，不重连，可以使用close方法
        - 服务器在生成事件流时，需要添加一个id
        - 设置了之后，EventSource会追踪上一次触发的事件
        - 如果断开，浏览器会发送包含Last-Event-ID的特殊请求头，便于服务器直到下次触发哪一个
        - 在多次连接的事件流中，这种歌机制可以确保浏览器以正确的顺序收到数据段
10. Web Sockets
    - 是一个单独的持久连接上，提供全双工，双向通信
    - js中创建了Web Sockets后，会有一个http请求发送给浏览器以发起连接
    - 取得服务器响应后，连接会升级，从http升级为web socket
    - web socket使用了自定义的协议
        - 未加密的连接不再是http：//，而是ws：//
        - 加密： 不是https：//， 而是wss：//
    - 创建
        - var socket = new webSocket("ws://www.example.com")
        - 也有readyState属性：
            - WebSocket.OPENING(0): 正在建立连接
            - WebSocket.OPEN(1): 已经建立
            - WebSocket.CLOSING(2): 正在关闭连接
            - WebSocket.CLOSE(3): 已经关闭
        - 调用close关闭
        - 建立连接后，通过send发送任意字符串
        - 只就收纯文本数据，复杂数据格式必须序列化
        - 当服务器向客户端发送数据时，会触发massage事件，数据保存在event.data中
        - 也有open，close，error事件