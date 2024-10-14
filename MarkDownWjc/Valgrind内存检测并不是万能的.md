# Valgrind内存检测
 在使用valgrind之前我想先说一下，你使用cppcheck了吗？ 或者说你是怎么掉到内存泄露这个泥潭里面的，毕竟CPP有智能指针
 ![](vx_images/257561924677596.png =307x)
 
Valgrind的试用相对是简单的，需要在对应平台安装 valgrind , 在嵌入式平台通常sdk里面会带有这个功能选项，不然就需要自己手动移植了。


有以下代码
```cpp
#include <iostream>
#include <string>
#include <vector> 
#include <functional>
#include <memory>
#include <stdlib.h> 
#include <string.h> 
#include <cstring>
#include <stdexcept>
#include <chrono>   
#include <thread> 

using namespace std;    

int main() 
{

    char*p  = static_cast<char*>(malloc(20*sizeof(char)));
    strcpy(p,"hello");  
    cout<<p<<endl;  


    char buf[10]={0};  //valgrind 没有检测到这里出现了越界访问
    buf[10]=0x32;  

    std::thread t([]{ 
        while(1)
        {
        cout << "Hello world!" << endl;
        char*p = static_cast<char*>(malloc(0*sizeof(char)));
        this_thread::sleep_for(chrono::seconds(10)); 
        }
     });  
    t.join();  
     return 0;   
}  
// valgrind --tool=memcheck --show-leak-kinds=all  --leak-check=full ./hi
// g++ main.cpp -o hi -g -lpthread
```
valgrind对应的输出，我曾在想为什么等到程序结束才有输出而不是运行时候实时监测内存泄露，但是转头一想，谁知道你在程序退出时候会不会释放。所以等待程序结束、崩溃再输出 也是合理的

> **valgrind --tool=memcheck --show-leak-kinds=all  --leak-check=full ./hi **


