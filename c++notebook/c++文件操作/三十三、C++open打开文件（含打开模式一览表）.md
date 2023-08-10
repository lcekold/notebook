在对文件进行读写操作之前，先要打开文件。打开文件有以下两个目的：
* 通过指定文件名，建立起文件和文件流对象的关联，以后要对文件进行操作时，就可以通过与之关联的流对象来进行。
* 指明文件的使用方式。使用方式有只读、只写、既读又写、在文件末尾添加数据、以文本方式使用、以二进制方式使用等多种。

打开文件可以通过以下两种方式进行：
* 调用流对象的 open 成员函数打开文件。
* 定义文件流对象时，通过构造函数打开文件。

## 1.使用 open 函数打开文件
先看第一种文件打开方式。以 ifstream 类为例，该类有一个 open 成员函数，其他两个文件流类也有同样的 open 成员函数：

    void open(const char* szFileName, int mode)

第一个参数是指向文件名的指针，第二个参数是文件的打开模式标记。

文件的打开模式标记代表了文件的使用方式，这些标记可以单独使用，也可以组合使用。表 1 列出了各种模式标记单独使用时的作用，以及常见的两种模式标记组合的作用。

|              模式标记             |          适用对象         |                                                          作用                                                          |   |  结果 |
|:---------------------------------:|:-------------------------:|:----------------------------------------------------------------------------------------------------------------------:|---|:-----:|
| ios::in                           | ifstream fstream          | 打开文件用于读取数据。如果文件不存在，则打开出错。                                                                     |   | true  |
| ios::out                          | ofstream fstream          | 打开文件用于写入数据。如果文件不存在，则新建该文件；如果文件原来就存在，则打开时清除原来的内容。                       |   | false |
| ios::app                          | ofstream fstream          | 打开文件，用于在其尾部添加数据。如果文件不存在，则新建该文件。                                                         |   | true  |
| ios::ate                          | ifstream                  | 打开一个已有的文件，并将文件读指针指向文件末尾（读写指 的概念后面解释）。如果文件不存在，则打开出错。                  |   | false |
| ios:: trunc                       | ofstream                  | 打开文件时会清空内部存储的所有数据，单独使用时与 ios::out 相同。                                                       |   | true  |
| ios::binary                       | ifstream ofstream fstream | 以二进制方式打开文件。若不指定此模式，则以文本模式打开。                                                               |   |       |
| ios::in \| ios::out               | fstream                   | 打开已存在的文件，既可读取其内容，也可向其写入数据。文件刚打开时，原有内容保持不变。如果文件不存在，则打开出错。       |   |       |
| ios::in \| ios::out               | ofstream                  | 打开已存在的文件，可以向其写入数据。文件刚打开时，原有内容保持不变。如果文件不存在，则打开出错。                       |   |       |
| ios::in \| ios::out \| ios::trunc | fstream                   | 打开文件，既可读取其内容，也可向其写入数据。如果文件本来就存在，则打开时清除原来的内容；如果文件不存在，则新建该文件。 |   |       |

ios::binary 可以和其他模式标记组合使用，例如：
* ios::in | ios::binary表示用二进制模式，以读取的方式打开文件。
* ios::out | ios::binary表示用二进制模式，以写入的方式打开文件。

文本方式与二进制方式打开文件的区别其实非常微小，我会在《文件的文本打开方式和二进制打开方式的区别》一节中专门解释。一般来说，如果处理的是文本文件，那么用文本方式打开会方便一些。但其实任何文件都可以以二进制方式打开来读写。

在流对象上执行 open 成员函数，给出文件名和打开模式，就可以打开文件。判断文件打开是否成功，可以看“对象名”这个表达式的值是否为 true，如果为 true，则表示文件打开成功。


下面的程序演示了如何打开文件：

```c++
#include <iostream>
#include <fstream>
using namespace std;
int main()
{
    ifstream inFile;
    inFile.open("c:\\tmp\\test.txt", ios::in);
    if (inFile)  //条件成立，则说明文件打开成功
        inFile.close();
    else
        cout << "test.txt doesn't exist" << endl;
    ofstream oFile;
    oFile.open("test1.txt", ios::out);
    if (!oFile)  //条件成立，则说明文件打开出错
        cout << "error 1" << endl;
    else
        oFile.close();
    oFile.open("tmp\\test2.txt", ios::out | ios::in);
    if (oFile)  //条件成立，则说明文件打开成功
        oFile.close();
    else
        cout << "error 2" << endl;
    fstream ioFile;
    ioFile.open("..\\test3.txt", ios::out | ios::in | ios::trunc);
    if (!ioFile)
        cout << "error 3" << endl;
    else
        ioFile.close();
    return 0;
}
```

调用 open 成员函数时，给出的文件名可以是全路径的，如第 7 行的c:\\tmp\\test.txt， 指明文件在 c 盘的 tmp 文件夹中；也可以只给出文件名，如第 13 行的test1.txt，这种情况下程序会在当前文件夹（也就是可执行程序所在的文件夹）中寻找要打开的文件。

第 18 行的tmp\\test2.txt给出的是相对路径，说明 test2.txt 位于当前文件夹的 tmp 子文件夹中。第 24 行的..\\test3.txt也是相对路径，代表上一层文件夹，此时要到当前文件夹的上一层文件夹中查找 test3.txt。此外，..\\..\\test4.txt、..\\tmp\\test4.txt等都是合法的带相对路径的文件名。

## 2.使用流类的构造函数打开文件
定义流对象时，在构造函数中给出文件名和打开模式也可以打开文件。以 ifstream 类为例，它有如下构造函数：

    ifstream::ifstream (const char* szFileName, int mode = ios::in, int);

第一个参数是指向文件名的指针；第二个参数是打开文件的模式标记，默认值为ios::in; 第三个参数是整型的，也有默认值，一般极少使用。

在定义流对象时打开文件的示例程序如下（用流类的构造函数打开文件）：

```c++
#include <iostream>
#include <fstream>
using namespace std;
int main()
{
    ifstream inFile("c:\\tmp\\test.txt", ios::in);
    if (inFile)
        inFile.close();
    else
        cout << "test.txt doesn't exist" << endl;
    ofstream oFile("test1.txt", ios::out);
    if (!oFile)
        cout << "error 1";
    else
        oFile.close();
    fstream oFile2("tmp\\test2.txt", ios::out | ios::in);
    if (!oFile2)
        cout << "error 2";
    else
        oFile.close();
    return 0;
}
```

    注意，当不再对打开的文件进行任何操作时，应及时调用 close() 成员方法关闭文件。有关该方法的用法，后续会做详细讲解。