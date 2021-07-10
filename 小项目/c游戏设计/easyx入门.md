easyx入门

```c++
#include <graphics.h>
#include <conio.h>
int main()
{
	return 0;
}
```

简单示例

```c++
#include <graphics.h>
#include <conio.h>
int main()
{
	initgraph(640, 480);//初始化640*480的画布
	setcolor(YELLOW);//圆的线条为黄色
	setfillcolor(GREEN);//圆内以绿色填充
	fillcircle(100, 100, 20);//画圆,圆心为(100,100),半径为20
	getch();//按任意键继续
	closegraph();//关闭图形界面
	return 0;
}
```

画围棋棋盘

```c++
#include <graphics.h>
#include <conio.h>
int main()
{
	int step = 30;//步长
	initgraph(600, 600);//初始化绘图窗口
	setbkcolor(YELLOW);//设置背景色为黄色
	cleardevice();//用背景色清空屏幕
	
	setlinestyle(PS_SOLID, 2);//画实线，宽度为2个像素点
	setcolor(RGB(0, 0, 0));//设置为黑色

	int i;
	for (i = 1; i <= 19; i++) {
		line(i * step, 0, i * step, 600);
		line(0, i * step, 600, i * step);
	}

	getch();//按任意键继续
	closegraph();//关闭图形界面
	return 0;
}
```

动画，移动的圆

```c++
#include <graphics.h>
#include <conio.h>
#include <Windows.h>
int main()
{
	initgraph(640, 480);//初始化绘图窗口
	
	for (int x = 100; x < 540; x += 20) {
		//绘制黄线，绿色填充的圆
		setcolor(YELLOW);
		setfillcolor(GREEN);
		fillcircle(x, 100, 20);
		Sleep(200);
		//绘制黑线黑色填充的圆
		setcolor(BLACK);
		setfillcolor(BLACK);
		fillcircle(x, 100, 20);
	}
	getch();//按任意键继续
	closegraph();//关闭图形界面
	return 0;
}
```

反弹的小球

```c++
#include <graphics.h>
#include <conio.h>
#include <Windows.h>

const int high = 480;
const int width = 640;

int main()
{
	float ball_x, ball_y;//小球坐标
	float ball_vx, ball_vy;//小球的速度
	float radius;//小球的半径

	initgraph(width, high);//初始化绘图窗口
	
	ball_x = width / 2;
	ball_y = high / 2;
	ball_vx = 1;
	ball_vy = 1;
	radius = 20;

	while(1){
		//绘制黑线黑色填充的圆
		setcolor(BLACK);
		setfillcolor(BLACK);
		fillcircle(ball_x, ball_y, radius);

		//更新小圆的坐标
		ball_x += ball_vx;
		ball_y += ball_vy;

		if (ball_x <= radius || ball_x >= width - radius) {
			ball_vx = -ball_vx;
		}
		if (ball_y <= radius || ball_y >= high - radius) {
			ball_vy = -ball_vy;
		}

		//绘制黄线，绿色填充的圆
		setcolor(YELLOW);
		setfillcolor(GREEN);
		fillcircle(ball_x, ball_y, radius);
		Sleep(3);
	}
	getch();//按任意键继续
	closegraph();//关闭图形界面
	return 0;
}
```

两个函数

```
BeginBatchDraw();
使用后，后面执行的绘图操作暂时不输出到屏幕
FlushBatchDraw();
用于执行未完成的绘制任务，进行批量绘制
两个配合使用能够减少画面闪烁
```

减少画面闪烁的反弹运动小球

```c++
#include <graphics.h>
#include <conio.h>
#include <Windows.h>

const int high = 480;
const int width = 640;

int main()
{
	float ball_x, ball_y;//小球坐标
	float ball_vx, ball_vy;//小球的速度
	float radius;//小球的半径

	initgraph(width, high);//初始化绘图窗口
	
	ball_x = width / 2;
	ball_y = high / 2;
	ball_vx = 1;
	ball_vy = 1;
	radius = 20;

	BeginBatchDraw();
	while(1){
		//绘制黑线黑色填充的圆
		setcolor(BLACK);
		setfillcolor(BLACK);
		fillcircle(ball_x, ball_y, radius);

		//更新小圆的坐标
		ball_x += ball_vx;
		ball_y += ball_vy;

		if (ball_x <= radius || ball_x >= width - radius) {
			ball_vx = -ball_vx;
		}
		if (ball_y <= radius || ball_y >= high - radius) {
			ball_vy = -ball_vy;
		}

		//绘制黄线，绿色填充的圆
		setcolor(YELLOW);
		setfillcolor(GREEN);
		fillcircle(ball_x, ball_y, radius);
		
		FlushBatchDraw();
		Sleep(3);
	}
	EndBatchDraw();
	closegraph();//关闭图形界面
	return 0;
}
```

