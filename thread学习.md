# thread学习

进程就是运行中的程序
线程就是进程中的进程

## cmake设置

需要添加这几行：

```cmake
set(THREADS_PREFER_PTHREAD_FLAG ON) # 使用线程时优先考虑pthread
find_package(Threads REQUIRED)
target_link_libraries(pthread_practise Threads::Threads)
```



## 线程锁

`std::mutex m1`: m1是一个线程锁变量，对其操作有`m1.lock()`和`m1.unlock()`。实际上，线程锁表达的是一种“占有”的概念。也就是说，当某一线程“占有”该锁时，另一线程就无法占有该锁，如：

```c++
#include <iostream>
#include <thread>
#include <mutex>
using namespace std;
std::mutex m1,m2;
void func_1(){
    m1.lock();
    cout<<"f1 m1 lock"<<endl;
}

void func_2(){
    m1.lock();
    cout<<"f2 m1 lock"<<endl;
    m1.unlock();
    cout<<"f2 m1 unlock"<<endl;
}

int main() {
    std::thread t1(func_1);
    std::thread t2(func_2);
    t1.join();
    t2.join();
std::cout<<"over"<<std::endl;
    return 0;
}
```

当线程t1执行`m1.lock()`时，线程t2就无法再进行`m1.lock()`，因为线程锁m1已经被线程t1占有。m2要等t1执行`m1.unlock()`后才能执行`m1.lock()`。总的来说，只有在当前占有线程锁的线程能够运行。

为了方便，可以直接使用`lock_guard`函数来实现局部作用域中的自动上锁解锁

```c++
std::lock_guard<std::mutex> lg(mtx)
```



## call_once

当多个线程调用带有这个函数的函数时，该函数只执行一次

```c++
std::once_flag flag1;
void pt_error(){
    std::call_once(flag1, [](){ std::cout << "Simple example: called once\n"; });
}
int main() {
    std::thread t1(pt_error);
    std::thread t2(pt_error);
    t1.join();
    t2.join();
    return 0;
}
//结果：Simple example: called once
```

结果只输出了一次“Simple example: called once”