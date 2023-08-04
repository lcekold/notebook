# 二十八、C++ typeid运算符：获取类型信息
typeid 运算符用来获取一个表达式的类型信息。类型信息对于编程语言非常重要，它描述了数据的各种属性：
* 对于基本类型（int、float 等C++内置类型）的数据，类型信息所包含的内容比较简单，主要是指数据的类型。

* 对于类类型的数据（也就是对象），类型信息是指对象所属的类、所包含的成员、所在的继承关系等。

类型信息是创建数据的模板，数据占用多大内存、能进行什么样的操作、该如何操作等，这些都由它的类型信息决定。

typeid 的操作对象既可以是表达式，也可以是数据类型，下面是它的两种使用方法：
```c++
typeid( dataType )
typeid( expression )
```

dataType 是数据类型，expression 是表达式，这和 sizeof 运算符非常类似，只不过 sizeof 有时候可以省略括号( )，而 typeid 必须带上括号。

typeid 会把获取到的类型信息保存到一个 type_info 类型的对象里面，并返回该对象的常引用；当需要具体的类型信息时，可以通过成员函数来提取。typeid 的使用非常灵活，请看下面的例子（只能在 VC/VS 下运行）：

```c++
#include <iostream>
#include <typeinfo>
using namespace std;
class Base{ };
struct STU{ };
int main(){
    //获取一个普通变量的类型信息
    int n = 100;
    const type_info &nInfo = typeid(n);
    cout<<nInfo.name()<<" | "<<nInfo.raw_name()<<" | "<<nInfo.hash_code()<<endl;
    //获取一个字面量的类型信息
    const type_info &dInfo = typeid(25.65);
    cout<<dInfo.name()<<" | "<<dInfo.raw_name()<<" | "<<dInfo.hash_code()<<endl;
    //获取一个对象的类型信息
    Base obj;
    const type_info &objInfo = typeid(obj);
    cout<<objInfo.name()<<" | "<<objInfo.raw_name()<<" | "<<objInfo.hash_code()<<endl;
    //获取一个类的类型信息
    const type_info &baseInfo = typeid(Base);
    cout<<baseInfo.name()<<" | "<<baseInfo.raw_name()<<" | "<<baseInfo.hash_code()<<endl;
    //获取一个结构体的类型信息
    const type_info &stuInfo = typeid(struct STU);
    cout<<stuInfo.name()<<" | "<<stuInfo.raw_name()<<" | "<<stuInfo.hash_code()<<endl;
    //获取一个普通类型的类型信息
    const type_info &charInfo = typeid(char);
    cout<<charInfo.name()<<" | "<<charInfo.raw_name()<<" | "<<charInfo.hash_code()<<endl;
    //获取一个表达式的类型信息
    const type_info &expInfo = typeid(20 * 45 / 4.5);
    cout<<expInfo.name()<<" | "<<expInfo.raw_name()<<" | "<<expInfo.hash_code()<<endl;
    return 0;
}
```

    运行结果：
    int | .H | 529034928
    double | .N | 667332678
    class Base | .?AVBase@@ | 1035034353
    class Base | .?AVBase@@ | 1035034353
    struct STU | .?AUSTU@@ | 734635517
    char | .D | 4140304029
    double | .N | 667332678

从本例可以看出，typeid 的使用非常灵活，它的操作数可以是普通变量、对象、内置类型（int、float等）、自定义类型（结构体和类），还可以是一个表达式。

本例中还用到了 type_info 类的几个成员函数，下面是对它们的介绍：

* name() 用来返回类型的名称。
* raw_name() 用来返回名字编码（Name Mangling）算法产生的新名称。关于名字编码的概念，我们已在《C++函数编译原理和成员函数的实现》中讲到。
* hash_code() 用来返回当前类型对应的 hash 值。hash 值是一个可以用来标志当前类型的整数，有点类似学生的学号、公民的身份证号、银行卡号等。不过 hash 值有赖于编译器的实现，在不同的编译器下可能会有不同的整数，但它们都能唯一地标识某个类型。

遗憾的是，C++ 标准只对 type_info 类做了很有限的规定，不仅成员函数少，功能弱，而且各个平台的实现不一致。例如上面代码中的 name() 函数，nInfo.name()、objInfo.name()在 VC/VS 下的输出结果分别是int和class Base，而在 GCC 下的输出结果分别是i和4Base。

