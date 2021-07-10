# tr

```
dialog.setWindowTitle(tr("Hello, dialog!"));
```

tr("字符串")用于国际化时,方便替换成别的语言

# 关于connect

例子

```c++
//newspaper.h
#ifndef NEWSPAPER_H
#define NEWSPAPER_H
#include <QObject>

class Newspaper : public QObject
{
    //Qt必备的宏,如果要使用qt的语法扩展
    Q_OBJECT
public:
    Newspaper(const QString &name):
    m_name(name)
    {

    }
    virtual ~Newspaper() {}
    void send()
    {
        emit newPaper(m_name);//发生信号
    }
//qt的拓展,信号函数,不需要实现
signals:
    void newPaper(const QString &name) const;
private:
    QString m_name;
};


#endif // NEWSPAPER_H
```

```c++
#ifndef READER_H
#define READER_H

#include <QObject>
#include <QDebug>
class Reader : public QObject
{
    Q_OBJECT
public:
    Reader() {}
    virtual ~Reader() {}
    void receiveNewspaper(const QString & name)
    {
        qDebug()<<"receives Newspaper:"<<name;
    }
};

#endif // READER_H

```

```c++
#include <QApplication>

#include "newspaper.h"
#include "reader.h"


int main(int argc, char *argv[])
{
    QApplication app(argc, argv);

    Newspaper newspaper("newspaper A");
    Reader reader;
    QObject::connect(&newspaper,&Newspaper::newPaper,
                     &reader,&Reader::receiveNewspaper);
    newspaper.send();

    return app.exec();
}

```

# 关于QAction

```c++
// !!! Qt 5
// ========== mainwindow.h
#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QMainWindow>

class MainWindow : public QMainWindow
{
    Q_OBJECT
public:
    MainWindow(QWidget *parent = 0);
    ~MainWindow();

private:
    void open();

    QAction *openAction;
};

#endif // MAINWINDOW_H

// ========== mainwindow.cpp
#include <QAction>
#include <QMenuBar>
#include <QMessageBox>
#include <QStatusBar>
#include <QToolBar>

#include "mainwindow.h"

MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent)
{
    setWindowTitle(tr("Main Window")); //设置窗口标题

    openAction = new QAction(QIcon(":/images/doc-open"), tr("&Open..."), this);//设置统一动作的图标,文字,tr用于国际化,&代表将成为一个快捷键
    openAction->setShortcuts(QKeySequence::Open);//设置快捷方式,可以设置成tr("Ctrl+O")
    openAction->setStatusTip(tr("Open an existing file"));//划过显示的提示
    connect(openAction, &QAction::triggered, this, &MainWindow::open);//连接触发的槽函数

    QMenu *file = menuBar()->addMenu(tr("&File"));//菜单栏添加
    file->addAction(openAction);//设置动作

    QToolBar *toolBar = addToolBar(tr("&File"));//工具栏添加
    toolBar->addAction(openAction);//设置动作

    statusBar() ;//创建状态栏
}

MainWindow::~MainWindow()
{
}

void MainWindow::open()
{
    QMessageBox::information(this, tr("Information"), tr("Open"));
}
```

# 资源文件

资源起别名

# 组件

组件创建的是否就指定父对象

# 布局

```c++
//#include "mainwindow.h"

#include <QApplication>
#include <QWidget>
#include <QSpinBox>
#include <QSlider>
#include <QHBoxLayout>


int main(int argc, char *argv[])
{
    QApplication app(argc, argv);

    QWidget window;
    window.setWindowTitle("Enter your age");

    QSpinBox *spinBox = new QSpinBox(&window);
    QSlider *slider = new QSlider(Qt::Horizontal, &window);
    spinBox->setRange(0, 130);//可调数字的盒子
    slider->setRange(0, 130);//滑动条

    //注意双向绑定,不限循环
    QObject::connect(slider, &QSlider::valueChanged, spinBox, &QSpinBox::setValue);
    //当槽函数具有重载时,需要指定具体的哪个,不然无法connect
    void (QSpinBox:: *spinBoxSignal)(int) = &QSpinBox::valueChanged;
    QObject::connect(spinBox, spinBoxSignal, slider, &QSlider::setValue);
    spinBox->setValue(35);

    //布局
    QHBoxLayout *layout = new QHBoxLayout;
    layout->addWidget(spinBox);
    layout->addWidget(slider);
    window.setLayout(layout);

    window.show();

    return app.exec();
}

```

