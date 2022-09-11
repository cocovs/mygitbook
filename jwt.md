# JWT

jwt是json web token 的缩写，用于网络应用环境传递声明的一种基于json的开放标准



### jwt由三部分组成，用`.`分割。

分别是 

* 头部Headeer：描述JWT的元数据，使用的加密算法和token类型。

  * `alg`属性表示签名的算法（algorithm），默认是 HMAC SHA256（写成 HS256）；`typ`属性表示这个令牌（token）的类型（type），JWT 令牌统一写为`JWT`。最后，将该 JSON 对象使用 Base64URL 算法转成字符串。

    

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220801114615072.png" alt="image-20220801114615072" style="zoom:67%;" />

* 负载Payload： JSON 对象，用来存放实际需要传递的数据。JWT 规定了7个官方字段，另外还可以添加私有字段。

  <img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220801114304757.png" alt="image-20220801114304757" style="zoom:67%;" />

* 签名Signature：对前两部分的签名，防止数据篡改。需要指定一个密钥（secret）。这个密钥只有服务器才知道，不能泄露给用户。然后，使用 Header 里面指定的签名算法（默认是 HMAC SHA256）

  

  **jwt示例：**

![image-20220801114051182](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220801114051182.png)

### jwt特点：

（1）JWT 默认是不加密，但也是可以加密的。生成原始 Token 以后，可以用密钥再加密一次。

（2）JWT 不加密的情况下，不能将秘密数据写入 JWT。

（3）JWT 不仅可以用于认证，也可以用于交换信息。有效使用 JWT，可以降低服务器查询数据库的次数。

（4）JWT 的最大缺点是，由于服务器不保存 session 状态，因此无法在使用过程中废止某个 token，或者更改 token 的权限。也就是说，一旦 JWT 签发了，在到期之前就会始终有效，除非服务器部署额外的逻辑。

（5）JWT 本身包含了认证信息，一旦泄露，任何人都可以获得该令牌的所有权限。为了减少盗用，JWT 的有效期应该设置得比较短。对于一些比较重要的权限，使用时应该再次对用户进行认证。

（6）为了减少盗用，JWT 不应该使用 HTTP 协议明码传输，要使用 HTTPS 协议传输。



![image-20220804132955771](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220804132955771.png)