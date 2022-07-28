## gRPC

GRPC基于HTTP2

### 安装和准备工作

安装go专用的protoc的生成器:

```
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
```



将消息类型用在RPC系统中，在.proto文件中定义一个RPC服务接口，protocol buffer编译器会根据所选择的不同语言生成服务接口代码及存根

````protobuf
service SearchService {
 //rpc 服务的函数名 （传入参数） 返回 （返回参数）
 rpc Search (SearchRequest) returns (SearchResponse);
}
````

、

更新并安装protoc插件

```
go get -u google.golang.org/protobuf/cmd/protoc-gen-go
go get -u google.golang.org/grpc/cmd/protoc-gen-go-grpc
go install google.golang.org/protobuf/cmd/protoc-gen-go
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc
```



在proto文件夹中创建xxx.proto文件

使用protoc 将proto文件编译为 xxx.pb.go 和 xxx_grpc.pb.go 文件

```
protoc --go_out=. product.proto 
protoc --go-grpc_out=. product.proto 
```

一次生成：

```
protoc  --go_out=./service --go-grpc_out=./service pbfile\product.proto
```

--go_out=./service表示生成的GO代码的保存路径

 --go-grpc_out=./service       生成xxx_grpc.pb.go文件

pbfile\product.proto  proto目标文件





proto文件可以通过 import 来引用其他的proto包



### protocal buffers

gRPC 使用 [protocol buffers](https://developers.google.com/protocol-buffers)作为接口定义语言(IDL)来描述服务接口和有效负载消息的结构。

下面是一个完整的 proto 文件

```protobuf
syntax = "proto3"; // 版本声明，使用Protocol Buffers v3版本

option go_package = "hello_server/pb;pb";  // 指定go package名称

package pb; // 包名

// 定义服务
service Greeter {
  // SayHello 方法
  rpc SayHello (HelloRequest) returns (HelloResponse) {}
}

message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  int32 result_per_page = 3;
}
```

#### 定义服务

定义一个 RPC 服务，其方法接受你的 `SearchRequest`并返回一个 `SearchResponse`



#### 分配字段编号

如你所见，消息定义中的每个字段都有一个**唯一的编号**。这些字段编号用来在消息二进制格式中标识字段，在消息类型使用后就不能再更改。注意，范围1到15中的字段编号需要一个字节进行编码，包括字段编号和字段类型。范围16到2047的字段编号采用两个字节。因此，应该为经常使用的消息元素保留数字1到15的编号。切记为将来可能添加的经常使用的元素留出一些编号。

你可以指定的最小字段数是1，最大的字段数是 229−1229−1 ，即536,870,911。你也不能使用19000到19999 的编号，它们是预留给Protocol Buffers协议实现的。如果你在你的`.proto`文件中使用了预留的编号Protocol Buffers编译器就会报错。同样，你也不能使用任何之前保留的字段编号。



### grpc定义四种类型服务方法

- 普通 rpc，客户端向服务器发送一个请求，然后得到一个响应，就像普通的函数调用一样。

```protobuf
  rpc SayHello(HelloRequest) returns (HelloResponse);
```

- 服务器流式 rpc，其中客户端向服务器发送请求，并获得一个流来读取一系列消息。客户端从返回的流中读取，直到没有更多的消息。gRPC 保证在单个 RPC 调用中的消息是有序的。

```protobuf
  rpc LotsOfReplies(HelloRequest) returns (stream HelloResponse);
```

- 客户端流式 rpc，其中客户端写入一系列消息并将其发送到服务器，同样使用提供的流。一旦客户端完成了消息的写入，它就等待服务器读取消息并返回响应。同样，gRPC 保证在单个 RPC 调用中对消息进行排序。

```protobuf
  rpc LotsOfGreetings(stream HelloRequest) returns (HelloResponse);
```

- 双向流式 rpc，其中双方使用读写流发送一系列消息。这两个流独立运行，因此客户端和服务器可以按照自己喜欢的顺序读写: 例如，服务器可以等待接收所有客户端消息后再写响应，或者可以交替读取消息然后写入消息，或者其他读写组合。每个流中的消息是有序的。













### 认证

> 客户端和服务端之间调用，我们可以通过加入证书的方式，实现调用的安全性

TLS（Transport Layer Security，安全传输层)，TLS是建立在`传输层`TCP协议之上的协议，服务于应用层，它的前身是SSL（Secure Socket Layer，安全套接字层），它实现了将应用层的报文进行加密后再交由TCP进行传输的功能。

