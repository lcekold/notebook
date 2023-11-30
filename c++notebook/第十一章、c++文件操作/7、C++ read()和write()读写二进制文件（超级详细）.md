通过《C++文本文件读写操作》一节的学习，读者了解了以文本形式读写文件和以二进制形式读写文件的区别，并掌握了用重载的 >> 和 << 运算符实现以文本形式读写文件。在此基础上，本节继续讲解如何以二进制形式读写文件。

不过介绍具体的实现方法前，先给读者介绍一下相比以文本形式读写文件，以二进制形式读写文件有哪些好处？

举个例子，现在要做一个学籍管理程序，其中一个重要的工作就是记录学生的学号、姓名、年龄等信息。这意味着，我们需要用一个类来表示学生，如下所示：

```c++
class CStudent
{
    char szName[20];  //假设学生姓名不超过19个字符，以 '\0' 结尾
    char szId[l0];  //假设学号为9位，以 '\0' 结尾
    int age;  //年龄
};
```

前面章节中，我们学会了如何以文本形式读写文件，如果使用此方式存储学生的信息，则最终的文件中存储的学生信息可能是这个样子：

    Micheal Jackson 110923412 17
    Tom Hanks 110923413 18
    ......

要知道，这种存储学生信息的方式不但浪费空间，而且后期不利于查找指定学生的信息（查找效率低下），因为每个学生的信息所占用的字节数不同。

这种情况下，以二进制形式将学生信息存储到文件中，是非常不错的选择，因为以此形式存储学生信息，可以直接把 CStudent 对象写入文件中，这意味着每个学生的信息都只占用 sizeof(CStudent) 个字节。

值得一提的是，要实现以二进制形式读写文件，<< 和 >> 将不再适用，需要使用 C++ 标准库专门提供的 read() 和 write() 成员方法。其中，read() 方法用于以二进制形式从文件中读取数据；write() 方法用于以二进制形式将数据写入文件。

## C++ ostream::write()方法写文件

ofstream 和 fstream 的 write() 成员方法实际上继承自 ostream 类，其功能是将内存中 buffer 指向的 count 个字节的内容写入文件，基本格式如下：

    ostream & write(char* buffer, int count);

其中，buffer 用于指定要写入文件的二进制数据的起始位置；count 用于指定写入字节的个数。

    也就是说，该方法可以被 ostream 类的 cout 对象调用，常用于向屏幕上输出字符串。同时，它还可以被 ofstream 或者 fstream 对象调用，用于将指定个数的二进制数据写入文件。

同时，该方法会返回一个作用于该函数的引用形式的对象。举个例子，obj.write() 方法的返回值就是对 obj 对象的引用。

需要注意的一点是，write() 成员方法向文件中写入若干字节，可是调用 write() 函数时并没有指定这些字节写入文件中的具体位置。事实上，write() 方法会从文件写指针指向的位置将二进制数据写入。所谓文件写指针，是是 ofstream 或 fstream 对象内部维护的一个变量，文件刚打开时，文件写指针指向的是文件的开头（如果以 ios::app 方式打开，则指向文件末尾），用 write() 方法写入 n 个字节，写指针指向的位置就向后移动 n 个字节。

下面的程序演示了如何将学生信息以二进制形式写入文件：

```c++
#include <iostream>
#include <fstream>
using namespace std;
class CStudent
{
public:
    char szName[20];
    int age;
};
int main()
{
    CStudent s;
    ofstream outFile("students.dat", ios::out | ios::binary);
    while (cin >> s.szName >> s.age)
        outFile.write((char*)&s, sizeof(s));
    outFile.close();
    return 0;
}
```

输入

    Tom 60↙
    Jack 80↙
    Jane 40↙
    ^Z↙

其中，↙表示输出换行符，^Z 表示输入Ctrl+Z组合键结束输入。

执行程序后，会自动生成一个 students.dat 文件，其内部存有 72 字节的数据，如果用“记事本”打开此文件，可能看到如下乱码：

    Tom 烫烫烫烫烫烫烫烫<   Jack 烫烫烫烫烫烫烫蘌   Jane 烫烫烫烫烫烫烫?

值得一提的是，程序中第 13 行指定文件的打开模式为 ios::out | ios::binary，即以二进制写模式打开。在 Windows平台中，以二进制模式打开文件是非常有必要的，否则可能出错，原因会在《文件的文本打开方式和二进制打开方式的区别》一节中介绍。

另外，第 15 行将 s 对象写入文件。s 的地址就是要写入文件的内存缓冲区的地址，但是 &s 不是 char * 类型，因此要进行强制类型转换；第 16 行，文件使用完毕一定要关闭，否则程序结束后文件的内容可能不完整。

## C++ istream::read()方法读文件

ifstream 和 fstream 的 read() 方法实际上继承自 istream 类，其功能正好和 write() 方法相反，即从文件中读取 count 个字节的数据。该方法的语法格式如下：

    istream & read(char* buffer, int count);

其中，buffer 用于指定读取字节的起始位置，count 指定读取字节的个数。同样，该方法也会返回一个调用该方法的对象的引用。

和 write() 方法类似，read() 方法从文件读指针指向的位置开始读取若干字节。所谓文件读指针，可以理解为是 ifstream 或 fstream 对象内部维护的一个变量。文件刚打开时，文件读指针指向文件的开头（如果以 ios::app 方式打开，则指向文件末尾），用 read() 方法读取 n 个字节，读指针指向的位置就向后移动 n 个字节。因此，打开一个文件后连续调用 read() 方法，就能将整个文件的内容读取出来。

通过执行 write() 方法的示例程序，我们将 3 个学生的信息存储到了 students.dat 文件中，下面程序演示了如何使用 read() 方法将它们读取出来：

```c++
#include <iostream>
#include <fstream>
using namespace std;
class CStudent
{
public:
    char szName[20];
    int age;
};
int main()
{
    CStudent s;       
    ifstream inFile("students.dat",ios::in|ios::binary); //二进制读方式打开
    if(!inFile) {
        cout << "error" <<endl;
        return 0;
    }
    while(inFile.read((char *)&s, sizeof(s))) { //一直读到文件结束
        cout << s.szName << " " << s.age << endl;   
    }
    inFile.close();
    return 0;
}
```

    程序的输出结果是：
    Tom 60
    Jack 80
    Jane 40

注意，程序中第 18 行直接将 read() 方法作为 while 循环的判断条件，这意味着，read() 方法会一直读取到文件的末尾，将所有字节全部读取完毕，while 循环才会终止。

    另外，在使用 read() 方法的同时，如果想知道一共成功读取了多少个字节（读到文件尾时，未必能读取 count 个字节），可以在 read() 方法执行后立即调用文件流对象的 gcount() 成员方法，其返回值就是最近一次 read() 方法成功读取的字节数。感兴趣的读者可自行尝试，这里不再做具体演示。