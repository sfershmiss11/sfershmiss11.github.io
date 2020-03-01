---
title: C语言学习-小游戏制作
tags: [C]
comments: true
---

前几天在知乎上看到一篇文章，了解到了一个通过制作小游戏来学习C语言的书《c语言课程设计与游戏开发实践教程》，很感兴趣就买来学习一下。本帖为学习记录过程。
<!--more-->
## 自动弹跳的小球
```c
#include <stdio.h>
#include <stdlib.h>		//system 
#include <windows.h>	//Sleep 
int main()
{
	int i,j;
	int x=5;
	int y=10;
	
	int left=0;
	int right=20;
	int velocity_x=1;
	int velocity_y=1;
	int top=0;
	int bottom=10;
	
	while(1)
	{
		x=x+velocity_x;
		y=y+velocity_y;
		
		system("cls");	//清屏 
		for(i=0;i<x;i++)
		{
			printf("\n");
		}
		for(j=0;j<y;j++)
		{
			printf(" ");
		}
		printf("o");	//小球 
		printf("\n");
		Sleep(80);		//执行间隔时间，参数为毫秒 
		
		if(x==top||x==bottom)	//撞到上下的边界 
			velocity_x=-velocity_x;
		if(y==left||y==right)	//撞到左右的边界 
			velocity_y=-velocity_y;
	}
	return 0;
}
```
sleep语句也可以用循环语句来实现同样的效果。
```c
for(n=0;n<20000;n++)
		{
			for(m=0;m<2000;m++)
			{
				k++;
			}
		}		//增加执行间隔时间 
```
## 飞机游戏

```c
#include <stdio.h>
#include <stdlib.h>
#include <conio.h>
#include <windows.h>	//gotoxy 

int position_x,position_y;		//飞机位置 
int hight,width;		//游戏画面大小 
int bullet_x,bullet_y;		//子弹位置 
int enemy_x,enemy_y;		//敌机的位置 
int score;		//得分 

void gotoxy(int x,int y)		//清屏函数 
{
	HANDLE handle=GetStdHandle(STD_OUTPUT_HANDLE);
	COORD pos;
	pos.X=x;
	pos.Y=y;
	SetConsoleCursorPosition(handle,pos);
}

void HideCursor()
{
	CONSOLE_CURSOR_INFO cursor_info={1,0};	//第二个值为0隐藏光标
	SetConsoleCursorInfo(GetStdHandle(STD_OUTPUT_HANDLE),&cursor_info); 
}

void startup()
{
	hight=20;
	width=30; 
	position_x=hight/2;
	position_y=width/2;
	bullet_x=-1;
	bullet_y=position_y;
	enemy_x=0;
	enemy_y=position_y;
	score=0;
}

void show()
{
	gotoxy(0,0);	//清屏，可解决画面闪烁 
	
	int i,j;
	for(i=0;i<hight;i++)
	{
		for(j=0;j<width;j++)
		{
			if((i==position_x)&&(j==position_y))
			{
				printf("*");
			}else if((i==enemy_x)&&(j==enemy_y))
			{
				printf("@");
			}else if((i==bullet_x)&&(j==bullet_y))
			{
				printf("|");		//输出子弹 
			}else{
				printf(" ");
			}
		}
		printf("\n");
	}
	printf("得分：%d\n",score);
}

void updateWithoutInput()		//与用户输入无关的 
{
	if(bullet_x>-1)		//子弹飞行轨迹 
	{
		bullet_x--;
	}
	
	if((bullet_x==enemy_x)&&(bullet_y==enemy_y))		//打中飞机 
	{
		score++;
		enemy_x=0;		//产生新飞机 
		enemy_y=rand()%width;		//随机生成 
		bullet_x=0;
	}
	if(enemy_x>hight)		//敌机跑出边界 
	{
		enemy_x=0;
		enemy_y=rand()%width;
	}
	
	static int speed=0;		//控制敌机向下的移动速度，隔几次循环才移动一次 
	if(speed<10)
		speed++;
	if(speed==10)
	{
		enemy_x++;
		speed=0;
	}
}

void updateWithInput()
{
	char input;
	if(kbhit())			//有输入返回1，无输入返回0 
		{
		
			input=getch();		//判断对飞机进行的操作，不用输入回车 
			if(input=='a')
			{
				position_y--;
			}
			if(input=='d')
			{
				position_y++;
			}
			if(input=='w')
			{
				position_x--;
			}
			if(input=='s')
			{
				position_x++;
			}
			if(input==' ')
			{
				bullet_x=position_x-1;
				bullet_y=position_y;
			}
		
		}
}



int main()
{
	HideCursor();
	startup();		//初始化 
	while(1)
	{
		show();		//显示画面 
		updateWithoutInput();		//与用户输入无关的更新 
		updateWithInput();		//与用户输入有关的更新 
	}
	return 0;
}
```
