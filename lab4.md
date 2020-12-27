# 实验4：4×4 小键盘的使用

> 小组成员:吕建瑶1811400,郑佶1811464,吴京1811440

## 1. 实验目的

1. 认识键盘矩阵；
2. 学会用行扫描法和行反转法。

## 2. 实验背景

在单片机等小应用系统以及智能设备中，经常使用简单的键盘进行输入操作。这些键盘上的每一个按键或开关实际是一个一位二进制数字量。当按键或开关个数比较少时，可直接将其构成输入端口。但是，如果个数比较多时，仍然按通常 8 个构成一个字节型输入端口的做法，则将占用较多的 I/O 端口。这时，一般将按键排列成矩阵。4×4 键盘矩阵的典型电路图如下图所示：

![](img/lab4_bg.png)

在上图中，有 4 根行线（水平线）和 4 根列线（竖线），行线和列线相交位置上都接有一个按键。这样就构成了一个 4×4 键盘矩阵。不难想象，多行多列可构成更大规模的键盘矩阵。图的右边有一个排电阻，公共端接 VCC，每个电阻的另一端与一根行线或列线相连。这种电阻接法是与识别按键的方法有关。每根行线和列线都安排了一个引脚。为了识别键盘上的闭合键，通常可以采用两种方法：行扫描法和行反转法。

## 3. 实验内容

1. 用 8255A 作接口，用行扫描法识别闭合键。要求程序编成循环结构，不按键时无输出，按下某个键，将其对应的字符显示出来，并且只显示一次。
2. 了解行反转法编程要点。

## 4. 译码电路

## 5. 程序代码

```c
#include <stdio.h>
#include <stdlib.h>
#include <conio.h>
#include <bios.h>
#include <ctype.h>
#include <process.h>

void dis(void);
void key(void);
void clear(void);
void ccscan(void);
void putbuf(void);
void getkey(void);
void delay(int time);

//****************根据查看配置信息修改下列符号值*******************
#define  IOY0         0x3000
//*****************************************************************
#define  MY8255_A     IOY0 + 0x00*2
#define  MY8255_B     IOY0 + 0x01*2
#define  MY8255_C     IOY0 + 0x02*2
#define  MY8255_MODE  IOY0 + 0x03*2

char a[] = {0x3f, 0x06, 0x5b, 0x4f, 0x66, 0x6d, 0x7d, 0x07, 0x7f, 0x6f, 0x77, 0x7c, 0x39, 0x5e, 0x79, 0x71};
char b[] = {0x00, 0x00, 0x00, 0x00, 0x00, 0x00};
int  cc;
int  b_n;
int  n;

void main()
{
	outp(MY8255_MODE, 0x81);
	b_n = 5;
	
	while(1)
	{
		dis();
		clear();
		ccscan();
		if (cc)
		{
			dis();
			delay(0x100);
			delay(0x100);
			clear();
			ccscan();
			if (cc)
			{
				getkey();
			}
		}
		key();
	}
}

void getkey(void)
{
	int i;
	int j = 0xfe;
	
	for(i=0;i<=3;i++)
	{
		outp(MY8255_A, j);
		
		if ( !( (inp(MY8255_C)) & 0x01) )
		{
			n = i + 0;
			putbuf();
			return;
		}
		
		if ( !( (inp(MY8255_C)) & 0x02) )
		{
			n = i + 4;
			putbuf();
			return;
		}
		
		if ( !( (inp(MY8255_C)) & 0x04) )
		{
			n = i + 8;
			putbuf();
			return;
		}
		
		if ( !( (inp(MY8255_C)) & 0x08) )
		{
			n = i + 12;
			putbuf();
			return;
		}
		
		j <<= 1;
	}
}

void ccscan(void)
{
	outp(MY8255_A, 0x00);
	cc = inp(MY8255_C);
	cc = (~cc) & 0x0F;
}

void dis(void)
{
	int i;
	int j = 0xdf;
	
	for(i=0;i<=5;i++)
	{
		outp(MY8255_A, j);
		outp(MY8255_B, a[b[i]]);
		
		delay(0x100);
		j >>= 1;
		j |= 0x80;
	}
}

void clear(void)
{
	outp(MY8255_B, 0x00);
}

void putbuf(void)
{
	b[b_n] = n;
	b_n--;
	if (b_n == -1)
	{
		b_n = 5;
	}
	
	dis();
	clear();
	ccscan();
	while (cc)
	{
		dis();
		clear();
		ccscan();
	}
}

void key(void)
{
	if (bioskey(1) != 0)
	{
		exit(0);
	}
}

void delay(int time)
{
	int i;
	int j;
	for(i=0;i<=time;i++)
	{
		for(j=0;j<=0x100;j++)
		{   }
	}
	return;
}
```

## 6. 系统接线图

![](img/lab4_diagram.jpg)

## 7. 实验连线图

![](img/lab4_result.png)