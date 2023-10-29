在读写文件时，有时希望直接跳到文件中的某处开始读写，这就需要先将文件的读写指针指向该处，然后再进行读写。
* ifstream 类和 fstream 类有 seekg 成员函数，可以设置文件读指针的位置；
* ofstream 类和 fstream 类有 seekp 成员函数，可以设置文件写指针的位置。

这两个函数的原型如下：

    ostream & seekp (int offset, int mode);
    istream & seekg (int offset, int mode);

mode 代表文件读写指针的设置模式，有以下三种选项：
* ios::beg：让文件读指针（或写指针）指向从文件开始向后的 offset 字节处。offset 等于 0 即代表文件开头。在此情况下，offset 只能是非负数。
* ios::cur：在此情况下，offset 为负数则表示将读指针（或写指针）从当前位置朝文件开头方向移动 offset 字节，为正数则表示将读指针（或写指针）从当前位置朝文件尾部移动 offset字节，为 0 则不移动。
* ios::end：让文件读指针（或写指针）指向从文件结尾往前的 |offset|（offset 的绝对值）字节处。在此情况下，offset 只能是 0 或者负数。

此外，我们还可以得到当前读写指针的具体位置：
* ifstream 类和 fstream 类还有 tellg 成员函数，能够返回文件读指针的位置；
* ofstream 类和 fstream 类还有 tellp 成员函数，能够返回文件写指针的位置。

这两个成员函数的原型如下：

    int tellg();
    int tellp();

<font color="green">要获取文件长度，可以用 seekg 函数将文件读指针定位到文件尾部，再用 tellg 函数获取文件读指针的位置，此位置即为文件长度。</font>

例题：假设学生记录文件 students.dat 是按照姓名排好序的，编写程序，在 students.dat 文件中用折半查找的方法找到姓名为 Jack 的学生记录，并将其年龄改为 20（假设文件很大，无法全部读入内存）。程序如下：

```c++
#include <iostream>
#include <fstream>
#include <cstring>
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
    fstream ioFile("students.dat", ios::in|ios::out);//用既读又写的方式打开
    if(!ioFile) {
        cout << "error" ;
        return 0;
    }
    ioFile.seekg(0,ios::end); //定位读指针到文件尾部，
                              //以便用以后tellg 获取文件长度
    int L = 0,R; // L是折半查找范围内第一个记录的序号
                  // R是折半查找范围内最后一个记录的序号
    R = ioFile.tellg() / sizeof(CStudent) - 1;
    //首次查找范围的最后一个记录的序号就是: 记录总数- 1
    do {
        int mid = (L + R)/2; //要用查找范围正中的记录和待查找的名字比对
        ioFile.seekg(mid *sizeof(CStudent),ios::beg); //定位到正中的记录
        ioFile.read((char *)&s, sizeof(s));
        int tmp = strcmp( s.szName,"Jack");
        if(tmp == 0) { //找到了
            s.age = 20;
            ioFile.seekp(mid*sizeof(CStudent),ios::beg);
            ioFile.write((char*)&s, sizeof(s));
            break;
        }
        else if (tmp > 0) //继续到前一半查找
            R = mid - 1 ;
        else  //继续到后一半查找
            L = mid + 1;
    }while(L <= R);
    ioFile.close();
    return 0;
}
```
