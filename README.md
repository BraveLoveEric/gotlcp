# Go TLCP

Information security technology **T**ransport **L**ayer **C**ryptography **P**rotocol (TLCP)

GoTLCP采用Go语言实现的传输层密码协议(TLCP)，TLCP协议遵循《GB/T 38636-2020 信息安全技术 传输层密码协议》。

GoTLCP实现了TLCP协议中的记录层协议、握手协议族以及密钥计算，支持完整TLCP握手、会话重用、传输保护、单向身份认证（认证服务端）双向身份认证。

密码套件支持：

- ECC_SM4_CBC_SM3
- ECC_SM4_GCM_SM3

**在使用GOTLCP前，请务必悉知 [***《Go TLCP 免责声明》***](免责声明.md)！**

*若clone和文档预览存在困难，请移步 [https://gitee.com/Trisia/gotlcp](https://gitee.com/Trisia/gotlcp)*

> 致谢：
> 
> - 项目中涉及的SM系列算法由 [emmansun/gmsm](https://github.com/emmansun/gmsm) 项目实现，其项目中通过CPU指令集提升了算法效率。
> - 项目TLCP协议代码裁剪自 go 1.19版本 crypto/tls模块。


## 安装

为了安装使用GoTLCP，您需要首先安装 [Go](https://go.dev/) 并且设置您的Go环境，GoTLCP至少需要您的Go版本在 **1.15以上**。

通过下面命令就可以安装 GoTLCP:

```bash
go get -u gitee.com/Trisia/gotlcp
```

> GoTLCP 将持续保证API的向下兼容，您可以放心的升级GoTLCP库至最新版本。


## 快速开始

### 客户端

```go
package main

import (
	"fmt"
	"gitee.com/Trisia/gotlcp/tlcp"
)

func main() {
	conn, err := tlcp.Dial("tcp", "127.0.0.1:8443", &tlcp.Config{InsecureSkipVerify: true})
	if err != nil {
		panic(err)
	}
	defer conn.Close()

	buff := make([]byte, 516)
	n, err := conn.Read(buff)
	if err != nil {
		panic(err)
	}
	fmt.Printf(">> %s\n", buff[:n])
}
```

上述代码实现了客户端向服务端建立TLCP连接并读取数据，注客户端配置`InsecureSkipVerify`表示跳过服务端证书校验。

- 完整代码见 [quickstart/client/main.go](./example/quickstart/client/main.go)

### 服务端

```go
package main

import (
	"gitee.com/Trisia/gotlcp/tlcp"
	"net"
)


func main() {
	// 证书解析以详见下方完整代码。
	config := &tlcp.Config{
		Certificates: []tlcp.Certificate{sigCert, encCert},
	}

	listen, err := tlcp.Listen("tcp", ":8443", config)
	if err != nil {
		panic(err)
	}
	var conn net.Conn
	for {
		conn, err = listen.Accept()
		if err != nil {
			panic(err)
		}
		_, _ = conn.Write([]byte("Hello Go TLCP!"))
		_ = conn.Close()
	}
}
```

- 完整代码见 [quickstart/server/main.go](./example/quickstart/server/main.go)

## 文档

- [《关于 TLCP协议》](./doc/AboutTLCP.md)
- [《GoTLCP 数字证书及密钥》](./doc/CertAndKey.md)
- [《GoTLCP 客户端 配置》](./doc/ClientConfig.md) 
- [《GoTLCP 服务端 配置》](./doc/ServerConfig.md)
- [《GoTLCP HTTPS 配置》](./doc/HTTPsConfig.md)

## 进展

[>> 项目进展](./releasenote.md)
