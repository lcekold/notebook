# 十四、C++继承时的名字遮蔽问题
如果派生类中的成员（包括成员变量和成员函数）和基类中的成员重名，那么就会遮蔽从基类继承过来的成员。所谓遮蔽，就是在派生类中使用该成员（包括在定义派生类时使用，也包括通过派生类对象访问该成员）时，实际上使用的是派生类新增的成员，而不是从基类继承来的。

下面是一个成员函数的名字遮蔽的例子：

```c++
#include<iostream>
using namespace std;
//基类People
class People{
public:
    void show();
protected:
    char *m_name;
    int m_age;
};
void People::show(){
    cout<<"嗨，大家好，我叫"<<m_name<<"，今年"<<m_age<<"岁"<<endl;
}
//派生类Student
class Student: public People{
public:
    Student(char *name, int age, float score);
public:
    void show();  //遮蔽基类的show()
private:
    float m_score;
};
Student::Student(char *name, int age, float score){
    m_name = name;
    m_age = age;
    m_score = score;
}
void Student::show(){
    cout<<m_name<<"的年龄是"<<m_age<<"，成绩是"<<m_score<<endl;
}
int main(){
    Student stu("小明", 16, 90.5);
    //使用的是派生类新增的成员函数，而不是从基类继承的
    stu.show();
    //使用的是从基类继承来的成员函数
    stu.People::show();
    return 0;
}
```
    运行结果：
    小明的年龄是16，成绩是90.5
    嗨，大家好，我叫小明，今年16岁
本例中，基类 People 和派生类 Student 都定义了成员函数 show()，它们的名字一样，会造成遮蔽。第 37 行代码中，stu 是 Student 类的对象，默认使用 Student 类的 show() 函数。

但是，基类 People 中的 show() 函数仍然可以访问，不过要加上类名和域解析符，如第 39 行代码所示。
## 基类成员函数和派生类成员函数不构成重载

基类成员和派生类成员的名字一样时会造成遮蔽，这句话对于成员变量很好理解，对于成员函数要引起注意，不管函数的参数如何，只要名字一样就会造成遮蔽。换句话说，基类成员函数和派生类成员函数不会构成重载，如果派生类有同名函数，那么就会遮蔽基类中的所有同名函数，不管它们的参数是否一样。  

下面的例子很好的说明了这一点：
```c++
#include<iostream>
using namespace std;
//基类Base
class Base{
public:
    void func();
    void func(int);
};
void Base::func(){ cout<<"Base::func()"<<endl; }
void Base::func(int a){ cout<<"Base::func(int)"<<endl; }
//派生类Derived
class Derived: public Base{
public:
    void func(char *);
    void func(bool);
};
void Derived::func(char *str){ cout<<"Derived::func(char *)"<<endl; }
void Derived::func(bool is){ cout<<"Derived::func(bool)"<<endl; }
int main(){
    Derived d;
    d.func("c.biancheng.net");
    d.func(true);
    d.func();  //compile error
    d.func(10);  //compile error
    d.Base::func();
    d.Base::func(100);
    return 0;
}
```
本例中，Base 类的func()、func(int)和 Derived 类的func(char *)、func(bool)四个成员函数的名字相同，参数列表不同，它们看似构成了重载，能够通过对象 d 访问所有的函数，实则不然，Derive 类的 func 遮蔽了 Base 类的 func，导致第 26、27 行代码没有匹配的函数，所以调用失败。

如果说有重载关系，那么也是 Base 类的两个 func 构成重载，而 Derive 类的两个 func 构成另外的重载。