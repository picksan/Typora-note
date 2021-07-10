QT绘图系统

Qt 的绘图系统允许使用相同的 API 在屏幕和其它打印设备上进行绘制。整个绘图系统基于`QPainter`，`QPainterDevice`和`QPaintEngine`三个类。

`QPainter`用来执行绘制的操作；`QPaintDevice`是一个二维空间的抽象，这个二维空间允许`QPainter`在其上面进行绘制，也就是`QPainter`工作的空间；`QPaintEngine`提供了画笔（`QPainter`）在不同的设备上进行绘制的统一的接口。`QPaintEngine`类应用于`QPainter`和`QPaintDevice`之间，通常对开发人员是透明的。除非你需要自定义一个设备，否则你是不需要关心`QPaintEngine`这个类的。我们可以把`QPainter`理解成画笔；把`QPaintDevice`理解成使用画笔的地方，比如纸张、屏幕等；而对于纸张、屏幕而言，肯定要使用不同的画笔绘制，为了统一使用一种画笔，我们设计了`QPaintEngine`类，这个类让不同的纸张、屏幕都能使用一种画笔。

```
//重写绘图事件
void MainWindow::paintEvent(QPaintEvent *)
{
    QPainter painter(this);
    painter.drawLine(80, 100, 650, 500);
    painter.setPen(Qt::darkCyan);
    painter.drawRect(10, 10, 100, 400);//矩形
    painter.setPen(QPen(Qt::green, 5));
    painter.setBrush(Qt::blue);//刷子
    painter.drawEllipse(50, 150, 400, 200);//椭圆

}
```

前面一章我们提到，Qt 绘图系统定义了两个绘制时使用的关键属性：画刷和画笔。前者使用`QBrush`描述，大多用于填充；后者使用`QPen`描述，大多用于绘制轮廓线。



`QBrush`定义了`QPainter`的填充模式，具有样式、颜色、渐变以及纹理等属性。

画刷的`style()`定义了填充的样式，使用`Qt::BrushStyle`枚举，默认值是`Qt::NoBrush`，也就是不进行任何填充。我们可以从下面的图示中看到各种填充样式的区别：