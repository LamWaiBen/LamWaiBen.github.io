# WebSocket
WebSocket是HTML5开始提供的在一种在TCP连接上进行全双工通信的协议.属于应用层协议

## 特点
1. 建立在TCP协议上
2. 与HTTP协议有良好的兼容性.默认端口也采用80和443,并且握手阶段采用HTTP协议(状态码101),能通过各种HTTP代理服务器.
3. 数据格式比较轻量,性能开销小,通信高效.
4. 可以发送文本,也可以发送二进制数据.
5. 没有同源限制,客户端可以与任意服务器通信.
6. 协议标示符是`ws`(如果加密,则为`wss`), 服务器网址就是URL

## 可能存在的问题
1. 由于没有同源限制,而且切换ws协议时的http请求中cookie会自动设置到头部,所以ws存在跨站点websocket劫持的安全隐患.防范方法:
    - 判断Origin的来源, 但是也存在被抓包修改Origin的可能性,这时候则需要https与wss来防范了.
    - 也可以参考防范CSRF的方法,服务端发送token到客户端, 客户端建立连接时把token返回,服务端验证token合法性,无误后才建立连接.
2. 可以被恶意Dos攻击

## 注意
Socket是应用层与TCP/IP协议族的中间软件抽象层,它是一组接口.
Socket是传输控制层协议,WebSocket是应用层协议,两者并不一样.