
需要预先学习：
- [[服务端-Server]]

**包含库文件，初始化该库，并创建套接字**

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

    int sockfd = socket(AF_INET, SOCK_STREAM, 0);

    if(sockfd == INVALID_SOCKET)
    {
        std::cerr << "Socket creation failed: " << WSAGetLastError() << std::endl;
        WSACleanup();
        return 1;
    }
    else 
    {
        std::cout << "Socket created successfully: " << sockfd << std::endl;
    }

    // ... 其他代码 ...

    closesocket(sockfd); // 关闭套接字
    WSACleanup(); // 清理 Winsock 库
    return 0;
}
```

**连接到服务器**

```cpp
    std::string serverIp = "127.0.0.1";
    int serverPort = 8080;

    sockaddr_in serverAddr;
    serverAddr.sin_family = AF_INET;
    serverAddr.sin_addr.s_addr = inet_addr(serverIp.c_str());
    serverAddr.sin_port = htons(serverPort);

    result = connect(sockfd, (sockaddr*)&serverAddr, sizeof(serverAddr));

    if(result == SOCKET_ERROR)
    {
        std::cerr << "Connection failed: " << WSAGetLastError() << std::endl;
        closesocket(sockfd);
        WSACleanup();
        return 1;
    }
    else
    {
        std::cout << "Connected to server successfully." << std::endl;
    }
```

**发送和接收数据**

```cpp
    const char* message = "Hello, Server!";
    result = send(sockfd, message, strlen(message), 0);

    if(result == SOCKET_ERROR)
    {
        std::cerr << "Send failed: " << WSAGetLastError() << std::endl;
        closesocket(sockfd);
        WSACleanup();
        return 1;
    }
    else
    {
        std::cout << "Message sent successfully." << std::endl;
    }

    char buffer[1024];
    result = recv(sockfd, buffer, sizeof(buffer), 0);

    if(result > 0)
    {
        std::cout << "Received from server: " << std::string(buffer, result) << std::endl;
    }
    else if(result == 0)
    {
        std::cout << "Connection closed by server." << std::endl;
    }
    else
    {
        std::cerr << "Receive failed: " << WSAGetLastError() << std::endl;
        closesocket(sockfd);
        WSACleanup();
        return 1;
    }
```