TLS协议主要解决如下三个网络安全问题。

- 保密(message privacy)，保密通过加密encryption实现，所有信息都加密传输，第三方无法嗅探；
- 完整性(message integrity)，通过MAC校验机制，一旦被篡改，通信双方会立刻发现；
- 认证(mutual authentication)，双方认证,双方都可以配备证书，防止身份被冒充；

#### 1. 生成自签证书

> 生产环境可以购买证书或者使用一些平台发放的免费证书

* 安装openssl

  网站下载：http://slproweb.com/products/Win32OpenSSL.html

  （mac电脑 自行搜索安装）

* 生成私钥文件

  ~~~shell
  ## 需要输入密码
  openssl genrsa -des3 -out ca.key 2048
  ~~~

  密码123456

* 创建证书请求

  ~~~shell
  openssl req -new -key ca.key -out ca.csr
  ~~~

* 生成ca.crt   证书

  ~~~shell
  openssl x509 -req -days 365 -in ca.csr -signkey ca.key -out ca.crt
  ~~~

找到openssl.cnf 文件

cmd输入可查询openssl.cnf文件位置 openssl version -d

1. 打开copy_extensions = copy

2. 打开 req_extensions = v3_req

3. 找到[ v3_req ],添加 subjectAltName = @alt_names

4. 添加新的标签 [ alt_names ] , 和标签字段

   指明验证域名

   ~~~ini
   [ alt_names ]
   
   DNS.1 = *.mszlu.com
   ~~~

5. 生成证书私钥server.key

   ~~~shell
   openssl genpkey -algorithm RSA -out server.key
   ~~~

   

6. 通过私钥server.key生成证书请求文件server.csr

   ~~~shell
   openssl req -new -nodes -key server.key -out server.csr -days 3650 -config ./openssl.cnf -extensions v3_req
   ~~~

7. 生成SAN证书  通过CA来生成证书

   ~~~shell
   openssl x509 -req -days 365 -in server.csr -out server.pem -CA ca.crt -CAkey ca.key -CAcreateserial -extfile ./openssl.cnf -extensions v3_req
   ~~~

- **key：** 服务器上的私钥文件，用于对发送给客户端数据的加密，以及对从客户端接收到数据的解密。
- **csr：** 证书签名请求文件，用于提交给证书颁发机构（CA）对证书签名。
- **crt：** 由证书颁发机构（CA）签名后的证书，或者是开发者自签名的证书，包含证书持有人的信息，持有人的公钥，以及签署者的签名等信息。
- **pem：** 是基于Base64编码的证书格式，扩展名包括PEM、CRT和CER。

什么是 SAN？

SAN（Subject Alternative Name）是 SSL 标准 x509 中定义的一个扩展。使用了 SAN 字段的 SSL 证书，可以扩展此证书支持的域名，使得一个证书可以支持多个不同域名的解析。



#### 2. 服务端应用证书

将`server.key`和`server.pem` copy到程序中

~~~go
func main()  {

	//添加证书
	file, err2 := credentials.NewServerTLSFromFile("keys/mszlu.pem", "keys/mszlu.key")
	if err2 != nil {
		log.Fatal("证书生成错误",err2)
	}
	rpcServer := grpc.NewServer(grpc.Creds(file))

	service.RegisterProdServiceServer(rpcServer,service.ProductService)

	listener ,err := net.Listen("tcp",":8002")
	if err != nil {
		log.Fatal("启动监听出错",err)
	}
	err = rpcServer.Serve(listener)
	if err != nil {
		log.Fatal("启动服务出错",err)
	}
	fmt.Println("启动grpc服务端成功")
}

~~~

#### 3. 客户端认证

公钥copy到客户端

