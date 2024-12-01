# Winsock2

## 服务端

### 必要头文件与库

```c
#include <winsock2.h>	//包含windows.h
#pragma comment(lib , "Ws2_32") //导库
```

```cmake
...
target_include_libraries(${ProjectId} Ws2_32)
```



### 初始化Winsock2库

```c
#include <winsock2.h>	
#pragma comment(lib , "Ws2_32") 

int main(){
    WSADATA wsadata = {0};
    //MAKEWORD(2,2) 指定最佳和最高winsock版本
    if (WSAStartup(MAKEWORD(2,2),&wsadata)){
        printf("WSAStartup failed: %d\n", iResult);
        return 1;
    }
    ...
    WSACleanup();    //释放Winsock资源
}
```



### 创建套接字

```c
SOCKET socketListen = socket(AF_INET,SOCK_STREAM,0);
//AF_INET :IPv4地址格式 (AF_INET6 IPv6地址格式)
//SOCK_STREAM :流式套接字(面向连接，无差错，无重复)
//0 :自动指定与套接字类型匹配的协议

if(socketListen == INVALID_SOCKET){
    MessageBox(NULL,TEXT("套接字创建失败!"),TEXT("socket error"),MB_OK);
    WSACleanup();
    return 1;
}

...
closesocket(socketListen); //关闭套接字资源
```



### 将套接字与IP地址、端口号捆绑

```c
struct sockaddr_in sockAddr;
sockAddr.sin_family = AF_INET;
sockAddr.sin_port = htons(POST);
sockAddr.sin_addr.S_un.S_addr = INADDR_ANY;
//INADDR_ANY :自动在本机所有IP地址上监听(每次连接都不知道它会用哪个地址，建议不用)
//inet_addr("127.0.0.1") 将IP从字符串转换成IPv4格式，127.0.0.1为本机地址


if(bind(socketListen, (SOCKADDR *) &sockAddr, sizeof (sockAddr)) == SOCKET_ERROR){
    MessageBoxA(NULL,TEXT("套接字捆绑IP,端口失败"),TEXT("bind error"),MB_OK); //MessageBoxA是个弹窗
    closesocket(socketListen)
    WSACleanup();
    return 1;
};

//可以使用getsockname(SOCKET s,sockaddr *name,int namelen)获取分配给套接字的地址
```

### 监听端口

```c
//SOMAXCONN 将连接队列的最大长度设置为最大合理值
if(listen(socketListen,SOMAXCONN) == SOCKET_ERROR){
    MessageBoxA(NULL,TEXT("监听端口失败!"),TEXT("Listen error"),MB_OK);
    closesocket(socketListen);
    WSACleanup();
    return 1;
}
```

### 接收连接请求，返回套接字句柄

```c
struct sockaddr_in sockAddrClient;
int nAddrlen = sizeof (sockAddrClient);
while (TRUE){
    SOCKET socketAccept = accept(socketListen,(SOCKADDR*)&sockAddrClient,&nAddrlen);
    if(socketAccept == INVALID_SOCKET){
        continue;
    }
}
```

### 收发数据

```c
//发数据用send()
int send (
    SOCKET s,	//已连接的套接字句柄 _In_	(如果是服务器是socketAccept，客户端是socketClient)
    const char *buf,	//要发送数据的缓冲区指针	_In_
	int len,		//以字节为单位的缓冲区长度	_In_
    int flags		//标志位，设置0就行	_In_
)
//收数据用recv()
int recv (
	SOCKET s,	// _In_
    char *buf,	//要接受数据的缓冲区指针	_Out_
    int len,
    int flag
)    
```

## 客户端

```c
//初始化Winsock2
//创建套接字
//建立连接 
int connect(
	SOCKET s,	//与服务器通信的套接字句柄(客户端就是socketClient)
    const struct sockaddr *name,	//包含服务器IP和端口号的sockaddr_in结构
    int namelen //sockaddr_in结构的长度
)

```

