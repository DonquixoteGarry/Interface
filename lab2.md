# 实验2：手动数字量输入与无条件输入端口的构成

> 小组成员:吕建瑶1811400,郑佶1811464,吴京1811440

## 实验内容

1. 将手动数字量输入模块的输出接 LED 显示模块，拨动开关，观察 LED 的亮灭；
2. 构建无条件输入端口，输入数据由手动数字量输入模块提供，编一程序，读该端口并显示在屏幕上；
3. 设计电路并编写循环程序，在循环体中，先读入无条件输入端口的数值，经过处理后再向无条件输出端口输出，并接 LED 显示模块，要求输入数据与输出数据呈现不同规律。

## 译码电路

![](img/lab2_decode.png)

## 程序代码

```c
#include<stdio.h>
#include<stdlib.h>
#include<conio.h>
#include<bios.h>
#include<ctype.h>
#include<process.h>
void main()
{
	int p = 0;//要读入的数据
	int i, j;
	for (i = 0; i < 1000000; i++)
	{
		p = inp(0x3001);
		printf("%d ", p);
		outp(0x3000, p * 2 + 1);
		for(j = 0; j < 32000; j++)
		{
			;//延迟
		}
	}
}
```

## 系统接线图

![](img/lab2_diagram.jpg)

## 实验连线图

![](img/lab2_result.png)
