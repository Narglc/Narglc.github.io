---
title: cmake学习笔记
date: 2019-09-28 21:53:06
tags:
categories:
---

# CMake笔记

## Background
**CMake**意为**cross-platform make**，可用于管理c/c++工程。
CMake**解析**配置文件CMakeLists.txt生成Makefile，相比直接用Makefile管理工程，CMake更灵活和简单。

## 最简单的CMakeLists.txt
```cmake
PROJECT(HELLO)    //更简单，这一句都可以省略!!!
ADD_EXECUTABLE(hello main.c)
```

然后执行`cmake .`,后输入`make`（单线程编译）或者`make -jN`（多线程编译：例如make -j4，即使用4个线程编译）

## 内置变量
变量|说明
---:|---
CMAKE_SOURCE_DIR|
PROJECT_SOURCE_DIR|三条等价
[projectname]_SOURCE_DIR|projectname通过`PROJECT(projectName)`定义
CMAKE_BINARY_DIR|
PROJECT_BINARY_DIR|三条等价
[projectname]_BINARY_DIR|projectname通过`PROJECT(projectName)`定义
CMAKE_CURRENT_SOURCE_DIR|当前处理的 CMakeLists.txt 所在的路径
CMAKE_CURRRENT_BINARY_DIR|如果是 in-source 编译，它跟 CMAKE_CURRENT_SOURCE_DIR 一致;<br>如果是 out-ofsource 编译，他指的是 target 编译目录
CMAKE_CURRENT_LIST_FILE|调用这个变量的 CMakeLists.txt 的完整路径
CMAKE_CURRENT_LIST_LINE|这个变量所在的行
**CMAKE_MODULE_PATH**|定义自己的 cmake 模块所在的路径<br>为了让 cmake 在处理CMakeLists.txt 时找到这些模块，你需要通过 SET 指令，将自己的 cmake 模块路径设置一下:<br>`SET(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)`<br>此时可以通过**INCLUDE**指令来调用自己的模块
EXECUTABLE_OUTPUT_PATH|更改生成的可执行文件路径
LIBRARY_OUTPUT_PATH|更改生成的库文件路径
PROJECT_NAME|返回通过 **PROJECT** 指令定义的项目名称

## 系统信息
- CMAKE_MAJOR_VERSION，CMAKE主版本号，比如2.4.6中的2
- CMAKE_MINOR_VERSION，CMAKE次版本号，比如2.4.6中的4
- CMAKE_PATCH_VERSION，CMAKE补丁等级，比如2.4.6 中的6
- **CMAKE_SYSTEM**，系统名称，比如Linux-2.6.22
- CMAKE_SYSTEM_NAME，不包含版本的系统名，比如Linux
- CMAKE_SYSTEM_VERSION，系统版本，比如2.6.22
- CMAKE_SYSTEM_PROCESSOR，处理器名称，比如i686.
- UNIX，在所有的类UNIX平台为TRUE，包括OS X和cygwin
- WIN32，在所有的win32平台为TRUE，包括cygwin

## 开关选项
变量|说明
---|---
CMAKE_C_FLAGS|设置 C 编译选项，也可以通过指令 **ADD_DEFINITIONS()**添加
CMAKE_CXX_FLAGS|设置 C++编译选项，也可以通过指令 **ADD_DEFINITIONS()**添加

**注:**
```cmake
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fpermissive") 
//g++使用 -fpermissive 参数进行编译，即兼容一些老的语法，但是一些语法错误也会被忽略
```


## 命令
**注意:**
- 参数使用括弧括起，参数之间使用**空格**或**分号**分开。`command(avg1 avg2 ...)`
- **指令是大小写无关的**，<font color=red size=5>**参数和变量是大小写相关的**</font>。但推荐全部使用大写指令。