> ==737474== Memcheck, a memory error detector
> ==737474== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
> ==737474== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info
> ==737474== Command: ./hi
> ==737474== 
> hello
> Hello world!
> Hello world!
> ^C==737474== 
> ==737474== Process terminating with default action of signal 2 (SIGINT)
> ==737474==    at 0x487ACD7: __pthread_clockjoin_ex (pthread_join_common.c:145)
> ==737474==    by 0x496B056: std::thread::join() (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.28)
> ==737474==    by 0x109415: main (main.cpp:34)
> ==737474== 
> ==737474== HEAP SUMMARY:
> ==737474==     in use at exit: 324 bytes in 5 blocks
> ==737474==   total heap usage: 7 allocs, 2 frees, 74,052 bytes allocated
> ==737474== 
> ==737474== 0 bytes in 1 blocks are still reachable in loss record 1 of 5
> ==737474==    at 0x483B7F3: malloc (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)
> ==737474==    by 0x109356: main::{lambda()#1}::operator()() const (main.cpp:30)
> ==737474==    by 0x10981F: void std::__invoke_impl<void, main::{lambda()#1}>(std::__invoke_other, main::{lambda()#1}&&) (invoke.h:60)
> ==737474==    by 0x1097D4: std::__invoke_result<main::{lambda()#1}>::type std::__invoke<main::{lambda()#1}>(std::__invoke_result&&, (main::{lambda()#1}&&)...) (invoke.h:95)
> ==737474==    by 0x109781: void std::thread::_Invoker<std::tuple<main::{lambda()#1}> >::_M_invoke<0ul>(std::_Index_tuple<0ul>) (thread:264)
> ==737474==    by 0x109755: std::thread::_Invoker<std::tuple<main::{lambda()#1}> >::operator()() (thread:271)
> ==737474==    by 0x109739: std::thread::_State_impl<std::thread::_Invoker<std::tuple<main::{lambda()#1}> > >::_M_run() (thread:215)
> ==737474==    by 0x496ADF3: ??? (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.28)
> ==737474==    by 0x4879608: start_thread (pthread_create.c:477)
> ==737474==    by 0x4BB0352: clone (clone.S:95)
> ==737474== 
> ==737474== 0 bytes in 1 blocks are definitely lost in loss record 2 of 5
> ==737474==    at 0x483B7F3: malloc (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)
> ==737474==    by 0x109356: main::{lambda()#1}::operator()() const (main.cpp:30)
> ==737474==    by 0x10981F: void std::__invoke_impl<void, main::{lambda()#1}>(std::__invoke_other, main::{lambda()#1}&&) (invoke.h:60)
> ==737474==    by 0x1097D4: std::__invoke_result<main::{lambda()#1}>::type std::__invoke<main::{lambda()#1}>(std::__invoke_result&&, (main::{lambda()#1}&&)...) (invoke.h:95)
> ==737474==    by 0x109781: void std::thread::_Invoker<std::tuple<main::{lambda()#1}> >::_M_invoke<0ul>(std::_Index_tuple<0ul>) (thread:264)
> ==737474==    by 0x109755: std::thread::_Invoker<std::tuple<main::{lambda()#1}> >::operator()() (thread:271)
> ==737474==    by 0x109739: std::thread::_State_impl<std::thread::_Invoker<std::tuple<main::{lambda()#1}> > >::_M_run() (thread:215)
> ==737474==    by 0x496ADF3: ??? (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.28)
> ==737474==    by 0x4879608: start_thread (pthread_create.c:477)
> ==737474==    by 0x4BB0352: clone (clone.S:95)
> ==737474== 
> ==737474== 16 bytes in 1 blocks are still reachable in loss record 3 of 5
> ==737474==    at 0x483BE63: operator new(unsigned long) (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)
> ==737474==    by 0x10954E: std::unique_ptr<std::thread::_State, std::default_delete<std::unique_ptr> > std::thread::_S_make_state<std::thread::_Invoker<std::tuple<main::{lambda()#1}> >, {lambda()#1}>({lambda()#1}&&) (thread:226)
> ==737474==    by 0x1094B8: std::thread::thread<main::{lambda()#1}, , void>(main::{lambda()#1}&&) (thread:149)
> ==737474==    by 0x109409: main (main.cpp:33)
> ==737474== 
> ==737474== 20 bytes in 1 blocks are still reachable in loss record 4 of 5
> ==737474==    at 0x483B7F3: malloc (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)
> ==737474==    by 0x1093A8: main (main.cpp:18)
> ==737474== 
> ==737474== 288 bytes in 1 blocks are possibly lost in loss record 5 of 5
> ==737474==    at 0x483DD99: calloc (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)
> ==737474==    by 0x40149DA: allocate_dtv (dl-tls.c:286)
> ==737474==    by 0x40149DA: _dl_allocate_tls (dl-tls.c:532)
> ==737474==    by 0x487A322: allocate_stack (allocatestack.c:622)
> ==737474==    by 0x487A322: pthread_create@@GLIBC_2.2.5 (pthread_create.c:660)
> ==737474==    by 0x496B0C9: std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.28)
> ==737474==    by 0x1094CF: std::thread::thread<main::{lambda()#1}, , void>(main::{lambda()#1}&&) (thread:149)
> ==737474==    by 0x109409: main (main.cpp:33)
> ==737474== 
> ==737474== LEAK SUMMARY:
> ==737474==    definitely lost: 0 bytes in 1 blocks
> ==737474==    indirectly lost: 0 bytes in 0 blocks
> ==737474==      possibly lost: 288 bytes in 1 blocks
> ==737474==    still reachable: 36 bytes in 3 blocks
> ==737474==         suppressed: 0 bytes in 0 blocks
> ==737474== 
> ==737474== For lists of detected and suppressed errors, rerun with: -s
> ==737474== ERROR SUMMARY: 2 errors from 2 contexts (suppressed: 0 from 0)


valgrind --tool=memcheck --show-leak-kinds=all  --leak-check=full ./hi  
valgrind 编译时候只需要加个 -g 然后，使用   --tool=memcheck --show-leak-kinds=all  --leak-check=full 选项就可以使用。
除了memcheck还有其他几个工具 ，内存检测是主要的
[manual-intro.html](https://valgrind.org/docs/manual/manual-intro.html) 
然后在程序结束时候他会给你他的检查结果。 valgrind是模拟一个cpu 运行你的代码，大型项目会很影响性能，这个能理解。毕竟我觉得如果发现了内存泄漏大致的方向，可以用valgrind 大方向上检测。但是他没有检测到我的 越界访问让我有点意外。
于是我换了一个更常用的工具  AddressSanitizer  阿三。 asan

编译时候加入参数  g++ main.cpp -o hi -g -fsanitize=address -fsanitize=leak -pthread

> ./test_asan 
> hello
> =================================================================
> ==766110==ERROR: AddressSanitizer: stack-buffer-overflow on address 0x7ffcf23c002a at pc 0x5643282eb843 bp 0x7ffcf23bffa0 sp 0x7ffcf23bff90
> WRITE of size 1 at 0x7ffcf23c002a thread T0
>     #0 0x5643282eb842 in main /home/wjc/WorkSpace/TestValgriad/main.cpp:24
>     #1 0x7fb727f16082 in __libc_start_main ../csu/libc-start.c:308
>     #2 0x5643282eb46d in _start (/home/wjc/WorkSpace/TestValgriad/test_asan+0x246d)
> 
> Address 0x7ffcf23c002a is located in stack of thread T0 at offset 106 in frame
>     #0 0x5643282eb6a6 in main /home/wjc/WorkSpace/TestValgriad/main.cpp:16
> 
>   This frame has 3 object(s):
>     [48, 49) '<unknown>'
>     [64, 72) 't' (line 26)
>     [96, 106) 'buf' (line 23) <== Memory access at offset 106 overflows this variable
> HINT: this may be a false positive if your program uses some custom stack unwind mechanism, swapcontext or vfork
>       (longjmp and C++ exceptions *are* supported)
> SUMMARY: AddressSanitizer: stack-buffer-overflow /home/wjc/WorkSpace/TestValgriad/main.cpp:24 in main
> Shadow bytes around the buggy address:
>   0x10001e46ffb0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
>   0x10001e46ffc0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
>   0x10001e46ffd0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
>   0x10001e46ffe0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
>   0x10001e46fff0: 00 00 00 00 00 00 00 00 f1 f1 f1 f1 f1 f1 01 f2
> =>0x10001e470000: 00 f2 f2 f2 00[02]f3 f3 00 00 00 00 00 00 00 00
>   0x10001e470010: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
>   0x10001e470020: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
>   0x10001e470030: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
>   0x10001e470040: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
>   0x10001e470050: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
> Shadow byte legend (one shadow byte represents 8 application bytes):
>   Addressable:           00
>   Partially addressable: 01 02 03 04 05 06 07 
>   Heap left redzone:       fa
>   Freed heap region:       fd
>   Stack left redzone:      f1
>   Stack mid redzone:       f2
>   Stack right redzone:     f3
>   Stack after return:      f5
>   Stack use after scope:   f8
>   Global redzone:          f9
>   Global init order:       f6
>   Poisoned by user:        f7
>   Container overflow:      fc
>   Array cookie:            ac
>   Intra object redzone:    bb
>   ASan internal:           fe
>   Left alloca redzone:     ca
>   Right alloca redzone:    cb
>   Shadow gap:              cc
> ==766110==ABORTING

他检测到了我的内存越界访问 ，但是却没有检测到我的内存泄露 啊
难道是鱼和熊掌不可兼得？
![](vx_images/303052923442948.png =928x)

使用cppcheck  + valgrind 看来是个不错的组合  
![](vx_images/89722567037452.png =716x)

现在有一个更为轻量的工具  dmalloc ,让我们来试一下，我在busybox 中也确实发现了它

安装和使用 参考 
点击链接查看和 Kimi 智能助手的对话 https://kimi.moonshot.cn/share/cs6bh9rmvq8n5qfs757g

测试代码
```cpp

//测试一下dmalloc

#include <iostream>
#ifdef DMALLOC
#include "dmalloc.h"
#endif

int main() {
    int *array = (int *)malloc(10 * sizeof(int)); // 分配内存

    if (array == NULL) {
        std::cerr << "Memory allocation failed." << std::endl;
        return 1;
    }

    // 初始化数组
    for (int i = 0; i < 10; i++) {
        array[i] = i;
    }

    // 打印数组
    for (int i = 0; i < 10; i++) {
        std::cout << "Element " << i << ": " << array[i] << std::endl;
    }

    char buf[10]; // 缓冲区溢出，dmalloc 能不能检测？
    buf[10] = 23;   

    //free(array); // 释放内存

#ifdef DMALLOC
    dmalloc_shutdown(); // 关闭dmalloc并输出日志
#endif

    return 0;
}
//g++ -o test_dmalloc main.cpp -DDMALLOC -L/usr/local/lib -ldmalloc
//export LD_LIBRARY_PATH=/opt/dmalloc/lib:$LD_LIBRARY_PATH
//export DMALLOC_OPTIONS="debug=0x4f4e503,log=dmalloc.log"
//./test_dmalloc

```
![](vx_images/194482504266209.png =466x)

比valgrind 轻便但是同样检测不出来 内存的越界访问。  但是如果移植 valgrind 太麻烦不妨试一下  dmalloc

 
**静态分析 + 动态检查**