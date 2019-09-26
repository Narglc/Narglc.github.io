---
title: 'C++ 服务器编程开发'
date: 2019-09-04 20:36:53
tags: 
	- C++
	- 网络编程
	- 服务器编程
categories: Coding
---
# Day 01
## 效率
**开发效率**： 
C++11 -> auto、lambda、move
同步、异步、多线程、多进程...
**运行效率**

## 预备知识
1. TCP/IP （网络编程都是此种协议）
	- TCP -> 传输控制协议
	- IP	-> 网际协议
2. Socket
3. C++
	- 指针、引用、地址、循环、函数、类...
	- std::shared_ptr、std::thread、std::aync、decltype、std::future、right、reference
4. 第三方库
   - BOOST

**问题**：

``` cpp
// 反转链表
#include<algorithm>

using namespace std;
struct Node{
  int* next;
  int value;
}

void pirntNodeList(Node* n){
  while(n)
  {
  	cout << n->value << " ";
    n = n->next;
  }
  cout << "\n";
}

Node* reverseList(Node* node)
{
  
  return 0;
}

void quizOne()
{
  Node* n = 0;
  for(int i = 0; i<3; i++)
  {
    n->next = Node(i);
  }
}
```

## IP网际协议详解

> IP = Internet protocol suite

### OSI模型（Open System Interconnection model）
![](/images/OSI_model.jpg)
<font color="red"><b>注意:</b></font> 七个层级，过于复杂。

### IP模型
![](/images/IP_model.jpg)
#### 数据进入协议栈的封装
![](/images/UDP_协议栈封装.jpg)
<font color="red"><b>注意:</b></font> **Frame data** 长度有要求，最小46字节，不足添0；最大1500个字节。
> MTU（最大传输单元），当`传输数据 > MTU`时，则在IP层需要拆分成小包。 
#### IP特点
- 不可靠（unreliable）
- 无连接（connectionless）

#### IP数据报格式首部字段
![](/images/IP_telegraph.jpg)
<font color="red"><b>注意:【小小】</b></font> 网络数据都是按照**Big Endian【大端存储】**传送的；家用操作系统都是**Littele Endian**数据存储。 
![Big_Small_Endian.jpg](/images/Big_Small_Endian.jpg)
**思考题**：
``` cpp
static bool isLittleEndianSystem(){

}
```

## TCP详解
### TCP如何利用IP
- IP的特点
- TCP将应用程序的传输数据分割成合适的数据块
- 定时器
- 延迟确认
- 检验和CRC
- 流量控制

### TCP首部
![](/images/TCP_header.jpg)
<font color="red"><b>注意:</b></font> IP首部20Byte，TCP首部20Byte，如需在网络传输，还需填充6Byte。
> 所谓的`Socket` , 即`ip+port`
> > ip 来自 IP首部
> > port 来自 TCP首部

- `Sequence Number` 用于标记`定时器`对那一数据包计时。
- `Acknowlegment Number` 与`延迟确认`有关 -> if `ACK` set
- `Checksum` 与 `检验和` 有关
- `Data offset` -- 数据长度
- `URG` -> 数据紧急标志位
- `ACK` -> 数据确认标识位
- `PSH` -> push
- `RST` -> reset
- `SYN` -> 建立时需要
- `FIN` -> 结束

### TCP状态
![](/images/TCP_states.jpg)

### TPC状态变迁
![](/images/TCP_state_change.jpg)
> 蓝线--server；棕线--client；虚线--异常

### TCP的连接（三次握手）
![](/images/TCP_three_shakehand.jpg)

### TCP连接的断开（四次握手）
![](/images/TCP_four_shakehands.jpg)
> **1.为什么断开要四次握手？**
> > 全双工,两边都需要确认
> > 主动断开方，会进入`TIME_WAIT`状态

### TCP数据相互传送
- 交互式(小数据)与成块的数据两种
- 时间延迟确认
- Nagle算法\[小数据积攒后再发送\](游戏开发一般关闭这个算法)
- 接收窗口大小

### TCP内部使用的定时器
- 重传定时器
- 坚持定时器(Persist)
- 保活定时器(KeepAlive)<建议不开启>
- 2MSL定时器(TIME_WAIT)