# 顶层窗口

指定了窗口的父类，作为子对话框，任务表不会有自己的窗口名

```c++
QDialog dialog(this);
dialog.setWindowTitle(tr("hello dialog"));
dialog.exec();
```

没指定窗口父类，作为顶层窗口，任务栏会有自己的窗口名

```
QDialog dialog(0); // or QDialog dialog;
dialog.setWindowTitle(tr("hello dialog"));
dialog.exec();
```

# 模态对话框

会阻塞其他窗口

## 窗口级别模态

只阻塞相关窗口

```
QDialog::open()
```



## 应用程序级别的模态

阻塞应用程序的所有窗口

```
QDialog::exec()
```



# 非模态对话框

不会阻塞对话框

```
QDialog dialog(this);
dialog.setWindowTitle(tr("hello dialog"));
dialog::show()
```

一闪而过：在栈上创建，然后没有阻塞，函数返回，dialog被析构

改为堆上创建就没事了

```
void MainWindow::open()
{
QDialog* dialog =new  QDialog(this);
dialog->setWindowTitle(tr("hello dialog"));
dialog->show();
}
```

上述存在内存泄漏的情况

this的窗口不关闭，会一直占用内存

```
void MainWindow::open()
{
    QDialog *dialog = new QDialog;
    dialog->setAttribute(Qt::WA_DeleteOnClose);
    dialog->setWindowTitle(tr("Hello, dialog!"));
    dialog->show();
}
```

取消掉对话框窗口栏的？

```
void MainWindow::open()
{
    //QMessageBox::information(this, tr("Information"), tr("Open"));
    QDialog* dialog =new  QDialog(this);
    dialog->setWindowFlag(Qt::WindowContextHelpButtonHint,false);
    
    dialog->setAttribute(Qt::WA_DeleteOnClose);//对话框关闭,自动delete
    dialog->setWindowTitle(tr("hello dialog"));
    dialog->show();
}
```

qt内置对话框

- `QColorDialog`：选择颜色；
- `QFileDialog`：选择文件或者目录；
- `QFontDialog`：选择字体；
- `QInputDialog`：允许用户输入一个值，并将其值返回；
- `QMessageBox`：模态对话框，用于显示信息、询问问题等；
- `QPageSetupDialog`：为打印机提供纸张相关的选项；
- `QPrintDialog`：打印机配置；
- `QPrintPreviewDialog`：打印预览；
- `QProgressDialog`：显示操作过程。

```
void MainWindow::open()
{
    //QMessageBox::information(this, tr("Information"), tr("Open"));
    QDialog* dialog =new  QDialog(this);
    dialog->setWindowFlag(Qt::WindowContextHelpButtonHint,false);
    
    dialog->setAttribute(Qt::WA_DeleteOnClose);//对话框关闭,自动delete
    dialog->setWindowTitle(tr("hello dialog"));
    dialog->show();
}
void MainWindow::open()
{
QMessageBox msgBox;
    msgBox.setText(tr("The document has been modified."));
    msgBox.setInformativeText(tr("Do you want to save your changes?"));
    msgBox.setDetailedText(tr("Differences here..."));
    msgBox.setStandardButtons(QMessageBox::Save
                              | QMessageBox::Discard
                              | QMessageBox::Cancel);
    msgBox.setDefaultButton(QMessageBox::Save);
    int ret = msgBox.exec();
    switch (ret) {
    case QMessageBox::Save:
        qDebug() << "Save document!";
        break;
    case QMessageBox::Discard:
        qDebug() << "Discard changes!";
        break;
    case QMessageBox::Cancel:
        qDebug() << "Close document!";
        break;
    }

}

```

# 关于连接