多个小球弹射

```c++
#include <graphics.h>
#include <conio.h>
#include <Windows.h>

const int high = 480;
const int width = 640;
const int ballnum = 6;
int main()
{
	float ball_x[ballnum], ball_y[ballnum];//小球坐标
	float ball_vx[ballnum], ball_vy[ballnum];//小球的速度
	float radius;//小球的半径
	int i,j;
	
	radius = 20;
	for (i = 0; i < ballnum; i++) {
		
		ball_x[i] = (i+2)*radius*3;
		ball_y[i] = high/2;
		ball_vx[i] = i;
		ball_vy[i] = i;
	}
	
	initgraph(width, high);//初始化绘图窗口
	BeginBatchDraw();
	while(1){
		//绘制黑线黑色填充的圆
		setcolor(BLACK);
		setfillcolor(BLACK);
		for (i = 0; i < ballnum; i++) {
			fillcircle(ball_x[i], ball_y[i], radius);
		}
		//更新小圆的坐标
		for (i = 0; i < ballnum; i++) {
			ball_x[i] += ball_vx[i];
			ball_y[i] += ball_vy[i];
			if (ball_x[i] < radius) {
				ball_x[i] = radius;
			}
			if (ball_y[i] < radius) {
				ball_y[i] = radius;
			}
			if (ball_x[i] > width - radius) {
				ball_x[i] = width - radius;
			}
			if (ball_y[i] > width - radius) {
				ball_y[i] = width - radius;
			}
		}
		
		//判断是否和墙碰撞
		for (i = 0; i < ballnum; i++) {
			if (ball_x[i] <= radius || ball_x[i] >= width - radius) {
				ball_vx[i] = -ball_vx[i];
			}
			if (ball_y[i] <= radius || ball_y[i] >= high - radius) {
				ball_vy[i] = -ball_vy[i];
			}
		}
		
		float minDistances2[ballnum][2];//记录某个小球和它最近的小球的距离，以及那个小球的下标
		for (i = 0; i < ballnum; i++) {
			minDistances2[i][0] = 9999999;
			minDistances2[i][1] = -1;
		}

		//求所有小球两两之间的距离的平方
		for (i = 0; i < ballnum; i++) {
			for (j = 0; j < ballnum; j++) {
				if (i != j) {
					float dist2;
					dist2 = (ball_x[i] - ball_x[j]) * (ball_x[i] - ball_x[j])
						+ (ball_y[i] - ball_y[j]) * (ball_y[i] - ball_y[j]);
					if (dist2 < minDistances2[i][0]) {
						minDistances2[i][0] = dist2;
						minDistances2[i][1] = j;
					}
				}
			}
		}

		//判断球是否碰撞
		for (i = 0; i < ballnum; i++) {
			if (minDistances2[i][0] <= 4 * radius * radius) { //若最小距离小于阈值，发生碰撞
				j = minDistances2[i][1];
				//交换速度
				int temp;
				temp = ball_vx[i]; ball_vx[i] = ball_vx[j]; ball_vx[j] = temp;
				temp = ball_vy[i]; ball_vy[i] = ball_vy[j]; ball_vy[j] = temp;

				minDistances2[j][0] = 9999999; // 避免交换两次速度
				minDistances2[i][1] = -1;
			}
		}

		//绘制黄线，绿色填充的圆
		setcolor(YELLOW);
		setfillcolor(GREEN);
		for (i = 0; i < ballnum; i++) {
			fillcircle(ball_x[i], ball_y[i], radius);
		}

		FlushBatchDraw();
		Sleep(3);
	}
	EndBatchDraw();
	closegraph();//关闭图形界面
	return 0;
}
```

时钟秒针

