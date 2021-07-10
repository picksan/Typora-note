#### bit位图排序

> 位向量数据结构，

位图常用操作

```c
#include <stdio.h>

#define BITSPERWORD 32 //1个int的bit数
#define SHIFT 5 //log(2)32=5 右移5位=除以32
#define MASK 0x1F //31 0-31刚好表示32个bit
#define N 100 //总的bit数量
unsigned int a[1+N/BITSPERWORD]; //bit存在int数组中

//设置bit位 i的范围[0,N)
void set(unsigned int i)
{//i=[0,N)
    a[i>>SHIFT] |= (1<<(i & MASK));
}
//清除bit位
void clr(unsigned int i)
{
    a[i>>SHIFT] &= ~(1<<(i & MASK));
}
//测试相应bit位是否置位
unsigned int test(unsigned int i)
{
    return a[i>>SHIFT] & (1<<(i & MASK));
}
int main()
{
    unsigned int i,result;
    for(i=0;i<N;i++)
    {
        set(i);
    }
    while(scanf("%d",&i) != EOF){
        result = test(i);
        printf("%#x,%#u,%#o\n",result,result,result);
    }
        
    return 0;
}
```

bit向量排序

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#define BITSPERWORD 32
#define SHIFT 5
#define MASK 0x1F
#define N 1000
int a[1+N/BITSPERWORD];//存储bit向量
int x[N];//输入数组
//设置bit位
void set(int i)
{//i=[0,N)
    a[i>>SHIFT] |= (1<<(i & MASK));
}
//清除bit位
void clr(int i)
{
    a[i>>SHIFT] &= ~(1<<(i & MASK));
}
//测试当前bit位是否置位
int test(int i)
{
    return a[i>>SHIFT] & (1<<(i & MASK));
}
//bit向量排序
void bitsort()
{
    int i;
    for(i = 0;i < N;i++)
    {
        clr(i);
    }
    for(i=0;i<N;i++)
    {
        set(x[i]);
    }
    for(i = 0;i <N;i++)
    {
        if(test(i))
        {
            printf("%d",i);
            putchar(i % 20 == 19 ? '\n' : ' ');
        } 
        
    }
}
//交换
void swap(int *x,int *y)
{
    int temp;
    temp = *x;
    *x = *y;
    *y = temp;
}
//产生 [l,u]之间的随机整数
int randint(int l,int u)
{
    return rand()%(u-l+1)+l;
}
//产生N个，不重复的[0,N)之间的随机整数输入
void randarray()
{
    int i,k=N/2,r;
    
    for(i=0;i<N;i++)
    {
        x[i]=i;
    }
    for(i=0;i<k;i++)
    {
        r = randint(i,N-1);
        swap(&x[i],&x[r]);
    }
    for(i=0;i<N;i++)
    {
        printf("%d",x[i]);
        putchar(i % 20 == 19 ? '\n' : ' ');
    }
}
int main()
{
    //int i;
    srand(time(0));
    randarray();
    bitsort();
    return 0;
}
```