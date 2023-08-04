# 十六、C++基类和派生类的构造函数
前面我们说基类的成员函数可以被继承，可以通过派生类的对象访问，但这仅仅指的是普通的成员函数，类的构造函数不能被继承。构造函数不能被继承是有道理的，因为即使继承了，它的名字和派生类的名字也不一样，不能成为派生类的构造函数，当然更不能成为普通的成员函数。

在设计派生类时，对继承过来的成员变量的初始化工作也要由派生类的构造函数完成，但是大部分基类都有 private 属性的成员变量，它们在派生类中无法访问，更不能使用派生类的构造函数来初始化。

<font color="green">这种矛盾在C++继承中是普遍存在的，解决这个问题的思路是：在派生类的构造函数中调用基类的构造函数。</font>

下面的例子展示了如何在派生类的构造函数中调用基类的构造函数：

```c++
#include<iostream>
using namespace std;
//基类People
class People{
protected:
    char *m_name;
    int m_age;
public:
    People(char*, int);
};
People::People(char *name, int age): m_name(name), m_age(age){}
//派生类Student
class Student: public People{
private:
    float m_score;
public:
    Student(char *name, int age, float score);
    void display();
};
//People(name, age)就是调用基类的构造函数
Student::Student(char *name, int age, float score): People(name, age), m_score(score){ }
void Student::display(){
    cout<<m_name<<"的年龄是"<<m_age<<"，成绩是"<<m_score<<"。"<<endl;
}
int main(){
    Student stu("小明", 16, 90.5);
    stu.display();
    return 0;
}
```
    运行结果为：
    小明的年龄是16，成绩是90.5。

但如何派生类的构造函数这样写，则是错误的，代码如下：
```c++
Student::Student(char *name, int age, float score){
    People(name, age);
    m_score = score;
}
```
因为基类构造函数不会被继承，不能当做普通的成员函数来调用。换句话说，只能将基类构造函数的调用放在函数头部，不能放在函数体中。

另外，函数头部是对基类构造函数的调用，而不是声明，所以括号里的参数是实参，它们不但可以是派生类构造函数参数列表中的参数，还可以是局部变量、常量等，例如：
```Student::Student(char *name, int age, float score): People("小明", 16), m_score(score){ }```

## 构造函数的调用顺序
从上面的分析中可以看出，基类构造函数总是被优先调用，这说明创建派生类对象时，会先调用基类构造函数，再调用派生类构造函数，如果继承关系有好几层的话，例如：  
>A --> B --> C

那么创建 C 类对象时构造函数的执行顺序为：  
>A类构造函数 --> B类构造函数 --> C类构造函数

构造函数的调用顺序是按照继承的层次自顶向下、从基类再到派生类的。

<font color="green">还有一点要注意，派生类构造函数中只能调用直接基类的构造函数，不能调用间接基类的。</font>以上面的 A、B、C 类为例，C 是最终的派生类，B 就是 C 的直接基类，A 就是 C 的间接基类。

C++ 这样规定是有道理的，因为我们在 C 中调用了 B 的构造函数，B 又调用了 A 的构造函数，相当于 C 间接地（或者说隐式地）调用了 A 的构造函数，如果再在 C 中显式地调用 A 的构造函数，那么 A 的构造函数就被调用了两次，相应地，初始化工作也做了两次，这不仅是多余的，还会浪费CPU时间以及内存，毫无益处，所以 C++ 禁止在 C 中显式地调用 A 的构造函数。 

## 基类构造函数调用规则
事实上，通过派生类创建对象时必须要调用基类的构造函数，这是语法规定。换句话说，定义派生类构造函数时最好指明基类构造函数；如果不指明，就调用基类的默认构造函数（不带参数的构造函数）；如果没有默认构造函数，那么编译失败。请看下面的例子：

```c++
#include <iostream>
using namespace std;
//基类People
class People{
public:
    People();  //基类默认构造函数
    People(char *name, int age);
protected:
    char *m_name;
    int m_age;
};
People::People(): m_name("xxx"), m_age(0){ }
People::People(char *name, int age): m_name(name), m_age(age){}
//派生类Student
class Student: public People{
public:
    Student();
    Student(char*, int, float);
public:
    void display();
private:
    float m_score;
};
Student::Student(): m_score(0.0){ }  //派生类默认构造函数
Student::Student(char *name, int age, float score): People(name, age), m_score(score){ }
void Student::display(){
    cout<<m_name<<"的年龄是"<<m_age<<"，成绩是"<<m_score<<"。"<<endl;
}
int main(){
    Student stu1;
    stu1.display();
    Student stu2("小明", 16, 90.5);
    stu2.display();
    return 0;
}
```
    运行结果：
    xxx的年龄是0，成绩是0。
    小明的年龄是16，成绩是90.5。