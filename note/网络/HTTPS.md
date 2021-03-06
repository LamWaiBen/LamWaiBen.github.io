# HTTPS
准确来说HTTPS并非是一种协议, 而是`HTTP+SSL/TSL`的结合体.

HTTPS就是在HTTP层与TCP层中间添加了一个SSL层,因为多了加密的流程, 所以HTTPS的速度要比HTTP慢.

## CA证书
SSL层加密主要采用的是RSA(非对称加密)与AES(对称加密)结合的加密方式.
在建立连接时服务器需要用到RSA把通信时的密钥发送给客户端, 但是如何确保客户端收到的密钥是没有被篡改过的呢?

这时候就需要有一个公正的机构来证明客户端收到的密钥就是服务器发送的密钥.这个中间认证机构就是数字证书认证机构,其颁发的证书就是`CA证书(Certificate Authority)`.

CA证书的签名,分发,验证过程:
1. 服务端使用RSA算法生成公钥与私钥, 公钥需要向`CA机构`申请进行数字签名, 得到数字签名公钥证书, 得到签名猴的证书才能分发给客户端.
2. 生成数字签名公钥证书: 对CA机构来说, 其实也有两个密钥(`CA公钥`和`CA私钥`).CA机构把服务端的公钥作为输入参数转换成一串hash值,然后使用CA私钥将这个hash值进行加密处理,并与服务端的公钥绑定在一起,生成数字签名证书.所以数字签名证书的本质就是`服务端的公钥+CA私钥加密的hash值`. (`CA私钥负责签名,CA公钥负责验证`)
3. 服务器获取到这个证书以后,把证书发送给客户端.当客户端收到数字证书后,会验证其有效性,大部分客户端都会预装`CA机构的公钥`.客户端使用CA公钥对数字证书上的签名进行验证,验证过程就是使用`CA公钥对CA私钥加密的内容进行解密`,将`解密后的内容`与服务端的公钥所生成的Hash值进行匹配,匹配成功则是合法证书
4. 验证服务端公钥合法后,就可以使用公钥进行加密通信

![pic_06](../../pic/tcp_ip_06.png)

## HTTPS安全通信机制
HTTPS与HTTP的主要差异在于SSL/TSL,

SSL -- 安全套节层(Secure Socket Layer),TSL(Transport Layer Security 安全传输层)是以SSL为原型开发的协议.

下面我们主要说一下SSL的加密过程, 主要可以分为五步:
> 1. 客户端给出协议版本号, `随机数1(Client random)`, 以及支持的加密方法
> 2. 服务端确认双方的加密方法,并给出数字证书(内含公钥), 以及服务器随机生成的`随机数2(Server random)`
> 3. 客户端确认数字证书有效后, 生成一个`随机数3(Premaster secret)`并使用数字证书中的公钥对其加密, 随后发给服务端(RSA不对称加密)
> 4. 服务端使用私钥, 解密客户端发来的随机数3
> 5. 客户端和服务端根据约定的加密方法, 使用前面的三个随机数, 生成对话密钥(session key), 用于AES对称加密接下来的HTTP报文


![pic_07](../../pic/tcp_ip_07.png)