# Lectures_Notes_for_Cplusplus

<p align="center">
    <a href="https://github.com/elegantcoin/Lectures_Notes_for_Cplusplus"><img src="https://img.shields.io/badge/status-updating-brightgreen.svg"></a>
    <a href="https://github.com/python/cpython"><img src="https://img.shields.io/badge/Python-3.7-FF1493.svg"></a>
    <a href="https://github.com/elegantcoin/Lectures_Notes_for_Cplusplus"><img src="https://img.shields.io/badge/platform-Windows%7CLinux%7CmacOS-660066.svg"></a>
    <a href="https://opensource.org/licenses/mit-license.php"><img src="https://badges.frapsoft.com/os/mit/mit.svg"></a>
    <a href="https://github.com/elegantcoin/Lectures_Notes_for_Cplusplus/stargazers"><img src="https://img.shields.io/github/stars/elegantcoin/Lectures_Notes_for_Cplusplus.svg?logo=github"></a>
    <a href="https://github.com/elegantcoin/Lectures_Notes_for_Cplusplus/network/members"><img src="https://img.shields.io/github/forks/elegantcoin/Lectures_Notes_for_Cplusplus.svg?color=blue&logo=github"></a>
    <a href="https://www.python.org/"><img src="https://upload.wikimedia.org/wikipedia/commons/c/c3/Python-logo-notext.svg" align="right" height="48" width="48" ></a>
</p>
<br />

## :fire: 1. GetMemory的基本用法

```C++
    //http://blog.sina.com.cn/s/blog_71af112a0101iao5.html
	//https://www.cnblogs.com/alinh/p/9636500.html
	//GetMemory的基本用法
	//	★NO.1
	void GetMemory(char *p)
	{
		p = (char *)malloc(100);
	}

	void Test(void)
	{
		char *str = NULL;
		GetMemory(str);
		strcpy(str, hello world);
		printf(str);
	}
	/*请问运行Test函数后会是什么样的结果？

	过程：程序首先申请一个char类型的指针str,并把str指向NULL(即str里存的是NULL的地址，*str为NULL中的值为0)，调用函数的过程中做了如下动作：
	1.申请一个char 类型的指针p，
	2.把str的内容copy到了p里(这是参数传递过程中系统所做的),
	3.为p指针申请了100个空间，
	4.返回Test函数．最后程序把字符串hello world拷贝到str指向的内存空间里．
	到这里错误出现了! str的空间始终为NULL而并没有实际的空间．深刻理解函数调用的第2步，将不难发现问题所在!

	分 析：由于void GetMemory(char *p)中的*p实际上是主函数中的一个str的一个副本，编译器总是要为函数的每个参数制作临时副本。
	在本题中，p申请了新的内存，只是把p把指的内存 地址改变了，但str丝毫未变。因为GetMemory没有返回值，因此str并不指向p所申请的那段内存，
	所以函数GetMemory并不能输出任何东 西。事实上，每执行一次GetMemory就会申请一块内存，但申请的内存却不能有效释放，结果是内存一直被独占，最终造成内存泄露。
	答案：程序崩溃。因为GetMemory并不能传递动态内存，Test函数中的str一直都是NULL。*/

	//★NO.2
	char *GetMemory(void)
	{
		char p[] = hello world;
		retrun p;
	}

	void Test(void)
	{
		char *str = NULL;
		str = GetMemory();
		printf(str);
	}
	/*请问运行Test函数后会是什么样的结果？

	过程：程序首先申请一个char类型的指针str,并把str指向NULL．调用函数的过程中做了如下动作：
	1.申请一数组p[]并将其赋值为hello world(数组的空间大小为12)，
	2. 返回数组名p付给str指针(即返回了数组的首地址)．那么这样就可以打印出字符串"hello world"了么? 当然是不能的! 
	因为在函数调用的时候漏掉了最后一步．也就是在第2步return数组名后，函数调用还要进行一步操作，也就是释放内存空间．
	当一个函数被调用结束后它会 释放掉它里面所有的变量所占用的空间．所以数组空间被释放掉了，
	也就是说str所指向的内容将不确定是什么东西．*/

	/*★NO.3*/
	void GetMemory2(char **p, int num)
	{
		*p = (char *)malloc(num);
	}

	void Test(void)
	{
		char *str = NULL;
		GetMemory(&str, 100);
		strcpy(str, hello);
		printf(str);
	}
	/*请问运行Test函数后会是什么样的结果？

		正确答案为可以打印出hello．但内存泄漏了！
		因为在GetMemory()中使用了malloc申请内存，但是在最后却没有对申请的内存没有做
		任何的处理，因此可能导致内存的泄露，非常的危险。*/

	//★NO.4
	void Test(void)
	{
		char *str = (char *)malloc(100);
		strcpy(str, hello);
		free(str);
		if (str != NULL)
		{
			strcpy(str, world);
			printf(str);
		}
	}

	void GetMemory1(char *p)
	{
		p = (char *)malloc(100);
	}

	void Test1(void)
	{
		char *str = NULL;
		GetMemory1(str);
		strcpy(str, "hello world");
		printf(str);
	}
	//GetMemory1()并不能申请到内存，这和第1个例子一样，也是形参实参的问题没有把握正确。
	//同样都没有释放内存的过程//str一直是空，程序崩溃

	char *GetMemory2(void)
	{
		char p[] = "hello world";
		return p;
	}
	//返回的数据被释放了，可能会出现不一样的数据，但是地址是固定的，也就是存在一个起始地址

	void Test2(void)
	{
		char *str = NULL;
		str = GetMemory2();
		printf(str);
	}

	char *GetMemory3(void)
	{
		return "hello world";
	}

	void Test3(void)
	{
		char *str = NULL;
		str = GetMemory3();
		printf(str);
	}
	//Test3 中打印hello world，因为返回常量区，而且并没有被修改过。Test2中不一定能打印出hello world，因为指向的是栈。

	void GetMemory4(char **p, int num)
	{
		*p = (char *)malloc(num);
	}

	void Test4(void)
	{
		char *str = NULL;
		GetMemory3(&str, 100);
		strcpy(str, "hello");
		printf(str); //没有free来释放内存
	}
	//内存没释放

	void Test5(void)
	{
		char *str = (char *)malloc(100);
		strcpy(str, "hello");
		free(str); //str = NULL,避免野指针的产生
		if (str != NULL)

		{
			strcpy(str, "world");
			printf(str);
		}
	}
	//str为野指针，打印的结果不得而知

	void Test6()
	{
		char *str = (char *)malloc(100);
		strcpy(str, "hello");
		str += 6;
		free(str);
		if (str != NULL)

		{
			strcpy(str, "world");
			printf(str);
		}
	}
	//VC断言失败，运行错误

	//过 程：申请空间，拷贝字符串，释放空间．前三步操作都没有任何问题．到if语句里的判断条件开始出错了，
	//因为一个指针被释放之后其内容并不是NULL，而是 一个不确定的值．所以if语句永远都能被执行．
	//这也是著名的"野"指针问题．所以我们在编写程序释放一个指针之后一定要人为的将指针付成NULL．
	//这样就会避免出现"野"指针的出现．有人说"野"指针很可怕，会带来意想不到的错误．
```