函数由重载时，需要指明

```
QObject::connect(&newspaper,
                 static_cast<void (Newspaper:: *)(const QString &, const QDate &)>(&Newspaper::newPaper),
                 &reader,
                 &Reader::receiveNewspaper);
```

信号参数不能有默认值

```
void QPushButton::clicked(bool checked = false)
```

案例 创建action并加入toolbar

```
setWindowTitle(tr("Main Window"));

    openAction = new QAction(QIcon(":/images/doc-open"), tr("&Open..."), this);
    openAction->setShortcuts(QKeySequence::Open);
    openAction->setStatusTip(tr("Open an existing file"));//鼠标停留时在状态栏显示

    saveAction = new QAction(QIcon(":/images/doc-save"),tr("&Save..."),this);
    saveAction->setShortcut(QKeySequence::Save);
    saveAction->setStatusTip(tr("Save the file"));

    connect(openAction, &QAction::triggered, this, &MainWindow::open);
    connect(saveAction,&QAction::triggered,this,&MainWindow::save);


    QMenu *file = menuBar()->addMenu(tr("&File"));
    file->addAction(openAction);
    file->addAction(saveAction);

    QToolBar *toolBar = addToolBar(tr("&File"));
    toolBar->addAction(openAction);
    toolBar->addAction(saveAction);

    statusBar() ;

    textEdit = new QTextEdit(this);
    setCentralWidget(textEdit);
```

案例 打开文件选择框

```
void MainWindow::open()
{
    QString path = QFileDialog::getOpenFileName(this,
                                                tr("Open File"),
                                                ".",
                                                tr("Text Files(*.txt)"));
    if(!path.isEmpty())
    {
        //中文乱码 ansi编码
        QFile file(path);
        if(!file.open(QIODevice::ReadOnly | QIODevice::Text)){
            QMessageBox::warning(this,tr("Read File"),tr("can open file:\n%1").arg(path));
            return;
        }
        QTextStream in(&file);
        in.setCodec("UTF-8");//不设置默认ansi
        textEdit->setText(in.readAll());//文件太大会直接死掉
        file.close();
    }else{
        QMessageBox::warning(this,tr("Path"),tr("You did not select any file"));

    }
}
void MainWindow::save()
{
    QString path = QFileDialog::getSaveFileName(this,
                                                tr("save file"),
                                                ".",
                                                tr("Text file(*.txt);;"Markdown file(*.md)"));
    if(!path.isEmpty())
    {
        //中文乱码
        QFile file(path);
        if(!file.open(QIODevice::WriteOnly | QIODevice::Text)){
            QMessageBox::warning(this,tr("Write File"),tr("can save file:\n%1").arg(path));
            return;
        }
        QTextStream out(&file);
        in.setCodec("UTF-8");//不设置默认ansi
        out<<textEdit->toPlainText();
        file.close();
    }else{
        QMessageBox::warning(this,tr("Path"),tr("You did not select any file"));

    }

}
```

```
QString getOpenFileName(QWidget * parent = 0,
                        const QString & caption = QString(),
                        const QString & dir = QString(),
                        const QString & filter = QString(),
                        QString * selectedFilter = 0,
                        Options options = 0)
```

函数解析：

- parent：父窗口。我们前面介绍过，Qt 的标准对话框提供静态函数，用于返回一个模态对话框（在一定程度上这就是外观模式的一种体现）；
- caption：对话框标题；
- dir：对话框打开时的默认目录，“.” 代表程序运行目录，“/” 代表当前盘符的根目录（特指 Windows 平台；Linux 平台当然就是根目录），这个参数也可以是平台相关的，比如“C:\\”等；
- filter：过滤器。我们使用文件对话框可以浏览很多类型的文件，但是，很多时候我们仅希望打开特定类型的文件。比如，文本编辑器希望打开文本文件，图片浏览器希望打开图片文件。过滤器就是用于过滤特定的后缀名。如果我们使用“Image Files(*.jpg *.png)”，则只能显示后缀名是 jpg 或者 png 的文件。如果需要多个过滤器，使用“;;”分割，比如“JPEG Files(*.jpg);;PNG Files(*.png)”；
- selectedFilter：默认选择的过滤器；
- options：对话框的一些参数设定，比如只显示文件夹等等，它的取值是`enum QFileDialog::Option`，每个选项可以使用 | 运算组合起来。



