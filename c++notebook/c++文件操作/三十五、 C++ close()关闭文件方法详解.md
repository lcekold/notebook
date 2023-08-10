《C++ open打开文件》一节中，详细介绍了文件流对象如何调用 open() 成员方法打开指定文件。相对应地，文件流对象还可以主动关闭先前打开的文件，即调用 close() 成员方法。

我们知道，调用 open() 方法打开文件，是文件流对象和文件之间建立关联的过程。那么，调用 close() 方法关闭已打开的文件，就可以理解为是切断文件流对象和文件之间的关联。注意，close() 方法的功能仅是切断文件流与文件之间的关联，该文件流并会被销毁，其后续还可用于关联其它的文件。

close() 方法的用法很简单，其语法格式如下：

    void close( )

可以看到，该方法既不需要传递任何参数，也没有返回值。

举个例子：
```c++
#include <fstream>
using namespace std;
int main()
{
    const char *url="http://c.biancheng.net/cplus/";
    ofstream outFile("url.txt", ios::out);
    //向 url.txt 文件中写入字符串
    outFile.write(url, 30);
    //关闭已打开的文件
    outFile.close();
    return 0;
}
```

运行程序，在该程序同目录下会生成一个 url.txt 文件，其内部存储的数据为：

    http://c.biancheng.net/cplus/

有些读者可能发现，即便上面程序中不调用 close() 方法，也能成功向 url.txt 文件中写入 url 字符串。这是因为，当文件流对象的生命周期结束时，会自行调用其析构函数，该函数内部在销毁对象之前，会先调用 close() 方法切断它与任何文件的关联，最后才销毁它。

    强烈建议读者，使用 open() 方法打开的文件，一定要手动调用 close() 方法关闭，这样可以避免程序发生一些奇葩的错误！

值得一提的是，《C++处理输入输出错误》一节中介绍了 4 种流状态，它们也同样适用于文件流。当文件流对象未关联任何文件时，调用 close() 方法会失败，其会为文件流设置 failbit 状态标志，该标志可以被 fail() 成员方法捕获。例如：

```c++
#include <iostream>
#include <fstream>
using namespace std;
int main()
{
    const char *url="http://c.biancheng.net/cplus/";
    ofstream outFile;
    outFile.close();
    if (outFile.fail()) {
        cout << "文件操作过程发生了错误！";
    }
    return 0;
}
```

程序执行结果为：

    文件操作过程发生了错误！