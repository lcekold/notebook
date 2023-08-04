# 二十三、C++多态和虚函数快速入门教程
请看以下代码
```c++
#include <iostream>
using namespace std;
//基类People
class People{
public:
    People(char *name, int age);
    void display();
protected:
    char *m_name;
    int m_age;
};
People::People(char *name, int age): m_name(name), m_age(age){}
void People::display(){
    cout<<m_name<<"今年"<<m_age<<"岁了，是个无业游民。"<<endl;
}
//派生类Teacher
class Teacher: public People{
public:
    Teacher(char *name, int age, int salary);
    void display();
private:
    int m_salary;
};
Teacher::Teacher(char *name, int age, int salary): People(name, age), m_salary(salary){}
void Teacher::display(){
    cout<<m_name<<"今年"<<m_age<<"岁了，是一名教师，每月有"<<m_salary<<"元的收入。"<<endl;
}
int main(){
    People *p = new People("王志刚", 23);
    p -> display();
    p = new Teacher("赵宏佳", 45, 8200);
    p -> display();
    return 0;
}
```

    运行结果：
    王志刚今年23岁了，是个无业游民。
    赵宏佳今年45岁了，是个无业游民。

我们直观上认为，如果指针指向了派生类对象，那么就应该使用派生类的成员变量和成员函数，这符合人们的思维习惯。但是本例的运行结果却告诉我们，当基类指针 p 指向派生类 Teacher 的对象时，虽然使用了 Teacher 的成员变量，但是却没有使用它的成员函数，导致输出结果不伦不类（赵宏佳本来是一名老师，输出结果却显示人家是个无业游民），不符合我们的预期。

换句话说，通过基类指针只能访问派生类的成员变量，但是不能访问派生类的成员函数。

为了消除这种尴尬，让基类指针能够访问派生类的成员函数，C++ 增加了虚函数（Virtual Function）。使用虚函数非常简单，只需要在函数声明前面增加 virtual 关键字。

更改上面的代码，将 display() 声明为虚函数：

```c++
#include <iostream>
using namespace std;
//基类People
class People{
public:
    People(char *name, int age);
    virtual void display();  //声明为虚函数
protected:
    char *m_name;
    int m_age;
};
People::People(char *name, int age): m_name(name), m_age(age){}
void People::display(){
    cout<<m_name<<"今年"<<m_age<<"岁了，是个无业游民。"<<endl;
}
//派生类Teacher
class Teacher: public People{
public:
    Teacher(char *name, int age, int salary);
    virtual void display();  //声明为虚函数
private:
    int m_salary;
};
Teacher::Teacher(char *name, int age, int salary): People(name, age), m_salary(salary){}
void Teacher::display(){
    cout<<m_name<<"今年"<<m_age<<"岁了，是一名教师，每月有"<<m_salary<<"元的收入。"<<endl;
}
int main(){
    People *p = new People("王志刚", 23);
    p -> display();
    p = new Teacher("赵宏佳", 45, 8200);
    p -> display();
    return 0;
}
```
    运行结果：
    王志刚今年23岁了，是个无业游民。
    赵宏佳今年45岁了，是一名教师，每月有8200元的收入。
和前面的例子相比，本例仅仅是在 display() 函数声明前加了一个virtual关键字，将成员函数声明为了虚函数（Virtual Function），这样就可以通过 p 指针调用 Teacher 类的成员函数了，运行结果也证明了这一点（赵宏佳已经是一名老师了，不再是无业游民了）。

<font color="green">有了虚函数，基类指针指向基类对象时就使用基类的成员（包括成员函数和成员变量），指向派生类对象时就使用派生类的成员。换句话说，基类指针可以按照基类的方式来做事，也可以按照派生类的方式来做事，它有多种形态，或者说有多种表现方式，我们将这种现象称为多态（Polymorphism）。</font>

上面的代码中，同样是p->display();这条语句，当 p 指向不同的对象时，它执行的操作是不一样的。同一条语句可以执行不同的操作，看起来有不同表现方式，这就是多态。

多态是面向对象编程的主要特征之一，C++中虚函数的唯一用处就是构成多态。

<font color="green">C++提供多态的目的是：可以通过基类指针对所有派生类（包括直接派生和间接派生）的成员变量和成员函数进行“全方位”的访问，尤其是成员函数。如果没有多态，我们只能访问成员变量。</font>