C++ 标准规定，type_info 类至少要有如下所示的 4 个 public 属性的成员函数，其他的扩展函数编译器开发者可以自由发挥，不做限制。
### 1) 原型：const char* name() const;
返回一个能表示类型名称的字符串。但是C++标准并没有规定这个字符串是什么形式的，例如对于上面的objInfo.name()语句，VC/VS 下返回“class Base”，但 GCC 下返回“4Base”。
### 2) 原型：bool before (const type_info& rhs) const;
判断一个类型是否位于另一个类型的前面，rhs 参数是一个 type_info 对象的引用。但是C++标准并没有规定类型的排列顺序，不同的编译器有不同的排列规则，程序员也可以自定义。要特别注意的是，这个排列顺序和继承顺序没有关系，基类并不一定位于派生类的前面。
### 3) 原型：bool operator== (const type_info& rhs) const;
重载运算符“==”，判断两个类型是否相同，rhs 参数是一个 type_info 对象的引用。
### 4) 原型：bool operator!= (const type_info& rhs) const;
重载运算符“!=”，判断两个类型是否不同，rhs 参数是一个 type_info 对象的引用。

    关于运算符重载，我们将在《C++运算符重载》一章中详细讲解。

raw_name() 是 VC/VS 独有的一个成员函数，hash_code() 在 VC/VS 和较新的 GCC 下有效。

可以发现，不像 Java、C# 等动态性较强的语言，C++ 能获取到的类型信息非常有限，也没有统一的标准，如同“鸡肋”一般，大部分情况下我们只是使用重载过的“==”运算符来判断两个类型是否相同。

## 判断类型是否相等
typeid 运算符经常被用来判断两个类型是否相等。

### 1) 内置类型的比较

例如有下面的定义：

```c++
char *str;
int a = 2;
int b = 10;
float f;
```

类型判断结果为：

|            类型比较           |  结果 |           类型比较           |  结果 |   |
|:-----------------------------:|:-----:|:----------------------------:|:-----:|---|
| typeid(int) == typeid(int)    | true  | typeid(int) == typeid(char)  | false |   |
| typeid(char*) == typeid(char) | false | typeid(str) == typeid(char*) | true  |   |
| typeid(a) == typeid(int)      | true  | typeid(b) == typeid(int)     | true  |   |
| typeid(a) == typeid(a)        | true  | typeid(a) == typeid(b)       | true  |   |
| typeid(a) == typeid(f)        | false | typeid(a/b) == typeid(int)   | true  |   |



typeid 返回 type_info 对象的引用，而表达式typeid(a) == typeid(b)的结果为 true，可以说明，一个类型不管使用了多少次，编译器都只为它创建一个对象，所有 typeid 都返回这个对象的引用。

需要提醒的是，为了减小编译后文件的体积，编译器不会为所有的类型创建 type_info 对象，只会为使用了 typeid 运算符的类型创建。不过有一种特殊情况，就是带虚函数的类（包括继承来的），不管有没有使用 typeid 运算符，编译器都会为带虚函数的类创建 type_info 对象，我们将在《C++ RTTI机制精讲（C++运行时类型识别机制）》中展开讲解。

### 2) 类的比较
例如有下面的定义：

```c++
class Base{};
class Derived: public Base{};
Base obj1;
Base *p1;
Derived obj2;
Derived *p2 = new Derived;
p1 = p2;
```

类型判断结果为：

|           类型比较           |  结果 |            类型比较            |  结果 |   |
|:----------------------------:|:-----:|:------------------------------:|:-----:|---|
| typeid(obj1) == typeid(p1)   | false | typeid(obj1) == typeid(*p1)    | true  |   |
| typeid(&obj1) == typeid(p1)  | true  | typeid(obj1) == typeid(obj2)   | false |   |
| typeid(obj1) == typeid(Base) | true  | typeid(*p1) == typeid(Base)    | true  |   |
| typeid(p1) == typeid(Base*)  | true  | typeid(p1) == typeid(Derived*) | false |   |
| typeid(a) == typeid(f)       | false | typeid(a/b) == typeid(int)     | true  |   |

表达式typeid(*p1) == typeid(Base)和typeid(p1) == typeid(Base*)的结果为 true 可以说明：即使将派生类指针 p2 赋值给基类指针 p1，p1 的类型仍然为 Base*。

## type_info 类的声明
最后我们再来看一下 type_info 类的声明，以进一步了解它所包含的成员函数以及这些函数的访问权限。type_info 类位于typeinfo头文件，声明形式类似于：
```c++
class type_info {
public:
    virtual ~type_info();
    int operator==(const type_info& rhs) const;
    int operator!=(const type_info& rhs) const;
    int before(const type_info& rhs) const;
    const char* name() const;
    const char* raw_name() const;
private:
    void *_m_data;
    char _m_d_name[1];
    type_info(const type_info& rhs);
    type_info& operator=(const type_info& rhs);
};
```
它的构造函数是 private 属性的，所以不能在代码中直接实例化，只能由编译器在内部实例化（借助友元）。而且还重载了“=”运算符，也是 private 属性的，所以也不能赋值。