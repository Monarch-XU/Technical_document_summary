# CMake学习

参考文章：[CMake 保姆级教程（上）（https://subingwen.cn/cmake/CMake-primer/）](https://subingwen.cn/cmake/CMake-primer/)

[CMake 保姆级教程（下）(https://subingwen.cn/cmake/CMake-advanced/)](https://subingwen.cn/cmake/CMake-advanced/)

## CMake使用

### 源文件编译

#### 编写cmake

文件结构：

```shell
$ tree
.
├── add.c
├── div.c
├── head.h
├── main.c
├── mult.c
└── sub.c
```

在上述源文件所在目录下添加一个新文件 CMakeLists.txt，文件内容如下：

```cmake
cmake_minimum_required(VERSION 3.0)
project(CALC)
add_executable(app add.c div.c main.c mult.c sub.c)
```

cmake_minimum_required：指定使用的 cmake 的最低版本。可选，非必须，如果不加可能会有警告

project：定义工程名称，并可指定工程的版本、工程描述、web主页地址、支持的语言（默认情况支持所有语言），如果不需要这些都是可以忽略的，只需要指定出工程名字即可。

```cmake
# PROJECT 指令的语法是：
project(<PROJECT-NAME> [<language-name>...])
project(<PROJECT-NAME>
       [VERSION <major>[.<minor>[.<patch>[.<tweak>]]]]
       [DESCRIPTION <project-description-string>]
       [HOMEPAGE_URL <url-string>]
       [LANGUAGES <language-name>...])
```



add_executable：定义工程会生成一个可执行程序

```cmake
add_executable(可执行程序名 源文件名称)
```

这里的可执行程序名和project中的项目名没有任何关系

源文件名可以是一个也可以是多个，如有多个可用空格或 ";" 间隔



#### cmake编译

```shell
# cmake 命令原型
$ cmake CMakeLists.txt文件所在路径
```

对应的目录下生成了一个makefile文件，此时再执行make命令

```shell
make
```



#### 统一目录编译

要想把编译之类的文件放在一个build目录里，这样做：

```shell
$ mkdir build
$ cd build
$ cmake ..
```



### 定制变量

```cmake
# SET 指令的语法是：
# [] 中的参数为可选项, 如不需要可以不写
SET(VAR [VALUE] [CACHE TYPE DOCSTRING [FORCE]])
# VAR：变量名
# VALUE：变量值
```

实例：

```cmake
set(SRC_LIST add.c  div.c   main.c  mult.c  sub.c)
add_executable(app  ${SRC_LIST})
```

set()就等同于：SRC_LIST = add.c  div.c   main.c  mult.c  sub.c，类似编程语言中的赋值操作

实例：

```cmake
set(HOME /home/robin/Linux/Sort)
set(EXECUTABLE_OUTPUT_PATH ${HOME}/bin
```



### 搜索文件

如果一个项目里边的源文件很多，在编写CMakeLists.txt文件的时候不可能将项目目录的各个文件一一罗列出来，这样太麻烦也不现实。所以，在CMake中为我们提供了搜索文件的命令，可以使用aux_source_directory命令或者file命令。

```cmake
aux_source_directory(< dir > < variable >)
# dir：要搜索的目录
# variable：将从dir目录下搜索到的源文件列表存储到该变量中
```

实例;

```cmake
cmake_minimum_required(VERSION 3.0)
project(CALC)
include_directories(${PROJECT_SOURCE_DIR}/include)
# 搜索 src 目录下的源文件
aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/src SRC_LIST)
add_executable(app  ${SRC_LIST})
```

也可以用file命令

```cmake
file(GLOB/GLOB_RECURSE 变量名 要搜索的文件路径和文件类型)
# GLOB: 将指定目录下搜索到的满足条件的所有文件名生成一个列表，并将其存储到变量中。
# GLOB_RECURSE：递归搜索指定目录，将搜索到的满足条件的文件名生成一个列表，并将其存储到变量中。
```

实例：

```cmake
file(GLOB MAIN_SRC ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)
file(GLOB MAIN_HEAD ${CMAKE_CURRENT_SOURCE_DIR}/include/*.h)
# 可加双引号也可以不加
file(GLOB MAIN_HEAD "${CMAKE_CURRENT_SOURCE_DIR}/src/*.h")
```



### 包含文件夹

将某个文件夹的路径包含，之后就不用使用绝对路径了

```cmake
include_directories(headpath)
```

实例：

```cmake
include_directories(${PROJECT_SOURCE_DIR}/include)
```



### 动/静态库操作

#### 生成静态库

```cmake
add_library(库名称 STATIC 源文件1 [源文件2] ...) 
```

#### 生成动态库

```cmake
add_library(库名称 SHARED 源文件1 [源文件2] ...) 
```

#### 指定输出路径

```cmake
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib) # 适用于动态库
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib) # 都适用
```

对于这种方式来说，其实就是通过set命令给**EXECUTABLE_OUTPUT_PATH**或**LIBRARY_OUTPUT_PATH**宏设置了一个路径，这个路径就是可执行文件生成的路径。


（亲测set(EXECUTABLE_OUTPUT_PATH \${PROJECT_SOURCE_DIR}/lib)有效而set(LIBRARY_OUTPUT_PATH \${PROJECT_SOURCE_DIR}/lib)无效）

#### 链接静态库

现在我们把上面src目录中的add.cpp、div.cpp、mult.cpp、sub.cpp编译成一个静态库文件libcalc.a。

```cmake
link_libraries(<static lib> [<static lib>...])
```

