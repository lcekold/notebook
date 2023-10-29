《cin.getline()》一节中，详细介绍了如何使用 getline() 方法从 cin 输入流缓冲区中读取一行字符串。在此基础上，getline() 方法还适用于读取指定文件中的一行数据，本节就给大家做详细的讲解。

我们知道，getline() 方法定义在 istream 类中，而 fstream 和 ifstream 类继承自 istream 类，因此 fstream 和 ifstream 的类对象可以调用 getline() 成员方法。

当文件流对象调用 getline() 方法时，该方法的功能就变成了从指定文件中读取一行字符串。该方法有以下 2 种语法格式：

    istream & getline(char* buf, int bufSize);
    istream & getline(char* buf, int bufSize, char delim);

其中，第一种语法格式用于从文件输入流缓冲区中读取 bufSize-1 个字符到 buf，或遇到 \n 为止（哪个条件先满足就按哪个执行），该方法会自动在 buf 中读入数据的结尾添加 '\0'。

第二种语法格式和第一种的区别在于，第一个版本是读到 \n 为止，第二个版本是读到 delim 字符为止。\n 或 delim 都不会被读入 buf，但会被从文件输入流缓冲区中取走。

以上 2 种格式中，getline() 方法都会返回一个当前所作用对象的引用。比如，obj.getline() 会返回 obj 的引用。

    注意，如果文件输入流中 \n 或 delim 之前的字符个数达到或超过 bufSize，就会导致读取失败。

举个例子：
```c
#include <iostream>
#include <fstream>
using namespace std;
int main()
{
    char c[40];
    //以二进制模式打开 in.txt 文件
    ifstream inFile("in.txt", ios::in | ios::binary);
    //判断文件是否正常打开
    if (!inFile) {
        cout << "error" << endl;
        return 0;
    }
    //从 in.txt 文件中读取一行字符串，最多不超过 39 个
    inFile.getline(c, 40);
    cout << c ;
    inFile.close();
    return 0;
}
```
假设 in.txt 文件中存有如下字符串：

    http://c.biancheng.net/cplus/

则程序执行结果为：

    http://c.biancheng.net/cplus/

当然，我们也可以使用 getline() 方法的第二种语法格式。例如，更改上面程序中第 15 行代码为：

    inFile.getline(c,40,'c');

这意味着，一旦遇到字符 'c'，getline() 方法就会停止读取。 再次运行程序，其输出结果为：
http://

另外，如果想读取文件中的多行数据，可以这样做：
```c
#include <iostream>
#include <fstream>
using namespace std;
int main()
{
    char c[40];
    ifstream inFile("in.txt", ios::in | ios::binary);
    if (!inFile) {
        cout << "error" << endl;
        return 0;
    }
    //连续以行为单位，读取 in.txt 文件中的数据
    while (inFile.getline(c, 40)) {
        cout << c << endl;
    }
    inFile.close();
    return 0;
}
```

假设 in.txt 文件中存有如下数据：

    http://c.biancheng.net/cplus/
    http://c.biancheng.net/python/
    http://c.biancheng.net/java/

则程序执行结果为：

    http://c.biancheng.net/cplus/
    http://c.biancheng.net/python/
    http://c.biancheng.net/java/