命令|用例|说明
---|---|---
make **clean**|`make clean`|对构建结果进行**清理**
**list**常用|`list(APPEND <list> <element> [<element> ...])`|将会在该list之后追加若干元素
|`list(REMOVE_ITEM SRC_LIST a.cpp)`|从SRC_LIST中删除a.cpp
**SET**|`set(CODE_DIR ${PROJECT_SOURCE_DIR})`<br>`SET(HELLO_SRC main.c)`<br>`SET(HELLO_SRC "main.c")`|定义变量并赋值,可以接受多个<br>`SET(SRC_LIST main.c t1.c t2.c)`
MESSAGE|`message([SEND_ERROR or STATUS or FATAL_ERROR], “message”)`|类似于printf
${}||变量的引用、取值（IF语句中直接变量名取值）
FILE|`FILE(GLOB_RECURSE SRC_LIST "*.cpp")`|
$ENV{NAME}|`MESSAGE(STATUS “HOME dir: $ENV{HOME}”)`|调用系统的环境变量
SET(ENV{NAME} VAL)||设置环境变量
ADD_DEFINITIONS|`ADD_DEFINITIONS(-DENABLE_DEBUG -DABC)`|向 C/C++编译器添加 **-D** 定义(**相当于添加了#define**)-->添加编译参数
**ADD_DEPENDENCIES**|`ADD_DEPENDENCIES(target-name depend-target1 depend-target2 ...)`|定义 target 依赖的其他 target，确保在编译本 target 之前，其他的 target 已经被构建
**ADD_SUBDIRECTORY**|`ADD_SUBDIRECTORY(source_dir [binary_dir] [EXCLUDE_FROM_ALL])`|向当前工程添加存放源文件的子目录，**并可以指定中间二进制和目标二进制存放的位置**<br>**EXCLUDE_FROM_ALL** 参数的含义是将这个目录从编译过程中排除
ENABLE_TESTING|ENABLE_TESTING()|控制 Makefile 是否构建 test 目标,一般情况这个指令放在工程的主CMakeLists.txt 中.
ADD_TEST|`ADD_TEST(testname Exename arg1 arg2 ...)`|testname 是自定义的 test 名称，Exename 可以是构建的目标文件也可以是外部脚本等等。后面连接传递给可执行文件的参数。如果没有在同一个 CMakeLists.txt 中打开ENABLE_TESTING()指令，任何 ADD_TEST 都是无效的。
AUX_SOURCE_DIRECTORY|`AUX_SOURCE_DIRECTORY(dir SRC_LIST)`|将dir目录下所有源文件列表存储在一个变量SRC_LIST中，被用来自动构建源文件列表
CMAKE_MINIMUM_REQUIRED|`CMAKE_MINIMUM_REQUIRED(VERSION 2.5 FATAL_ERROR)`|如果 cmake 版本小与 2.5，则出现严重错误，整个过程中止
INCLUDE|`INCLUDE(file1 [OPTIONAL])`<br>`INCLUDE(module [OPTIONAL])`|用来载入 CMakeLists.txt 文件，也用于载入预定义的 cmake 模块.<br>**OPTIONAL** 参数的作用是文件**不存在也不会产生错误**
INCLUDE_DIRECTORIES|`INCLUDE_DIRECTORIES(/usr/include/hello)`|用来**向工程添加多个特定的头文件搜索路径**==指定头文件的搜索路径，相当于指定gcc的-I参数
LINK_DIRECTORIES|`LINK_DIRECTORIES(directory1 directory2 ...)`|添加非标准的共享库搜索路径
TARGET_LINK_LIBRARIES|`TARGET_LINK_LIBRARIES(target library1<debug或optimized> library2...)`|为 target 添加需要链接的共享库(动态库、静态库)
ADD_LIBRARY|`ADD_LIBRARY(Hello hello.cxx)`|将hello.cxx编译成静态库如libHello.a


#### ADD\_LIBRARY
```cmake
 ADD_LIBRARY(libname [SHARED|STATIC|MODULE] [EXCLUDE_FROM_ALL] source1 source2 ... sourceN)
```

##### 类型有三种：
 - SHARED 动态库 **后缀为.so** -->生成文件**lib[libname].so**
 - STATIC 静态库 **后缀为.a**  -->生成文件**lib[libname].a**
 - MODULE 在使用 dyld 的系统有效，如果不支持 dyld，则被当作 SHARED 对待

**EXCLUDE\_FROM\_ALL** 参数的意思是这个库不会被默认构建，除非有其他的组件依赖或者手工构建。

```cmake
SET(LIBHELLO_SRC hello.c)
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})   //动态库
ADD_LIBRARY(hello STATIC ${LIBHELLO_SRC})    //静态库
ADD_EXECUTABLE(demo main.c)
TARGET_LINK_LIBRARIES(demo hello)           //hello为一个库
```

#### INCLUDE\_DIRECTORIES
```cmake
INCLUDE_DIRECTORIES([AFTER|BEFORE] [SYSTEM] dir1 dir2 ...)
```

用来**向工程添加多个特定的头文件搜索路径**，路径之间用空格分割，如果路径中包含了空格，可以使用双引号将它括起来，默认的行为是追加到当前的头文件搜索路径的后面。你可以通过两种方式来进行控制搜索路径添加的方式：
 1. **CMAKE_INCLUDE_DIRECTORIES_BEFORE**，通过 SET 这个 cmake 变量为 on，可以将添加的头文件搜索路径放在已有路径的前面。（另一个变量**CMAKE_INCLUDE_DIRECTORIES_BEFORE**也可使用!!）
 2. 通过 AFTER 或者 BEFORE 参数，也可以控制是追加还是置前。


#### ADD\_DEFINITIONS
```cmake
add_definitions(
    -D__SHORT_FILE__=__FILE__
)
```
等价于
```bash
    gcc -D__SHORT_FILE__=__FILE__ main.c -o demo
```
或者
```bash
cmake -D__SHORT__FILE__=__FILE__ main.c -o demo
cmake -DMY_VAR=hello .
```
或者在`main.c`头部添加了
    #define __SHORT_FILE__ __FILE__

**注:** **ADD_DEFINITIONS**本是**添加编译参数**，如：
> **add_definitions(-DDEBUG)** 将在gcc命令行添加DEBUG宏定义；
> **add_definitions( “-Wall -ansi –pedantic –g”)** 添加多个编译参数


#### ENABLE\_TESTING & ADD\_TEST
```cmake
ENABLE_TESTING()
ADD_TEST(mytest ${PROJECT_BINARY_DIR}/BIN/main 2 10)
SET_TESTS_PROPERTIES(mytest PROPERTIES PASS_REGULAR_EXPRESSION "1024")  \\用来测试输出中是否包含后面的字符串
```
#### AUX\_SOURCE\_DIRECTORY
```cmake
AUX_SOURCE_DIRECTORY(. SRC_LIST)
ADD_EXECUTABLE(main ${SRC_LIST})
```

## FILE指令
```cmake
FILE(WRITE filename "message towrite"... )
FILE(APPEND filename "message towrite"... )
FILE(READ filename variable)
FILE(GLOB variable [RELATIVE path][globbing expressions]...)
FILE(GLOB_RECURSE variable [RELATIVE path] [globbingexpressions]...)
FILE(REMOVE [directory]...)
FILE(REMOVE_RECURSE [directory]...)
FILE(MAKE_DIRECTORY [directory]...)
FILE(RELATIVE_PATH variable directory file)
FILE(TO_CMAKE_PATH path result)
FILE(TO_NATIVE_PATH path result)
```

**注：** `FILE(GLOB_RECURSE SRC_LIST "*.cpp")`即将该目录下及所有子文件夹内的++所有++后缀为**.cpp**的文件的路径，全部放入**SRC_LIST**变量中。
```cmake
查找当前目录下所有的源文件并保存到SRC_LIST变量里
aux_source_directory(. SRC_LIST)
查找src目录下所有以cmake开头的文件并保存到CMAKE_FILES变量里
file(GLOB CMAKE_FILES "src/cmake*")
file命令同时支持目录递归查找
file(GLOB_RECURSE CMAKE_FILES "src/cmake*")
```
## IF
```cmake
IF(expression)
    # THEN section.
    COMMAND1(ARGS ...)COMMAND2(ARGS ...)
    ...
ELSE(expression)
    # ELSE section.
    COMMAND1(ARGS ...)
    COMMAND2(ARGS ...)
    ...
ENDIF(expression)
```
表达式的使用方法如下：
```cmake
IF(var)，如果变量不是：空，0，N, NO, OFF, FALSE, NOTFOUND 或
<var>_NOTFOUND 时，表达式为真。
IF(NOT var )，与上述条件相反。
IF(var1 AND var2)，当两个变量都为真是为真。
IF(var1 OR var2)，当两个变量其中一个为真时为真。
IF(COMMAND cmd)，当给定的 cmd 确实是命令并可以调用是为真。
IF(EXISTS dir)或者 IF(EXISTS file)，当目录名或者文件名存在时为真。
IF(file1 IS_NEWER_THAN file2)，当 file1 比 file2 新，或者 file1/file2 其
中有一个不存在时为真，文件名请使用完整路径。
IF(IS_DIRECTORY dirname)，当 dirname 是目录时，为真。
IF(variable MATCHES regex)
IF(string MATCHES regex)
```
当给定的变量或者字符串能够匹配正则表达式 regex 时为真。比如：
```cmake
IF("hello" MATCHES "ell")
    MESSAGE("true")
ENDIF("hello" MATCHES "ell")IF(variable LESS number)

IF(string LESS number)
IF(variable GREATER number)
IF(string GREATER number)
IF(variable EQUAL number)
IF(string EQUAL number)
```
数字比较表达式
```cmake
IF(variable STRLESS string)
IF(string STRLESS string)
IF(variable STRGREATER string)
IF(string STRGREATER string)
IF(variable STREQUAL string)
IF(string STREQUAL string)
```
按照字母序的排列进行比较.
```cmake
    IF(DEFINED variable)，如果变量被定义，为真。
```
一个小例子，用来判断平台差异：
```cmake
IF(WIN32)
       MESSAGE(STATUS “This is windows.”)
    #作一些 Windows 相关的操作
ELSE(WIN32)   //容易引起歧义
    MESSAGE(STATUS “This is not windows”)
    #作一些非 Windows 相关的操作
ENDIF(WIN32)
```
或者配合`SET(CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS ON)`使用后，上述语句可简化为如下形式：
```cmake
IF(WIN32)
ELSE()
ENDIF()
```
如果配合 ELSEIF 使用，可能的写法是这样:
```cmake
IF(WIN32)
    #do something related to WIN32
ELSEIF(UNIX)
    #do something related to UNIX
ELSEIF(APPLE)
    #do something related to APPLE
ENDIF(WIN32)
```
## WHILE
```cmake
WHILE(condition)
    COMMAND1(ARGS ...)
    COMMAND2(ARGS ...)
    ...
ENDWHILE(condition)
```

## FOREACH
 1. 列表
```cmake
    AUX_SOURCE_DIRECTORY(. SRC_LIST)
    FOREACH(F ${SRC_LIST})
    MESSAGE(${F})
    ENDFOREACH(F)
```
 2. 范围
```cmake
    FOREACH(VAR RANGE 10)  //从0到10以1步进
    MESSAGE(${VAR})
    ENDFOREACH(VAR)
```
 3. 范围和步进
```cmake
    FOREACH(A RANGE 5 15 3)  //从5开始到15结束，以3为步进
    MESSAGE(${A})
    ENDFOREACH(A)
```
**注:**<font color=red>直到遇到**ENDFOREACH**指令，整个语句块才会得到真正的执行。</font>


## 内部构建 vs 外部构建
构建分为**内部构建(in-sourcebuild)**和**外部构建(out-of-source build)**。
**外部构建**一个最大的好处是，**对于原有的工程没有任何影响，所有动作全部发生在编译目录**。而**cmake强烈推荐的是_外部构建_**。


|——build
|——CMakeLists.txt
|——CMakeLists.txt.bak
|____main.c

根目录下包含源文件*.c、CMakeList.txt。然后创建**build**文件夹，并`cd build`下，再执行`cmake ..`，再`make -j4`.

**注:** 通过外部编译进行工程构建，**HELLO_SOURCE_DIR**仍然指代工程路径，即`/backup/cmake/t1`;而 **HELLO_BINARY_DIR** 则指代编译路径，即`/backup/cmake/t1/build`.


## FAQ

1）  怎样获得一个目录下的所有源文件

    >>  aux_source_directory(<dir> <variable>)
    >>  将dir中所有源文件（不包括头文件）保存到变量variable中，然后可以add_executable (ss7gw ${variable})这样使用。

2）  怎样指定项目编译目标
    >>  project命令指定

3）  怎样添加动态库和静态库
    >> target_link_libraries命令添加即可

4）  怎样在执行CMAKE时打印消息
    >> message([SEND_ERROR | STATUS | FATAL_ERROR] "message to display" ...)
    >> 注意大小写

5）  怎样指定头文件与库文件路径
    >> include_directories与link_directories
    >>可以多次调用以设置多个路径
    >> link_directories仅对其后面的targets起作用

6）  怎样区分debug、release版本
    >>建立debug/release两目录，分别在其中执行cmake -DCMAKE_BUILD_TYPE=Debug（或Release），需要编译不同版本时进入不同目录执行make即可；
    Debug版会使用参数-g；Release版使用-O3 –DNDEBUG
    >> 另一种设置方法——例如DEBUG版设置编译参数DDEBUG

    IF(DEBUG_mode)
        add_definitions(-DDEBUG)
    ENDIF()
    在执行cmake时增加参数即可，例如cmake -D DEBUG_mode=ON

7）  怎样设置条件编译
例如debug版设置编译选项DEBUG，并且更改不应改变CMakelist.txt

    >> 使用option command，eg：
   
    option(DEBUG_mode "ON for debug or OFF for release" ON)
    IF(DEBUG_mode)
        add_definitions(-DDEBUG)
    ENDIF()
   
    >> 使其生效的方法：首先cmake生成makefile，然后make edit_cache编辑编译选项；Linux下会打开一个文本框，可以更改，该完后再make生成目标文件——emacs不支持make edit_cache；
    >> 局限：这种方法不能直接设置生成的makefile，而是必须使用命令在make前设置参数；对于debug、release版本，相当于需要两个目录，分别先cmake一次，然后分别make edit_cache一次；
    >> 期望的效果：在执行cmake时直接通过参数指定一个开关项，生成相应的makefile——可以这样做，例如cmake –DDEBUGVERSION=ON

8）  怎样添加编译宏定义
    >> 使用add_definitions命令，见命令部分说明

9）  怎样添加编译依赖项
    用于确保编译目标项目前依赖项必须先构建好
    >>add_dependencies

10） 怎样指定目标文件目录
    >> 建立一个新的目录，在该目录中执行cmake生成Makefile文件，这样编译结果会保存在该目录——类似
    >> SET_TARGET_PROPERTIES(ss7gw PROPERTIES
                          RUNTIME_OUTPUT_DIRECTORY "${BIN_DIR}")

11） 很多文件夹，难道需要把每个文件夹编译成一个库文件？
    >> 可以不在子目录中使用CMakeList.txt，直接在上层目录中指定子目录

12）        怎样设定依赖的cmake版本
    >> cmake_minimum_required(VERSION 2.6)

13）        相对路径怎么指定
    >> ${projectname_SOURCE_DIR}表示根源文件目录，${ projectname _BINARY_DIR}表示根二进制文件目录？

14）        怎样设置编译中间文件的目录
    >> TBD

15）        怎样在IF语句中使用字串或数字比较
    >>数字比较LESS、GREATER、EQUAL，字串比STRLESS、STRGREATER、STREQUAL，
    >> Eg：

    set(CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS ON)
    set(AAA abc)
    IF(AAA STREQUAL abc)
        message(STATUS "true")   #应该打印true
    ENDIF()

16）        更改h文件时是否只编译必须的cpp文件
    >> 是

17）        机器上安装了VC7和VC8，CMAKE会自动搜索编译器，但是怎样指定某个版本？
    >> TBD

18）        怎样根据OS指定编译选项
    >> IF( APPLE ); IF( UNIX ); IF( WIN32 )

19）        能否自动执行某些编译前、后命令？
    >> 可以，TBD

20）        怎样打印make的输出
    >> make VERBOSE=1  #看到 make 构建的详细过程



- - -

## 还未吃透！！！

###包含目录和链接目录
将./include和./abc加入包含目录列表

    include_directories(
        ./include
        ./abc
    )
将./lib加入编译器链接阶段的搜索目录列表

    link_directories(
        ./lib
    )

### 添加生成目标
使用SRC_LIST源文件列表里的文件生成一个可执行文件hello

    add_executable(hello ${SRC_LIST})
使用SRC_LIST源文件列表里的文件生成一个静态链接库libhello.a

    add_library(hello STATIC ${SRC_LIST})
使用SRC_LIST源文件列表里的文件生成一个动态链接库libhello.so

    add_library(hello SHARED ${SRC_LIST})
将若干库文件链接到生成的目标hello(libhello.a或libhello.so)

    target_link_libraries(hello A B.a C.so)
需要注意的是，target_link_libraries里库文件的顺序符合gcc链接顺序的规则，即被依赖的库放在依赖它的库的后面，比如上面的命令里，libA.so可能依赖于libB.a和libC.so，如果顺序有错，链接时会报错。还有一点，B.a会告诉CMake优先使用静态链接库libB.a，C.so会告诉CMake优先使用动态链接库libC.so，也可直接使用库文件的相对路径或绝对路径。使用绝对路径的好处在于，当依赖的库被更新时，make的时候也会重新链接。