这里需要注意一点：我们的代码仅仅是用于演示，很多必须的操作并没有进行。比如，我们没有检查这个文件的实际类型是不是一个文本文件。并且，我们使用了`QTextStream::readAll()`直接读取文件所有内容，如果这个文件有 100M，程序会立刻死掉，这些都是实际程序必须考虑的问题。不过这些内容已经超出我们本章的介绍，也就不再详细说明。

中文乱码问题解读

QTextStream默认按ANSI解读，需要设定utf8方式解读

```
void MainWindow::open()
{
    QString path = QFileDialog::getOpenFileName(this,
                                                tr("Open File"),
                                                "/",
                                                tr("Text Files(*.txt);;Markdown file(*.md)"));
    if(!path.isEmpty())
    {
        //中文乱码 ansi编码
        QFile file(path);
        if(!file.open(QIODevice::ReadOnly | QIODevice::Text)){
            QMessageBox::warning(this,tr("Read File"),tr("can open file:\n%1").arg(path));
            return;
        }
        QTextStream in(&file);
        in.setCodec("UTF-8");//***************************
        textEdit->setText(in.readAll());
        file.close();
    }else{
        QMessageBox::warning(this,tr("Path"),tr("You did not select any file"));

    }


}
```

qt事件

```
//eventlabel.h
#ifndef EVENTLABEL_H
#define EVENTLABEL_H

#include <QLabel>

class EventLabel : public QLabel
{
    Q_OBJECT
public:
    EventLabel(QWidget *parent = nullptr);
    ~EventLabel();
protected:
    void mouseMoveEvent(QMouseEvent *event);
    void mousePressEvent(QMouseEvent *event);
    void mouseReleaseEvent(QMouseEvent *event);
};


#endif // EVENTLABEL_H
```

```
//eventlabel.cpp
#include "eventlabel.h"
#include <QString>
#include <QMouseEvent>

EventLabel::EventLabel(QWidget *parent)
    :QLabel(parent)
{

}

EventLabel::~EventLabel()
{

}

void EventLabel::mouseMoveEvent(QMouseEvent *event)
{
    this->setText(QString("<center><h1>Move: (%1, %2)</h1></center>")
                  .arg(QString::number(event->x()), QString::number(event->y())));
}

void EventLabel::mousePressEvent(QMouseEvent *event)
{
    this->setText(QString("<center><h1>Press: (%1, %2)</h1></center>")
                  .arg(QString::number(event->x()), QString::number(event->y())));
}

void EventLabel::mouseReleaseEvent(QMouseEvent *event)
{
    QString msg;
    msg.sprintf("<center><h1>Release: (%d, %d)</h1></center>",
                event->x(), event->y());
    this->setText(msg);
}

```

```
EventLabel *label = new EventLabel;
label->setWindowTitle("MouseEvent Demo");
label->resize(300, 200);
label->setMouseTracking(true);//鼠标被追踪 默认是false，要点击一次才跟踪
label->show();
```

关闭窗口 事件重写

```
//!!! Qt5
...
textEdit = new QTextEdit(this);
setCentralWidget(textEdit);
connect(textEdit, &QTextEdit::textChanged, [=]() {
    this->setWindowModified(true);
});

setWindowTitle("TextPad [*]");
...

void MainWindow::closeEvent(QCloseEvent *event)
{
    if (isWindowModified()) {
        bool exit = QMessageBox::question(this,
                                      tr("Quit"),
                                      tr("Are you sure to quit this application?"),
                                      QMessageBox::Yes | QMessageBox::No,
                                      QMessageBox::No) == QMessageBox::Yes;
        if (exit) {
            event->accept();
        } else {
            event->ignore();
        }
    } else {
        event->accept();
    }
}
```

