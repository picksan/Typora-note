指针

- 在指针声明时，* 号表示所声明的变量为指针
- 在指针使用时，* 号表示操作指针所指向的内存空间

1. 相当通过地址(指针变量的值)找到指针指向的内存，再操作内存

2. 放在等号的左边赋值（给内存赋值，写内存）

3.  放在等号的右边取值（从内存中取值，读内存）

   ```c
   //解引用
   void test01(){
   
   	//定义指针
   	int* p = NULL;
   	//指针指向谁，就把谁的地址赋给指针
   	int a = 10;
   	p = &a;
   	*p = 20;//*在左边当左值，必须确保内存可写
   	//*号放右面，从内存中读值
   	int b = *p;
   	//必须确保内存可写
   	char* str = "hello world!";
   	*str = 'm';
   
   	printf("a:%d\n", a);
   	printf("*p:%d\n", *p);
   	printf("b:%d\n", b);
   }
   
   ```

   

配置文件读写

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
struct ConfigInfo
{
	char key[64];
	char value[64];
};

//获取文件有效行数
int getFileLine(const char* filePath)
{
	FILE* file = fopen(filePath, "r");
	char buf[1024] = { 0 };
	int lines = 0;
	while (fgets(buf, 1024, file) != NULL)
	{
		if (isValidLine(buf))
		{
			lines++;
		}
		memset(buf, 0, 1024);
	}

	fclose(file);

	return lines;

}
//解析文件
void parseFile(const char* filePath, int lines, struct ConfigInfo** configInfo)
{

	struct ConfigInfo* pConfig = (struct ConfigInfo*)malloc(sizeof(struct ConfigInfo) * lines);

	if (pConfig == NULL)
	{
		return;
	}



	FILE* file = fopen(filePath, "r");
	char buf[1024] = { 0 };

	int index = 0;
	while (fgets(buf, 1024, file) != NULL)
	{
		if (isValidLine(buf))
		{
			//解析数据到struct ConfigInfo中
			memset(pConfig[index].key, 0, 64);
			memset(pConfig[index].value, 0, 64);
			char* pos = strchr(buf, ':');
			strncpy(pConfig[index].key, buf, pos - buf);
			strncpy(pConfig[index].value, pos + 1, strlen(pos + 1) - 1); // 从第二个单词开始截取字符串，并且不截取换行符
			//printf("key = %s\n", pConfig[index].key);
			//printf("value = %s\n", pConfig[index].value);
			index++;
		}
		memset(buf, 0, 1024);
	}



	*configInfo = pConfig;

}

//获取指定的配置信息
char* getInfoByKey(char* key, struct ConfigInfo* configInfo, int lines)
{
	for (int i = 0; i < lines; i++)
	{
		if (strcmp(key, configInfo[i].key) == 0)
		{
			return configInfo[i].value;
		}
	}
	return NULL;
}

//释放配置文件信息
void freeConfigInfo(struct ConfigInfo* configInfo)
{
	free(configInfo);
	configInfo = NULL;
}

//判断当前行是否为有效行
int isValidLine(char* buf)
{
	if (buf[0] == '0' || buf[0] == '\0' || strchr(buf, ':') == NULL)
	{
		return 0;// 如果行无限 返回假
	}
	return 1;
}

int main() {

	char* filePath = "./config.txt";
	int lines = getFileLine(filePath);
	printf("文件有效行数为：%d\n", lines);

	struct ConfigInfo* config = NULL;
	parseFile(filePath, lines, &config);

	printf("heroId = %s\n", getInfoByKey("heroId", config, lines));
	printf("heroName: = %s\n", getInfoByKey("heroName", config, lines));
	printf("heroAtk = %s\n", getInfoByKey("heroAtk", config, lines));
	printf("heroDef: = %s\n", getInfoByKey("heroDef", config, lines));
	printf("heroInfo: = %s\n", getInfoByKey("heroInfo", config, lines));


	freeConfigInfo(config);
	config = NULL;

	system("pause");
	return EXIT_SUCCESS;
}

```

