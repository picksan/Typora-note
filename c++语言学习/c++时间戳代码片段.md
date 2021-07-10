```c++
#include <iostream> //输入输出
#include <string> //字符类
#include <chrono> //时间库
#include <ctime>

using namespace std;
//打印系统时间
void func_time()
{
    auto today = std::chrono::system_clock::now();
	time_t tt = chrono::system_clock::to_time_t(today);
 	string time{ ctime(&tt) };
 	cout << time;
}

```

```
#include <iostream>
#include <string>
#include <chrono>//std::chrono::system_clock::now();
#include <ctime> //strftime  localtime
#include <iomanip> //put_time
void func()
{
    //c++ 风格 获取当前时刻 转化为ctime格式，转化前精度达到ns级别
    //auto nowtime = std::chrono::system_clock::now();
    //cout<<nowtime.time_since_epoch().count()<<endl;
    //auto tm_nowtime = std::chrono::system_clock::to_time_t(nowtime);
    
    //c风格
    auto c_time = time(nullptr);//精度到秒
    cout<<c_time<<endl;
    char timebuf[100];
    std::strftime(timebuf,sizeof(timebuf),"%F %T",std::localtime(&c_time));//ctime 
    cout<<"c风格:"<<timebuf<<endl;

    std::stringstream ss; 
    ss << std::put_time(localtime(&c_time),"%Y-%m-%d %X"); //这个输出要用流来接收 c++11
    std::string str_time = ss.str();//string流转化为 string
    cout<<"c++风格:"<<str_time<<endl;
}
```

```c++
#include <iostream>
#include <string>
#include <chrono>
#include <ctime>
#include <iomanip>

using std::cin;
using std::cout;
using std::endl;
//获取当前时间戳，ms级别 转化时间1ms
//标准库实现
void getTimeNow()
{
    using namespace std::chrono;
    auto nowtime = system_clock::now();//当前时间点
    auto dul = nowtime.time_since_epoch();//距离原点的时间间隔 ns级别
    auto ml_dul =duration_cast<milliseconds>(dul);//时间间隔截断，转化为ms级别
    auto ms_time = ml_dul.count()%1000;//算出当前的毫秒值
    //使用标准库，转化为标准日期 时间
    auto c_time = system_clock::to_time_t(nowtime);//转化为time_t格式时间戳
    std::stringstream ss;//string流 
    //put_time格式化输出时间，加上之前的毫秒，形成毫秒级别的格式化时间戳
    ss << std::put_time(localtime(&c_time),"%Y-%m-%d %X")<<":"<<std::setfill('0')<<std::setw(3)<<ms_time; 
    std::string str_time = ss.str();//string流转化为 string
    cout<<"当前时间:"<<str_time<<endl;
}
```