`setWindowTitle()`函数可以使用 [*] 这种语法来表明，在窗口内容发生改变时（通过`setWindowModified(true)`函数通知），Qt 会自动在标题上面的 [*] 位置替换成 * 号。我们使用 Lambda 表达式连接`QTextEdit::textChanged()`信号，将`windowModified`设置为 true。然后我们需要重写`closeEvent()`函数。在这个函数中，我们首先判断是不是有过修改，如果有，则弹出询问框，问一下是否要退出。如果用户点击了“Yes”，则接受关闭事件，这个事件所在的操作就是关闭窗口。因此，一旦接受事件，窗口就会被关闭；否则窗口继续保留。当然，如果窗口内容没有被修改，则直接接受事件，关闭窗口。

事件过滤器

```
class MainWindow : public QMainWindow
 {
 public:
     MainWindow();
 protected:
     bool eventFilter(QObject *obj, QEvent *event);
 private:
     QTextEdit *textEdit;
 };

 MainWindow::MainWindow()
 {
     textEdit = new QTextEdit;
     setCentralWidget(textEdit);

     textEdit->installEventFilter(this);
 }

 bool MainWindow::eventFilter(QObject *obj, QEvent *event)
 {
     if (obj == textEdit) {
         if (event->type() == QEvent::KeyPress) {
             QKeyEvent *keyEvent = static_cast<QKeyEvent *>(event);
             qDebug() << "Ate key press" << keyEvent->key();
             return true;
         } else {
             return false;
         }
     } else {
         // pass the event on to the parent class
         return QMainWindow::eventFilter(obj, event);
     }
 }
```

事件总结

Qt 中有很多种事件：鼠标事件、键盘事件、大小改变的事件、位置移动的事件等等。如何处理这些事件，实际有两种选择：

第一，所有事件对应一个事件处理函数，在这个事件处理函数中用一个很大的分支语句进行选择，其代表作就是 win32 API 的`WndProc()`函数：

```
LRESULT CALLBACK WndProc(HWND hWnd,
                          UINT message,
                          WPARAM wParam,
                          LPARAM lParam)
```

在这个函数中，我们需要使用`switch`语句，选择`message`参数的类型进行处理，典型代码是：

```
switch(message) {
     case WM_PAINT:
         // ...
         break;
     case WM_DESTROY:
         // ...
         break;
     ...
}
```

第二，每一种事件对应一个事件处理函数。Qt 就是使用的这么一种机制：

- `mouseEvent()`
- `keyPressEvent()`
- ...

Qt 具有这么多种事件处理函数，肯定有一个地方对其进行分发，否则，Qt 怎么知道哪一种事件调用哪一个事件处理函数呢？这个分发的函数，就是`event()`。显然，当`QMouseEvent`产生之后，`event()`函数将其分发给`mouseEvent()`事件处理器进行处理。

`event()`函数会有两个问题：

1. `QWidget::event()`函数是一个 protected 的函数，这意味着我们要想重写`event()`，必须继承一个已有的类。试想，我的程序根本不想要鼠标事件，程序中所有组件都不允许处理鼠标事件，是不是我得继承所有组件，一一重写其`event()`函数？protected 函数带来的另外一个问题是，如果我基于第三方库进行开发，而对方没有提供源代码，只有一个链接库，其它都是封装好的。我怎么去继承这种库中的组件呢？
2. `event()`函数的确有一定的控制，不过有时候我的需求更严格一些：我希望那些组件根本看不到这种事件。`event()`函数虽然可以拦截，但其实也是接收到了`QMouseEvent`对象。我连让它收都收不到。这样做的好处是，模拟一种系统根本没有那个事件的效果，所以其它组件根本不会收到这个事件，也就无需修改自己的事件处理函数。这种需求怎么办呢？