前面我们说过，通过指针调用普通的成员函数时会根据指针的类型（通过哪个类定义的指针）来判断调用哪个类的成员函数，但是通过本节的分析可以发现，这种说法并不适用于虚函数，虚函数是根据指针的指向来调用的，指针指向哪个类的对象就调用哪个类的虚函数。

但是话又说回来，对象的内存模型是非常干净的，没有包含任何成员函数的信息，编译器究竟是根据什么找到了成员函数呢？我们将在《C++虚函数表精讲教程，直戳多态的实现机制》一节中给出答案。

## 借助引用也可以实现多态
引用在本质上是通过指针的方式实现的,既然借助指针可以实现多态，那么我们就有理由推断：借助引用也可以实现多态。

修改上例中 main() 函数内部的代码，用引用取代指针：
```c++
int main(){
    People p("王志刚", 23);
    Teacher t("赵宏佳", 45, 8200);
   
    People &rp = p;
    People &rt = t;
   
    rp.display();
    rt.display();
    return 0;
}
```
    运行结果：
    王志刚今年23岁了，是个无业游民。
    赵宏佳今年45岁了，是一名教师，每月有8200元的收入。

由于引用类似于常量，只能在定义的同时初始化，并且以后也要从一而终，不能再引用其他数据，所以本例中必须要定义两个引用变量，一个用来引用基类对象，一个用来引用派生类对象。从运行结果可以看出，当基类的引用指代基类对象时，调用的是基类的成员，而指代派生类对象时，调用的是派生类的成员。

<font color="green">不过引用不像指针灵活，指针可以随时改变指向，而引用只能指代固定的对象，在多态性方面缺乏表现力，所以以后我们再谈及多态时一般是说指针。</font>本例的主要目的是让读者知道，除了指针，引用也可以实现多态。

## 多态的用途
通过上面的例子读者可能还未发现多态的用途，不过确实也是，多态在小项目中鲜有有用武之地。

接下来的例子中，我们假设你正在玩一款军事游戏，敌人突然发动了地面战争，于是你命令陆军、空军及其所有现役装备进入作战状态。具体的代码如下所示：

```c++
#include <iostream>
using namespace std;
//军队
class Troops{
public:
    virtual void fight(){ cout<<"Strike back!"<<endl; }
};
//陆军
class Army: public Troops{
public:
    void fight(){ cout<<"--Army is fighting!"<<endl; }
};
//99A主战坦克
class _99A: public Army{
public:
    void fight(){ cout<<"----99A(Tank) is fighting!"<<endl; }
};
//武直10武装直升机
class WZ_10: public Army{
public:
    void fight(){ cout<<"----WZ-10(Helicopter) is fighting!"<<endl; }
};
//长剑10巡航导弹
class CJ_10: public Army{
public:
    void fight(){ cout<<"----CJ-10(Missile) is fighting!"<<endl; }
};
//空军
class AirForce: public Troops{
public:
    void fight(){ cout<<"--AirForce is fighting!"<<endl; }
};
//J-20隐形歼击机
class J_20: public AirForce{
public:
    void fight(){ cout<<"----J-20(Fighter Plane) is fighting!"<<endl; }
};
//CH5无人机
class CH_5: public AirForce{
public:
    void fight(){ cout<<"----CH-5(UAV) is fighting!"<<endl; }
};
//轰6K轰炸机
class H_6K: public AirForce{
public:
    void fight(){ cout<<"----H-6K(Bomber) is fighting!"<<endl; }
};
int main(){
    Troops *p = new Troops;
    p ->fight();
    //陆军
    p = new Army;
    p ->fight();
    p = new _99A;
    p -> fight();
    p = new WZ_10;
    p -> fight();
    p = new CJ_10;
    p -> fight();
    //空军
    p = new AirForce;
    p -> fight();
    p = new J_20;
    p -> fight();
    p = new CH_5;
    p -> fight();
    p = new H_6K;
    p -> fight();
    return 0;
}
```

    运行结果：
    Strike back!
    --Army is fighting!
    ----99A(Tank) is fighting!
    ----WZ-10(Helicopter) is fighting!
    ----CJ-10(Missile) is fighting!
    --AirForce is fighting!
    ----J-20(Fighter Plane) is fighting!
    ----CH-5(UAV) is fighting!
    ----H-6K(Bomber) is fighting!

这个例子中的派生类比较多，如果不使用多态，那么就需要定义多个指针变量，很容易造成混乱；而有了多态，只需要一个指针变量 p 就可以调用所有派生类的虚函数。

从这个例子中也可以发现，对于具有复杂继承关系的大中型程序，多态可以增加其灵活性，让代码更具有表现力。