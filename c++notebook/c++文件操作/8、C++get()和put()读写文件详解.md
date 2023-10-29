在某些特殊的场景中，我们可能需要逐个读取文件中存储的字符，或者逐个将字符存储到文件中。这种情况下，就可以调用 get() 和 put() 成员方法实现。

## C++ ostream::put()成员方法

通过《C++ cout.put()》一节的学习，读者掌握了如何通过执行 cout.put() 方法向屏幕输出单个字符。我们知道，fstream 和 ofstream 类继承自 ostream 类，因此 fstream 和 ofstream 类对象都可以调用 put() 方法。

当 fstream 和 ofstream 文件流对象调用 put() 方法时，该方法的功能就变成了向指定文件中写入单个字符。put() 方法的语法格式如下：

    ostream& put (char c);

其中，c 用于指定要写入文件的字符。该方法会返回一个调用该方法的对象的引用形式。例如，obj.put() 方法会返回 obj 这个对象的引用。

举个例子：

```c
#include <iostream>
#include <fstream>
using namespace std;
int main()
{
    char c;
    //以二进制形式打开文件
    ofstream outFile("out.txt", ios::out | ios::binary);
    if (!outFile) {
        cout << "error" << endl;
        return 0;
    }
    while (cin >> c) {
        //将字符 c 写入 out.txt 文件
        outFile.put(c);
    }
    outFile.close();
    return 0;
}
```

    执行程序，输入：
    http://c.biancheng.net/cplus/↙
    ^Z↙


    其中，↙表示输入换行符；^Z是 Ctrl+Z 的组合键，表示输入结束。

由此，程序中通过执行 while 循环，会将 "http://c.biancheng.net/cplus/" 字符串的字符逐个复制给变量 c，并逐个写入到 out.txt 文件。

    注意，由于文件存放在硬盘中，硬盘的访问速度远远低于内存。如果每次写一个字节都要访问硬盘，那么文件的读写速度就会慢得不可忍受。因此，操作系统在接收到 put() 方法写文件的请求时，会先将指定字符存储在一块指定的内存空间中（称为文件流输出缓冲区），等刷新该缓冲区（缓冲区满、关闭文件、手动调用 flush() 方法等，都会导致缓冲区刷新）时，才会将缓冲区中存储的所有字符“一股脑儿”全写入文件。

## C++ istream::get()成员方法

和 put() 成员方法的功能相对的是 get() 方法，其定义在 istream 类中，借助 cin.get() 可以读取用户输入的字符。在此基础上，fstream 和 ifstream 类继承自 istream 类，因此 fstream 和 ifstream 类的对象也能调用 get() 方法。

当 fstream 和 ifstream 文件流对象调用 get() 方法时，其功能就变成了从指定文件中读取单个字符（还可以读取指定长度的字符串）。这里仅介绍最常用的 2 种get()用法：

    int get();
    istream& get (char& c);

其中，第一种语法格式的返回值就是读取到的字符，只不过返回的是它的 ASCII 码，如果碰到输入的末尾，则返回值为 EOF。第二种语法格式需要传递一个字符变量，get() 方法会自行将读取到的字符赋值给这个变量。

本节前面在讲解 put() 方法时，生成了一个 out.txt 文件，下面的样例演示了如何通过 get() 方法逐个读取 out.txt 文件中的字符：

```c
#include <iostream>
#include <fstream>
using namespace std;
int main()
{
    char c;
    //以二进制形式打开文件
    ifstream inFile("out.txt", ios::out | ios::binary);
    if (!inFile) {
        cout << "error" << endl;
        return 0;
    }
    while ( (c=inFile.get())&&c!=EOF )   //或者 while(inFile.get(c))，对应第二种语法格式
    {
        cout << c ;
    }
    inFile.close();
    return 0;
}
```

程序执行结果为：

    http://c.biancheng.net/cplus/

注意，和 put() 方法一样，操作系统在接收到 get() 方法的请求后，哪怕只读取一个字符，也会一次性从文件中将很多数据（通常至少是 512 个字节，因为硬盘的一个扇区是 512 B）读到一块内存空间中（可称为文件流输入缓冲区），这样当读取下一个字符时，就不需要再访问硬盘中的文件，直接从该缓冲区中读取即可。