```c++
#include <graphics.h>
#include <conio.h>
#include <math.h>

const int high = 480;
const int width = 640;
const float pi = 3.14159;

int main()
{
	initgraph(width, high); // 初始化绘图窗口
	int center_x, center_y; // 中心点坐标 秒针的起点
	center_x = width / 2;
	center_y = high / 2;
	int secondLength; // 秒针的长度
	secondLength = width / 5;

	int secondEnd_x, secondEnd_y;//秒针的终点

	float secondAngle = 0;//秒针对应的角度
	int second = 0;
	while (1) {
		//12点方向开始
		secondEnd_y = center_y - secondLength * cos(secondAngle);
		secondEnd_x = center_x + secondLength * sin(secondAngle);

		setlinestyle(PS_SOLID, 2);//画实线，宽度为两个像素
		setcolor(WHITE);
		line(center_x, center_y, secondEnd_x, secondEnd_y); //画秒针
		Sleep(100);

		setcolor(BLACK);
		line(center_x, center_y, secondEnd_x, secondEnd_y); //隐藏前一帧的秒针

		//秒针角度的变化
		secondAngle = secondAngle + 2 * pi / 60;
	}
	getch();
	closegraph();
	return 0;
}
```

完整钟表

```c++
#include <graphics.h>
#include <Windows.h>
//#include <conio.h>
#include <math.h>

const int high = 480;
const int width = 640;
const float pi = 3.14159;

int main()
{
	initgraph(width, high); // 初始化绘图窗口
	int center_x, center_y; // 中心点坐标 秒针的起点
	center_x = width / 2;
	center_y = high / 2;
	int secondLength; // 秒针的长度
	int minuteLength; //分针的长度
	int hourLength; //时针的长度

	secondLength = width / 5;
	minuteLength = width / 6;
	hourLength = width / 7; //时针最短

	int secondEnd_x, secondEnd_y;//秒针的终点
	int minuteEnd_x, minuteEnd_y;//分针的终点
	int hourEnd_x, hourEnd_y;//秒针的终点
	float secondAngle = 0;//秒针对应的角度
	float minuteAngle = 0;
	float hourAngle = 0;
	
	int radius = width / 4;
	SYSTEMTIME ti; //定义变量保存当前时间
	BeginBatchDraw();
	while (1) {
		//画表盘
		setlinestyle(PS_SOLID, 1);
		setcolor(WHITE);
		circle(center_x, center_y, radius);
		//画刻度
		int x, y, i;
		for (i = 0; i < 60; i++) {
			x = center_x + int(width / 4.3 * sin(pi * 2 * i / 60));
			y = center_y - int(width / 4.3 * cos(pi * 2 * i / 60));
			if (i % 15 == 0) { //每15分钟的刻度
				bar(x - 5, y - 5, x + 5, y + 5);//画无边框填充矩形
			}
			else if (i % 5 == 0) { //每1小时的刻度
				circle(x, y, 3);//画圆
			}
			else { //每分钟的刻度
				putpixel(x, y, WHITE);//画点
			}
		}
		TCHAR title[] = _T("我的时钟");
		outtextxy(center_x - 25, center_y + width / 6, title);//指定位置输出文字

		GetLocalTime(&ti);//获取当前时间
		//秒针角度的变化
		secondAngle = ti.wSecond * 2 * pi / 60;
		minuteAngle = ti.wMinute * 2 * pi / 60;
		hourAngle = ti.wHour * 2 * pi / 12;
		//12点方向开始
		secondEnd_x = center_x + secondLength * sin(secondAngle);
		secondEnd_y = center_y - secondLength * cos(secondAngle);

		minuteEnd_x = center_x + minuteLength * sin(minuteAngle);
		minuteEnd_y = center_y - minuteLength * cos(minuteAngle);

		hourEnd_x = center_x + hourLength * sin(hourAngle);
		hourEnd_y = center_y - hourLength * cos(hourAngle);

		setlinestyle(PS_SOLID, 2);//画实线，宽度为两个像素
		setcolor(WHITE);
		line(center_x, center_y, secondEnd_x, secondEnd_y); //画秒针
		
		setlinestyle(PS_SOLID, 4);//画实线，宽度为两个像素
		setcolor(BLUE);
		line(center_x, center_y, minuteEnd_x, minuteEnd_y); // 画分针

		setlinestyle(PS_SOLID, 6);//画实线，宽度为两个像素
		setcolor(RED);
		line(center_x, center_y, hourEnd_x, hourEnd_y);
		
		FlushBatchDraw();
		Sleep(10);

		setcolor(BLACK);
		line(center_x, center_y, secondEnd_x, secondEnd_y); //隐藏前一帧的秒针
		line(center_x, center_y, minuteEnd_x, minuteEnd_y); //隐藏分钟
		line(center_x, center_y, hourEnd_x, hourEnd_y);//隐藏秒针
		
	}
	EndBatchDraw();
	//getch();
	closegraph();
	return 0;
}
```

