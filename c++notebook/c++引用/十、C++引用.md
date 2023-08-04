# 十、C++引用
我们知道，参数的传递本质上是一次赋值的过程，赋值就是对内存进行拷贝。所谓内存拷贝，是指将一块内存上的数据复制到另一块内存上。  
单个数据所占据的内存往往只有几个字节，因此对于单个数据的拷贝十分迅速，但是对于数组、结构体、对象等一系列数据的集合，他们所占用的内存有时候非常大，此时进行内存拷贝就很可能会消耗过多时间。

因此为了提高效率，我们曾建议通过传递指针的方式从而直接对数据的内存进行改动，这样不会进行过多的内存拷贝，效率会大大提高。

但是在 C++ 中，我们有了一种比指针更加便捷的传递聚合类型数据的方式，那就是引用（Reference）。

引用（Reference）是 C++ 相对于C语言的又一个扩充。引用可以看做是数据的一个别名，通过这个别名和原来的名字都能够找到这份数据。引用类似于 Windows 中的快捷方式，一个可执行程序可以有多个快捷方式，通过这些快捷方式和可执行程序本身都能够运行程序；引用还类似于人的绰号（笔名），使用绰号（笔名）和本名都能表示一个人。

<font color="lightsalmon">引用必须在定义的同时初始化，并且以后也要从一而终，不能再引用其它数据，这有点类似于常量（const 变量）。</font>
```c++
#include <iostream>
using namespace std;
int main() {
    int a = 99;
    int &r = a;
    cout << a << ", " << r << endl;
    cout << &a << ", " << &r << endl;
    return 0;
}
```
---
    运行结果：  
    99, 99  
    0x28ff44, 0x28ff44

<font color="lightsalmon">注意，引用在定义时需要添加&，在使用时不能添加&，使用时添加&表示取地址。</font>

由于引用 r 和原始变量 a 都是指向同一地址，所以通过引用也可以修改原始变量中所存储的数据，请看下面的例子：
```c++
#include <iostream>
using namespace std;
int main() {
    int a = 99;
    int &r = a;
    r = 47;
    cout << a << ", " << r << endl;
    return 0;
}
```
---
    运行结果：  
    47, 47

如果读者不希望通过引用来修改原始的数据，那么可以在定义时添加 const 限制，形式为：  
```const type &name = value;```

也可以是：  
```type const &name = value;```

这种引用方式为常引用

## 1.C++引用作为函数参数
在定义或声明函数时，我们可以将函数的形参指定为引用的形式，这样在调用函数时就会将实参和形参绑定在一起，让它们都指代同一份数据。如此一来，如果在函数体中修改了形参的数据，那么实参的数据也会被修改，从而拥有“在函数内部影响函数外部数据”的效果。

一个能够展现按引用传参的优势的例子就是交换两个数的值，请看下面的代码：
```c++
#include <iostream>
using namespace std;
void swap1(int a, int b);
void swap2(int *p1, int *p2);
void swap3(int &r1, int &r2);
int main() {
    int num1, num2;
    cout << "Input two integers: ";
    cin >> num1 >> num2;
    swap1(num1, num2);
    cout << num1 << " " << num2 << endl;
    cout << "Input two integers: ";
    cin >> num1 >> num2;
    swap2(&num1, &num2);
    cout << num1 << " " << num2 << endl;
    cout << "Input two integers: ";
    cin >> num1 >> num2;
    swap3(num1, num2);
    cout << num1 << " " << num2 << endl;
    return 0;
}
//直接传递参数内容
void swap1(int a, int b) {
    int temp = a;
    a = b;
    b = temp;
}
//传递指针
void swap2(int *p1, int *p2) {
    int temp = *p1;
    *p1 = *p2;
    *p2 = temp;
}
//按引用传参
void swap3(int &r1, int &r2) {
    int temp = r1;
    r1 = r2;
    r2 = temp;
}
```
---
    运行结果：
    Input two integers: 12 34↙
    12 34
    Input two integers: 88 99↙
    99 88
    Input two integers: 100 200↙
    200 100

本例演示了三种交换变量的值的方法：
1) swap1() 直接传递参数的内容，不能达到交换两个数的值的目的。对于 swap1() 来说，a、b 是形参，是作用范围仅限于函数内部的局部变量，它们有自己独立的内存，和 num1、num2 指代的数据不一样。调用函数时分别将 num1、num2 的值传递给 a、b，此后 num1、num2 和 a、b 再无任何关系，在 swap1() 内部修改 a、b 的值不会影响函数外部的 num1、num2，更不会改变 num1、num2 的值。

2) swap2() 传递的是指针，能够达到交换两个数的值的目的。调用函数时，分别将 num1、num2 的指针传递给 p1、p2，此后 p1、p2 指向 a、b 所代表的数据，在函数内部可以通过指针间接地修改 a、b 的值。我们在《C语言指针变量作为函数参数》中也对比过第 1)、2) 中方式的区别。

2) swap3() 是按引用传递，能够达到交换两个数的值的目的。调用函数时，分别将 r1、r2 绑定到 num1、num2 所指代的数据，此后 r1 和 num1、r2 和 num2 就都代表同一份数据了，通过 r1 修改数据后会影响 num1，通过 r2 修改数据后也会影响 num2。

<font color="lightsalmon">从以上代码的编写中可以发现，按引用传参在使用形式上比指针更加直观。在以后的 C++ 编程中，我鼓励读者大量使用引用，它一般可以代替指针（当然指针在C++中也不可或缺），C++ 标准库也是这样做的。</font>

## 2.C++引用作为函数返回值
引用除了可以作为函数形参，还可以作为函数返回值，请看下面的例子：
```c++
#include <iostream>
using namespace std;
int &plus10(int &r) {
    r += 10;
    return r;
}
int main() {
    int num1 = 10;
    int num2 = plus10(num1);
    cout << num1 << " " << num2 << endl;
    return 0;
}
```
    运行结果：
    20 20
在将引用作为函数返回值时应该注意一个小问题，就是不能返回局部数据（例如局部变量、局部对象、局部数组等）的引用，因为当函数调用完成后局部数据就会被销毁，有可能在下次使用时数据就不存在了，C++ 编译器检测到该行为时也会给出警告。

更改上面的例子，让 plus10() 返回一个局部数据的引用：
```c++
#include <iostream>
using namespace std;
int &plus10(int &r) {
    int m = r + 10;
    return m;  //返回局部数据的引用
}
int main() {
    int num1 = 10;
    int num2 = plus10(num1);
    cout << num2 << endl;
    int &num3 = plus10(num1);
    int &num4 = plus10(num3);
    cout << num3 << " " << num4 << endl;
    return 0;
}
```
    在 Visual Studio 下的运行结果：
    20
    -858993450 -858993450
---
    在 GCC 下的运行结果：
    20
    30 30
---
    在 C-Free 下的运行结果：
    20
    30 0
---
    而我们期望的运行结果是：
    20
    20 30

plus10() 返回一个对局部变量 m 的引用，这是导致运行结果非常怪异的根源，因为函数是在栈上运行的，并且运行结束后会放弃对所有局部数据的管理权，后面的函数调用会覆盖前面函数的局部数据。本例中，第二次调用 plus10() 会覆盖第一次调用 plus10() 所产生的局部数据，第三次调用 plus10() 会覆盖第二次调用 plus10() 所产生的局部数据。