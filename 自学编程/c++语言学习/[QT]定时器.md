---
typora-root-url: img
---

# QT事件处理

Qt可以使用信号和槽，也能使用事件。信号和槽是通过事件实现的，事件的检测在信号发生之前。

![黑色调](/heisediao.jpg)



# 定时器

用来间隔一段时间处理一次代码，和sleep的区别不阻塞

```c++
#include <QTimer>
class myclass{

private slots:
	void onTimeout(void); //定时器槽函数
private:
	QTimer m_timer; //定时器
}
//连接信号和槽
connect(&m_timer,SIGNAL(timeout()),this,SLOT(onTimeout()));

void button_clicked()
{
    static bool flag=true;
    if(flag==true){
        m_timer.start(50);//开启定时器,定时周期50ms
        flag=false;
    }else{
        m_timer.stop();
        flag=true;
    }
    
}

void onTimeout(void)
{
    //定时器到了以后执行的函数
    dosomething();
}

```

# 鼠标事件

鼠标点击事件：点击 释放 移动 双击

常用辅助类：

- `QRect(x,y,w,h)`：位置和大小

- `QPoint(x,y)`：位置

- `QSize(w,h)`：大小