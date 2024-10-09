# 容不得沙子explicit

explicit 主要是用来防止发生隐式转换， 比如 出现  A a =5; 这种代码； 比如下面就发生了 从  5 转换到  float 类型的转换。
如果你在构造函数显示使用了 explicit 那他就得老老实实地   A a(5); 捉着   A a = new A(6);

Qt 里面经常看到 explicit 估计是为了防止  QClass  objecta = 0;?


```cpp
 #include <iostream>
#include <cstring> // 使用 memcpy 和 strlen
#include <string>
#include "TestClass.h"  
#include "TestNameSpace.h"
 
class Ta
{
private:
    float a=11; //C++11中可以在类内初始化
public:
    // explicit Ta(int i):a(i)
    // {
    //     std::cout << "Ta construct" << std::endl;
    // }

    Ta(float i):a(i) //如果不加explicit关键字，那么这个构造函数可以隐式转换 Ta ta = 10;
    {
        std::cout << "Ta construct" << std::endl;
    }
    ~Ta();
    void Hello()
    {
        std::cout << "Hello from T a->" <<a<<std::endl;
    }
};
 

Ta::~Ta()
{
    std::cout << "Ta disconstruct" << std::endl;
}
 

int main() {
    

     Ta ta(10);
        ta.Hello();
   
    Ta tb = 20; //如果不加explicit关键字，那么这个构造函数可以隐式转换 Ta ta = 10;
    tb.Hello();

    return 0;
}

 
```

# 总结
一般只在 单参数构造函数使用 explicit 防止发生隐式转换

在Qt库中，explicit 关键字经常被用来修饰构造函数，主要是为了防止不希望发生的隐式类型转换。隐式类型转换可能会导致代码难以理解和维护，尤其是当这种转换不是很明显或者不是预期的时候。

隐式类型转换的问题
在Qt中，如果一个类的构造函数接受单个参数，并且没有将该构造函数标记为 explicit，那么这个构造函数可能会被编译器用于隐式类型转换。这意味着，如果一个函数期望一个对象，但是传递给它的是一个不同类型的参数，编译器可能会自动调用这个构造函数来创建一个临时对象。这可能会导致一些不易察觉的错误，尤其是在信号和槽机制中，槽函数的参数类型必须与发射信号时传递的参数类型完全匹配
。

explicit 的作用
使用 explicit 关键字可以防止这种隐式转换。当构造函数被声明为 explicit 时，它只能被显式调用，不能进行隐式的类型转换。这样可以确保类型转换是程序员明确意图的，从而提高代码的可读性和健壮性。

示例
cpp
class MyClass {
public:
    explicit MyClass(int value) {
        // ...
    }
};
void someFunction(MyClass obj) {
    // ...
}
int main() {
    MyClass obj1 = 42;  // 错误：不能隐式调用构造函数
    MyClass obj2(42);   // 正确：显式调用构造函数
    someFunction(42);   // 错误：不能隐式转换为 MyClass
    someFunction(MyClass(42));  // 正确：显式调用构造函数
    return 0;
}
在这个例子中，尝试将整数 42 隐式转换为 MyClass 对象会导致编译错误，因为构造函数被声明为 explicit。

结论
在Qt中使用 explicit 关键字是一种良好的编程实践，它有助于避免不期望的隐式类型转换，确保类型转换的明确性和安全性。这在编写复杂和易于维护的Qt应用程序时尤其重要。