## 实战
### Wireshark使用
> 需安装WinCap方能使用
#### 筛选器
`ip.addr==115.239.210.27 && ip.port==80`

## Socket API (Berkeley sockets, BSD Socket)
### 头文件
![](/images/Socket_header.jpg)
### API函数
![](/images/API_func.jpg)
![](/images/API_func2.jpg)
  
## 服务器和客户端的例子
### TCP Socket 基本流程图
![](/images/TCP_Socket_flow_diagram.jpg)
### Code：
#### Server_main.cpp
```cpp
#include<sys/types.h>
#include<sys/socket.h>
#include<netinet/in.h>
#include<arpa/inet.h>

#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<unistd.h>

int main(int argc, char** argv)
{
  char hello[] = "hello world";
  struct sockaddr_in sa;
  int SocketFD = socket(PF_INET, SOCK_STREAM, IPPROTO_TCP);

  if(-1 == SocketFD)
  {
    perror("cannot create socket");
    exit(EXIT_FAILURE);
  }

  memset(&sa, 0, sizeof sa);

  sa.sin_family = AF_INET;
  sa.sin_port = htons(2222);
  sa.sin_addr.s_addr = htonl(INADDR_ANY);

  if(-1 == bind(SocketFD, (struct sockaddr*)&sa, sizeof sa))
  {
    perror("bind failed");
    close(SocketFD);
    exit(EXIT_FAILURE);
  }

  if(-1 == listen(SocketFD, 10))
  {
    perror("listen failed");
    close(SocketFD);
    exit(EXIT_FAILURE);
  }

  for(;;)
  {
    int ConnectFD = accept(SocketFD, NULL, NULL);

    if(0 > ConnectFD)
    {
      perror("accept failed");
      close(SocketFD);
      exit(EXIT_FAILURE);
    }

    int writeSize = 0;
    size_t totalWrite = 0;
    while(totalWrite < sizeof(hello))
    {
      writeSize = 
        write(ConnectFD, hello + totalWrite, sizeof(hello) - totalWrite);
      if(-1 == writeSize)
      {
        perror("write failed");
        close(ConnectFD);
        close(SocketFD);
        exit(EXIT_FAILURE);
      }
      totalWrite += writeSize;
    }

    if(-1 == shutdown(ConnectFD, SHUT_RDWR))
    {
      perror("shutdown failed");
      close(ConnectFD);
      close(SocketFD);
      exit(EXIT_FAILURE);
    }
    close(ConnectFD);
  }
  close(SocketFD);
  return EXIT_SUCCESS; 
}
```
#### Client_main.cpp
```cpp
#include<sys/types.h>
#include<sys/socket.h>
#include<netinet/in.h>
#include<arpa/inet.h>

#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<unistd.h>

int main(int argc, char** argv)
{
  struct sockaddr_in sa;
  int res;
  int SocketFD = socket(PF_INET, SOCK_STREAM, IPPROTO_TCP);

  if(-1 == SocketFD)
  {
    perror("cannot create socket");
    exit(EXIT_FAILURE);
  }

  memset(&sa, 0, sizeof sa);

  sa.sin_family = AF_INET;
  sa.sin_port = htons(2222);
  res = inet_pton(AF_INET, "127.0.0.1", &sa.sin_addr);

  if(-1 == connect(SocketFD, (struct sockaddr*)&sa, sizeof sa))
  {
    perror("connect failed");
    close(SocketFD);
    exit(EXIT_FAILURE);
  }

  char buffer[512];
  int totalRead = 0;
  for(;;)
  {
    int readSize = 0;
    readSize = read(SocketFD, buffer + totalRead, sizeof(buffer)-totalRead);
    if(readSize == 0)
    {
      break;
    }
    else if(readSize == -1)
    {
      perror("read failed");
      close(SocketFD);
      exit(EXIT_FAILURE);
    }
    totalRead += readSize;
  }
  buffer[totalRead] = 0;
  printf("get from server: %s\n",buffer);

  (void)shutdown(SocketFD, SHUT_RDWR);

  close(SocketFD);
  return EXIT_SUCCESS;
}
```