~~~go
func main()  {
	file, err2 := credentials.NewClientTLSFromFile("client/keys/mszlu.pem", "*.mszlu.com")
	if err2 != nil {
		log.Fatal("证书错误",err2)
	}
	conn, err := grpc.Dial(":8002", grpc.WithTransportCredentials(file))

	if err != nil {
		log.Fatal("服务端出错，连接不上",err)
	}
	defer conn.Close()

	prodClient := service.NewProdServiceClient(conn)

	request := &service.ProductRequest{
		ProdId: 123,
	}
	stockResponse, err := prodClient.GetProductStock(context.Background(), request)
	if err != nil {
		log.Fatal("查询库存出错",err)
	}
	fmt.Println("查询成功",stockResponse.ProdStock)
}

~~~

上述认证方式为单向认证：

![img](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/1586953-20210625171059706-1447106002-16509094111532.png)

中间人攻击

#### 4. 双向认证

![img](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/1586953-20210625211235069-195172761-16509094417774.png)

上面的server.pem和server.key 是服务端的 公钥和私钥。

如果双向认证，客户端也需要生成对应的公钥和私钥。

私钥：

~~~shell
openssl genpkey -algorithm RSA -out client.key
~~~

证书:

~~~shell
openssl req -new -nodes -key client.key -out client.csr -days 3650 -config ./openssl.cnf -extensions v3_req
~~~

SAN证书：

~~~shell
openssl x509 -req -days 365 -in client.csr -out client.pem -CA ca.crt -CAkey ca.key -CAcreateserial -extfile ./openssl.cnf -extensions v3_req
~~~

服务端：

~~~go
func main() {

	//添加证书
	//file, err2 := credentials.NewServerTLSFromFile("keys/mszlu.pem", "keys/mszlu.key")
	//if err2 != nil {
	//	log.Fatal("证书生成错误",err2)
	//}
	// 证书认证-双向认证
	// 从证书相关文件中读取和解析信息，得到证书公钥、密钥对
	cert, err := tls.LoadX509KeyPair("cert/server.pem", "cert/server.key")
	if err != nil {
		log.Fatal("证书读取错误", err)
	}
	// 创建一个新的、空的 CertPool
	certPool := x509.NewCertPool()
	//双向认证通过ca证书
	ca, err := ioutil.ReadFile("cert/ca.crt")
	if err != nil {
		log.Fatal("ca证书读取错误", err)
	}
	// 尝试解析所传入的 PEM 编码的证书。如果解析成功会将其加到 CertPool 中，便于后面的使用
	certPool.AppendCertsFromPEM(ca)
	// 构建基于 TLS 的 TransportCredentials 选项
	creds := credentials.NewTLS(&tls.Config{
		// 设置证书链，允许包含一个或多个
		Certificates: []tls.Certificate{cert},
		// 要求必须校验客户端的证书。可以根据实际情况选用以下参数
		ClientAuth: tls.RequireAndVerifyClientCert,
		// 设置根证书的集合，校验方式使用 ClientAuth 中设定的模式
		ClientCAs: certPool,
	})

	rpcServer := grpc.NewServer(grpc.Creds(creds))

	service.RegisterProdServiceServer(rpcServer, service.ProductServerice)

	listener, err := net.Listen("tcp", ":8002")
	if err != nil {
		log.Fatal("启动监听出错", err)
	}
	err = rpcServer.Serve(listener)
	if err != nil {
		log.Fatal("启动服务出错", err)
	}
	fmt.Println("启动grpc服务端成功")
}

~~~

客户端：

