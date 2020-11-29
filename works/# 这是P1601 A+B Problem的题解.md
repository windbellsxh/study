# 这是P1601 A+B Problem的题解

  #### windbell
~~第一次用这个东西感觉好好玩~~

![image-20201111145438973](C:\Users\p\AppData\Roaming\Typora\typora-user-images\image-20201111145438973.png)

（先附上作业要求的截图）

------
#### 一开始想到了用之前第一期招新任务用的字符串方法，结果编译没通过..
这里贴上之前的代码
```C
#include<stdio.h>
#include<string.h> 
int main(){
	char a[500],b[500],temp[500],c[500];
	int i,d;
	scanf("%s %s",&a[0],&b[0]);
	if(strlen(a)<strlen(b))
	{
		strcpy(temp,a);
		strcpy(a,b);
		strcpy(b,temp);
	}
	d=strlen(a)-strlen(b);
	for(i=0;i<=strlen(a)-1;i++)
	{
		c[i]='0';
	}
	
	strcpy(a,strrev(a));
	strcpy(b,strrev(b));
	
	for(i=strlen(b);i<=strlen(a);i++)
	{
		b[i]='0';
	}
	for(i=0;i<=strlen(a)-1;i++)
	{
		c[i]=c[i]+a[i]+b[i]-96;
		if(c[i]>=58)
			{
			c[i]=c[i]-10;
			c[i+1]='1';
			}
	}
	printf("%s\n",strrev(c));
	
	return 0;
}

```

------

#### 既然如此，那就不使用字符串函数简单模拟一下

这边讲一下大致思路，将两个数字存入两个字符数组中
a: 1 2 3 4 5 6 7
b: 1 2 3 4 5
从长度长的那个数字的末尾开始，通过ASCII码的原理模拟加法的计算，存入一个int数组中
a: 1 2 3 4 5 6 7
          ^
b: 1 2 3 4 5-----------------> ans: 0 0 4 6 9 1 2
    ^
当一个数组运算完毕后，依照相似原则计算多余数字。

a: 1 2 ----------------->ans: 1 2 4 6 9 1 2    over

最后依照int数组的顺序依次输出其中的数字

------

#### input
两个数字的输入，这里采用了字符数组的方式
采用idx1,idx2来统计字符的个数

```c
	int idx1=0,idx2=0;
	char num1[600],num2[600];
	scanf("%s %s",num1,num2);/*使用scanf("%s")的形式操作更方便*/
	while(num1[idx1]!='\0')
	{
		idx1++;
	}
	while(num2[idx2]!='\0')
	{
		idx2++;
	}
	idx1--;idx2--;/*方便起见，将两个变量减一，使得之后可以直接用作下标*/
```
------

#### 计算部分

因为在处理过程中发现有很多重复的步骤出现，所以统一使用空函数和指针参数的形式来编写。
1. 主计算函数
```C
void cal(int *n,char *c,int *cord)
{
	*n+=*c-'0'+*cord;
	*cord=0;
}
```
函数原理很简单,数字字符减去'0'就是其数字
这里cord用来判断是否进位,无论是否进位cord都将在这次运算结束后归零
2. 判断进位函数
```c
void judge(int *n,int *cord)
{
	if(*n>9)
	{
		*n-=10;
		*cord=1; 
	}
}
```
判断所得数字是否大于10，若大于则进1(记cord变量为1)

------

#### output
一个for循环
此时要判断一个特殊情况，开头数字是否为0，若为0则需将循环变量初始值+1，不输出0
下面为程序代码
```c
#include<stdio.h>
int max(int a,int b);
void judge(int *n,int *cord);
void cal(int *n,char *c,int *cord);
/*函数声明*/
int main(int argv,char *argc[]){
	char num1[600],num2[600];
	scanf("%s %s",num1,num2);/*使用scanf("%s")的形式操作更方便*/
	int sti,idx1=0,idx2=0,ans[600];    
	for(sti=0;sti<600;sti++){
		ans[sti]=0;
	}
	while(num1[idx1]!='\0')
	{
		idx1++;
	}
	while(num2[idx2]!='\0')
	{
		idx2++;
	}
	idx1--;
	idx2--;/*方便起见，将两个变量减一，使得之后可以直接用作下标*/
	int i,j,cord=0,h=max(idx1,idx2)+1;/*这边特意给ans留了一个空位，以便特殊情况处理*/
	for(i=idx1,j=idx2;i>=0&&j>=0;h--,j--,i--)
	{
		cal(&ans[h],&num1[i],&cord);
		cal(&ans[h],&num2[j],&cord);
		judge(&ans[h],&cord);/*逐位计算,其实这里第二行的cord没什么意义，必然为0*/
	}
	int endi;
	if(j<0){
		for(endi=i;endi>=0;h--,endi--)
		{
			cal(&ans[h],&num1[endi],&cord);
			judge(&ans[h],&cord);
		}
	}
	if(i<0){
		for(endi=j;endi>=0;h--,endi--)
		{
			cal(&ans[h],&num2[endi],&cord);
			judge(&ans[h],&cord);
		}
	}
	if(cord==1)/*特殊情况判断，计算完后有无进位，有则在首部加一*/
	{
		ans[0]=1;
		i=0;
	}
	else
		i=1;
	for(i;i<=max(idx1,idx2)+1;i++){/*计算完毕，依次输出*/
		printf("%d",ans[i]);
	}
	return 0;
}	
/*自定义函数部分*/
int max(int a,int b){
	if(a>=b)
	return a;
	else
	return b;
}
void judge(int *n,int *cord)
{
	if(*n>9)
	{
		*n-=10;
		*cord=1; 
	}
}
void cal(int *n,char *c,int *cord)
{
	*n+=*c-'0'+*cord;
	*cord=0;
}
```
### 结束啦~








