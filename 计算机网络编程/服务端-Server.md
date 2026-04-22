
需要预先学习：
- [[网络协议]]
- [[文件描述符]]

**包含库文件，并在main函数里初始化该库**

```cpp
#include <winsock2.h>

int main()
{
	WSADATA wsaData;
	int result = WSAStartup(MAKEWORD(2, 2), &wsaData);
  
    if(result != 0)
    {
        std::cerr << "WSAStartup failed: " << result << std::endl;
        return 1;
    }

	// ... 其他代码 ...

	WSACleanup(); // 清理 Winsock 库
	return 0;
}
```

注：
1. `winsock2.h`：包含了使用 Winsock API 所需的函数和数据结构的定义
2. `WSAStartup`：初始化 Winsock 库，必须在使用任何 Winsock 函数之前调用。
    - 参数 `MAKEWORD(2, 2)` 表示请求使用 Winsock 2.2 版本
    - 这里的 `WSA` 是 Windows Sockets API 的缩写，`Startup` 表示启动/初始化
    - 如果 `WSAStartup` 返回非零值，表示初始化失败，程序应该处理错误并退出。

**创建一个套接字**

```cpp
	/*
		这里的socket函数用于创建一个套接字，返回一个套接字文件描述符（socket file descriptor）
		套接字文件描述符可以为：
		 - 正整数：表示成功创建的套接字的文件描述符
		 - INVALID_SOCKET：表示套接字创建失败，可以通过调用 WSAGetLastError() 获取具体的错误代码。
	*/
    int sockfd = socket(AF_INET, SOCK_STREAM, 0);
    
	if(sockfd == INVALID_SOCKET)
    {
        std::cerr << "Socket creation failed: " << WSAGetLastError() << std::endl;
        WSACleanup();
        return 1;
    }

	// ... 其他代码 ...

	// 最后记得关闭套接字
	closesocket(sockfd);
```

这里socket函数的参数：

-  domain：地址族，协议族
	- `AF_INET`：IPv4
	- `AF_INET6`：IPv6
-  type：套接字类型
	- `SOCK_STREAM`：字节流，面向连接 → TCP
	- `SOCK_DGRAM`：数据报，不可靠、无连接 → UDP
	- `SOCK_RAW`：原始套接字（抓包/自定义协议，需权限）
- `protocol`：具体协议
	- 通常写 `0`，表示“让系统根据 `domain` + `type` 自动选默认协议”：
	    - `AF_INET` + `SOCK_STREAM` + `0` → TCP
	    - `AF_INET` + `SOCK_DGRAM` + `0` → UDP
	- 也可以显式写：
	    - `IPPROTO_TCP`
	    - `IPPROTO_UDP`

**绑定socket到ip和port**

```cpp
	std::string ip = "127.0.0.1";

    int port = 8080;

    sockaddr_in serverAddr;
    serverAddr.sin_family = AF_INET;
    serverAddr.sin_addr.s_addr = inet_addr(ip.c_str());
    serverAddr.sin_port = htons(port);

    result = bind(sockfd, (sockaddr*)&serverAddr, sizeof(serverAddr));

    if(result == SOCKET_ERROR)
    {
        std::cerr << "Bind failed: " << WSAGetLastError() << std::endl;
        closesocket(sockfd);
        WSACleanup();
        return 1;
    }
    else
    {
        std::cout << "Socket bound successfully." << std::endl;
    }
```

细节如下：
- `sockaddr_in`：Winsock 里“IPv4 地址结构体”的类型
- `sin_family = AF_INET` ：告诉系统这是一份 IPv4 地址
- `serverAddr.sin_addr.s_addr = inet_addr(ip.c_str());`：将字符串的IP地址转化为二进制的IP地址，并存入 `s_addr`
- `serverAddr.sin_port = htons(port);`：存储端口，这里的`htons`是把主机的端口号转换为网络字节序（大端）

**监听连接**

```cpp
	result = listen(sockfd, SOMAXCONN);

    if(result == SOCKET_ERROR)
    {
        std::cerr << "Listen failed: " << WSAGetLastError() << std::endl;
        closesocket(sockfd);
        WSACleanup();
        return 1;
    }
    else
    {
        std::cout << "Listening for connections..." << std::endl;
    }
```

这里的 `listen(int sockfd, int backlog)` 函数用于将套接字设置为监听状态，等待客户端连接。
    - `监听状态`：服务器套接字准备好接受连接请求，但尚未接受任何连接。
    - `sockfd`：之前创建并绑定的套接字描述符。
    - `backlog`：表示允许的最大连接数，通常为 `SOMAXCONN`，表示系统允许的最大连接数。

**接受连接**

```cpp
	sockaddr_in clientAddr;
    int clientAddrLen = sizeof(clientAddr);
    while(true)
    {
		/*
			accept 是一个阻塞函数，直到有客户端连接到来才会返回，因此通常在一个循环里调用 accept 来持续接受连接请求。
		*/
        int clientSockfd = accept(sockfd, (sockaddr*)&clientAddr, &clientAddrLen);

        if(clientSockfd == INVALID_SOCKET)
        {
            std::cerr << "Accept failed: " << WSAGetLastError() << std::endl;
            continue;
        }
        else
        {
            std::cout << "Accepted a connection." << std::endl;
        }

        // ... 处理客户端连接 ...

		/*
			与客户端通信完成后，关闭客户端套接字
			这里的 closesocket 实现了四次挥手中的两次，另外两次由客户端完成
		*/
        closesocket(clientSockfd);
    }
```

注：
1. `accept` 函数用于接受一个连接请求，返回一个新的套接字描述符（clientSockfd），用于与客户端通信。
2. 参数 `clientAddr` 是一个指向 `sockaddr_in` 结构体的指针，用于存储连接客户端的地址信息。
3. 参数 `clientAddrLen` 是一个指向整数的指针，表示 `clientAddr` 结构体的大小，函数调用后会被更新为实际存储的地址信息的大小。
4. 如果 `accept` 返回 `INVALID_SOCKET`，表示接受连接失败，可以通过调用 `WSAGetLastError()` 获取具体的错误代码。
5. 成功接受连接后，可以使用 `clientSockfd` 与客户端进行通信，最后记得关闭 `clientSockfd`。

**发送和接收数据**

以下内容应当在接受连接的循环内，处理客户端连接的部分：
```cpp
	char buffer[1024];
	size_t len = recv(clientSockfd, buffer, sizeof(buffer) - 1, 0);

	if(len > 0)
	{
		buffer[len] = '\0';
		std::cout << "   Received data: " << buffer << std::endl;
	}
	else
	{
		std::cerr << "   Receive failed: " << WSAGetLastError() << std::endl;
	}

	const char* response = "Server received your message.";
	result = send(clientSockfd, response, strlen(response), 0);

	if(result == SOCKET_ERROR)
	{
		std::cerr << "   Send failed: " << WSAGetLastError() << std::endl;
	}
	else
	{
		std::cout << "   Sent response to client." << std::endl;
	}
```

注：
1. `recv` 函数用于从客户端套接字接收数据，返回实际接收的字节数
   - 如果返回值为 `0`，表示连接已关闭；
   - 如果返回值为 `SOCKET_ERROR`，表示接收数据失败，可以通过调用 `WSAGetLastError()` 获取具体的错误代码。
2. `send` 函数用于向客户端套接字发送数据，返回实际发送的字节数，
   - 如果返回值为 `SOCKET_ERROR`，表示发送数据失败，可以通过调用 `WSAGetLastError()` 获取具体的错误代码。