这两个问题是`event()`函数无法处理的。于是，Qt 提供了另外一种解决方案：事件过滤器。事件过滤器给我们一种能力，让我们能够完全移除某种事件。事件过滤器可以安装到任意`QObject`类型上面，并且可以安装多个。如果要实现全局的事件过滤器，则可以安装到`QApplication`或者`QCoreApplication`上面。这里需要注意的是，如果使用`installEventFilter()`函数给一个对象安装事件过滤器，那么该事件过滤器只对该对象有效，只有这个对象的事件需要先传递给事件过滤器的`eventFilter()`函数进行过滤，其它对象不受影响。如果给`QApplication`对象安装事件过滤器，那么该过滤器对程序中的每一个对象都有效，任何对象的事件都是先传给`eventFilter()`函数。

事件过滤器可以解决刚刚我们提出的`event()`函数的两点不足：首先，事件过滤器不是 protected 的，因此我们可以向任何`QObject`子类安装事件过滤器；其次，事件过滤器在目标对象接收到事件之前进行处理，如果我们将事件过滤掉，目标对象根本不会见到这个事件。

现在我们可以总结一下 Qt 的事件处理，实际上是有五个层次：

1. 重写`paintEvent()`、`mousePressEvent()`等事件处理函数。这是最普通、最简单的形式，同时功能也最简单。
2. 重写`event()`函数。`event()`函数是所有对象的事件入口，`QObject`和`QWidget`中的实现，默认是把事件传递给特定的事件处理函数。
3. 在特定对象上面安装事件过滤器。该过滤器仅过滤该对象接收到的事件。
4. 在`QCoreApplication::instance()`上面安装事件过滤器。该过滤器将过滤所有对象的所有事件，因此和`notify()`函数一样强大，但是它更灵活，因为可以安装多个过滤器。全局的事件过滤器可以看到 disabled 组件上面发出的鼠标事件。全局过滤器有一个问题：只能用在主线程。
5. 重写`QCoreApplication::notify()`函数。这是最强大的，和全局事件过滤器一样提供完全控制，并且不受线程的限制。但是全局范围内只能有一个被使用（因为`QCoreApplication`是单例的）。

为了进一步了解这几个层次的事件处理方式的调用顺序，我们可以编写一个测试代码：

```
class Label : public QWidget
{
public:
    Label()
    {
        installEventFilter(this);
    }

    bool eventFilter(QObject *watched, QEvent *event)
    {
        if (watched == this) {
            if (event->type() == QEvent::MouseButtonPress) {
                qDebug() << "eventFilter";
            }
        }
        return false;
    }

protected:
    void mousePressEvent(QMouseEvent *)
    {
        qDebug() << "mousePressEvent";
    }

    bool event(QEvent *e)
    {
        if (e->type() == QEvent::MouseButtonPress) {
            qDebug() << "event";
        }
        return QWidget::event(e);
    }
};

class EventFilter : public QObject
{
public:
    EventFilter(QObject *watched, QObject *parent = 0) :
        QObject(parent),
        m_watched(watched)
    {
    }

    bool eventFilter(QObject *watched, QEvent *event)
    {
        if (watched == m_watched) {
            if (event->type() == QEvent::MouseButtonPress) {
                qDebug() << "QApplication::eventFilter";
            }
        }
        return false;
    }

private:
    QObject *m_watched;
};

int main(int argc, char *argv[])
{
    QApplication app(argc, argv);
    Label label;
    app.installEventFilter(new EventFilter(&label, &label));
    label.show();
    return app.exec();
}
```

```
QApplication::eventFilter 
eventFilter 
event 
mousePressEvent
```

自定义事件

尽管 Qt 已经提供了很多事件，但对于更加千变万化的需求来说，有限的事件都是不够的。例如，我要支持一种新的设备，这个设备提供一种崭新的交互方式，那么，这种事件如何处理呢？所以，允许创建自己的事件 类型也就势在必行。即便是不说那种非常极端的例子，在多线程的程序中，自定义事件也是尤其有用。当然，事件也并不是局限在多线程中，它可以用在单线程的程序中，作为一种对象间通讯的机制。那么，为什么我需要使用事件，而不是信号槽呢？主要原因是，事件的分发既可以是同步的，又可以是异步的，而函数的调用或者说是槽的回调总是同步的。事件的另外一个好处是，它可以使用过滤器。

事件：异步，同步，过滤器

信号槽：同步