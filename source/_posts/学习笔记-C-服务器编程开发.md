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





