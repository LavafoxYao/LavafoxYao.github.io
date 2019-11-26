---
title: LU分解
date: 2019-11-26 14:26:38
tags: C++
categories: 练习
---
LU分解的详解，请见下面维基百科的超链接。
<!--more-->

[维基百科LU详解](https://zh.wikipedia.org/wiki/LU%E5%88%86%E8%A7%A3)

```C++
#include <iostream>
#include <iomanip>

using namespace std;

template<typename T>
void print_mat(T* matrix, int row, int col)
{
	for (int i = 0; i < row; ++i)
	{
		for (int j = 0; j < col; ++j)
			cout << std::left << setw(9) << matrix[i * col + j] << "      ";

		cout << endl;
	}
	cout << endl;
}

template<typename T>
void tri_decomposition(T* mat_A, T* mat_L, T* mat_U, int row, int col)
{
	/*
	*	外层循环代表循环次数
	*	内层循环是分别处理L和U矩阵
	*/
	col--;					//统一从下标为0开始
	row--;					//统一从下标为0开始
	double cum_val = 0.0;
	for (int r = 0; r <= row; ++r)    //统一从下标0开始,与课本公式上的变量名保持一致
	{
		for (int i = r; i <= row; ++i) // U矩阵
		{
			if (0 == r)
				mat_U[r * (col+1) + i] = mat_A[r * (col+1) + i];
			else
			{
				cum_val = 0.0;						//置为0
				for (int k = 0; k <= r - 1; ++k)  //累加
				{
					cum_val += mat_L[r * (col + 1) + k] * mat_U[k * (col + 1) + i];
					//cout << "debug ";
					//cout << "step 1 cum_val = " << mat_L[r * (col + 1) + k] << "  " << r * (col + 1) + k <<"  "<< mat_U[k * (col + 1) + i]<<"  "<<cum_val << endl;
				}
				mat_U[r * (col + 1) + i] = mat_A[r * (col + 1) + i] - cum_val;
		
			}
		}
		
		for (int i = r; i <= row; ++i)	// L矩阵
		{
			if (0 == r)
				mat_L[i * (col + 1) + r] = (mat_A[i * (col + 1) + r]) / (mat_U[0 * (col + 1) + r]);

			else
			{
				cum_val = 0.0;
				for (int k = 0; k <= r - 1; ++k)   //累加
				{
					cum_val += mat_L[i * (col + 1) + k] * mat_U[k * (col + 1) + r];
				}
					//cum_val += mat_L[i * (col + 1) + k] * mat_U[k * (col + 1) + r];
				mat_L[i * (col + 1) + r] = (mat_A[i * (col + 1) + r] - cum_val) / mat_U[r * (col + 1) + r];
			}
				
		}
	}
}



int main()
{
	double mat_A [][3] = { {1,2,3}, {4,5,6}, {3,-3,5} };
	double mat_L [][3] = { {0,0,0}, {0,0,0} ,{0,0,0} };
	double mat_U [][3] = { {0,0,0}, {0,0,0}, {0,0,0} };

	print_mat((double*)mat_A, 3, 3);
	print_mat((double*)mat_L, 3, 3);
	print_mat((double*)mat_U, 3, 3);
	 
	tri_decomposition((double*)mat_A,(double*)mat_L,(double*)mat_U,3,3);
	print_mat((double*)mat_A, 3, 3);
	print_mat((double*)mat_L, 3, 3);
	print_mat((double*)mat_U, 3, 3);
}
```

![](https://yanyi-wuyoo.oss-cn-shanghai.aliyuncs.com/%E4%BD%9C%E4%B8%9A/Snipaste_2019-11-26_14-21-14.png)