~~~go
func main() {
	//file, err2 := credentials.NewClientTLSFromFile("client/keys/mszlu.pem", "*.mszlu.com")
	//if err2 != nil {
	//	log.Fatal("证书错误",err2)
	//}
	// 证书认证-双向认证
	// 从证书相关文件中读取和解析信息，得到证书公钥、密钥对
	cert, _ := tls.LoadX509KeyPair("cert/server.pem", "cert/server.key")
	// 创建一个新的、空的 CertPool
	certPool := x509.NewCertPool()
	ca, _ := ioutil.ReadFile("cert/ca.crt")
	// 尝试解析所传入的 PEM 编码的证书。如果解析成功会将其加到 CertPool 中，便于后面的使用
	certPool.AppendCertsFromPEM(ca)
	// 构建基于 TLS 的 TransportCredentials 选项
	creds := credentials.NewTLS(&tls.Config{
		// 设置证书链，允许包含一个或多个
		Certificates: []tls.Certificate{cert},
		// 要求必须校验客户端的证书。可以根据实际情况选用以下参数
		ServerName: "*.mszlu.com",
		RootCAs:    certPool,
	})

	conn, err := grpc.Dial(":8002", grpc.WithTransportCredentials(creds))

	if err != nil {
		log.Fatal("服务端出错，连接不上", err)
	}
	defer conn.Close()

	prodClient := service.NewProdServiceClient(conn)

	request := &service.ProductRequest{
		ProdId: 123,
	}
	stockResponse, err := prodClient.GetProductStock(context.Background(), request)
	if err != nil {
		log.Fatal("查询库存出错", err)
	}
	fmt.Println("查询成功", stockResponse.ProdStock)
}

~~~

#### 5. Token认证

##### 5.1 服务端添加用户名密码的校验

~~~go
func main()  {
	var authInterceptor grpc.UnaryServerInterceptor
	authInterceptor = func(
		ctx context.Context,
		req interface{},
		info *grpc.UnaryServerInfo,
		handler grpc.UnaryHandler,
	) (resp interface{}, err error) {
		//拦截普通方法请求，验证 Token
		err = Auth(ctx)
		if err != nil {
			return
		}
		// 继续处理请求
		return handler(ctx, req)
	}
	server := grpc.NewServer(grpc.UnaryInterceptor(authInterceptor))
	service.RegisterProdServiceServer(server,service.ProductService)

	listener, err := net.Listen("tcp", ":8002")
	if err != nil {
		log.Fatal("服务监听端口失败", err)
	}
	err = server.Serve(listener)
	if err != nil {
		log.Fatal("服务、启动失败", err)
	}
	fmt.Println("启动成功")
}


func Auth(ctx context.Context) error {
	md, ok := metadata.FromIncomingContext(ctx)
	if !ok {
		return fmt.Errorf("missing credentials")
	}
	var user string
	var password string

	if val, ok := md["user"]; ok {
		user = val[0]
	}
	if val, ok := md["password"]; ok {
		password = val[0]
	}

	if user != "admin" || password != "admin" {
		return status.Errorf(codes.Unauthenticated, "token不合法")
	}
	return nil
}

~~~

##### 5.2 客户端实现





客户端需要实现 `PerRPCCredentials` 接口。

~~~go
type PerRPCCredentials interface {
	// GetRequestMetadata gets the current request metadata, refreshing
	// tokens if required. This should be called by the transport layer on
	// each request, and the data should be populated in headers or other
	// context. If a status code is returned, it will be used as the status
	// for the RPC. uri is the URI of the entry point for the request.
	// When supported by the underlying implementation, ctx can be used for
	// timeout and cancellation. Additionally, RequestInfo data will be
	// available via ctx to this call.
	// TODO(zhaoq): Define the set of the qualified keys instead of leaving
	// it as an arbitrary string.
	GetRequestMetadata(ctx context.Context, uri ...string) (map[string]string, error)
	// RequireTransportSecurity indicates whether the credentials requires
	// transport security.
	RequireTransportSecurity() bool
}
~~~

`GetRequestMetadata` 方法返回认证需要的必要信息，`RequireTransportSecurity` 方法表示是否启用安全链接，在生产环境中，一般都是启用的，但为了测试方便，暂时这里不启用了。

实现接口：

~~~go
type Authentication struct {
    User     string
    Password string
}

func (a *Authentication) GetRequestMetadata(context.Context, ...string) (
    map[string]string, error,
) {
    return map[string]string{"user": a.User, "password": a.Password}, nil
}

func (a *Authentication) RequireTransportSecurity() bool {
    return false
}
~~~

应用：

~~~go
user := &auth.Authentication{
		User: "admin",
		Password: "admin",
	}
	conn, err := grpc.Dial(":8002", grpc.WithTransportCredentials(insecure.NewCredentials()),grpc.WithPerRPCCredentials(user))
	
~~~

