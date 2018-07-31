---
layout: artical
title: HunterCatchRabbits
date: 2018-07-18 10:00:22
tags: 算法
---
# 题目描述：
       假设卧底和特工有n个接头地点，编号为0和n-1。卧底每天晚上仅能换到相邻的接头地点，而特工每天白天在一个接头地点检查。
	   特工告诉你每天检查的接头地点的编号，希望你分析一下是否一定能与卧底碰头。
# 输入描述：
       首先输入n和k（n<100,k<1000），分别表示接头地点的个数和特工检查的天数；接下来输入k个整数，表示特工每天检查的接头地点的编号。
# 输出描述：
      如果一定能与卧底碰头，输出Yes，否则输出No。

# 测试用例：
```
	5 6
	1 2 3 3 2 1
	Yes
```
# 测试代码:
```
#include<iostream>
#include<vector>
using namespace std;
 
bool canCatchRibbit(vector<int> holeNums, vector<int> checked)
{
	
	int k = holeNums.size();
	int n = checked.size();
	bool flag = true;
	int cnt = n;  //  cnt为兔子能待的洞的个数，初试化为所有洞的个数
	for (int i = 0; i < k; i++)
	{
		int holeNum = holeNums[i] - 1; //  holeNum为洞的编号在vector中的序号，
		                               //  如：编号为1的洞在vector里面存的是0
		if (checked[holeNum] == 1)
		{
			checked[holeNum] = 0;
			cnt--;
		}
		if (cnt == 0)   //  如果兔子能待的洞的个数为0，则兔子肯定会被逮住
		{
			flag = false;
			break;
		}
		for (int j = 0; j < n; j++)
		{
			if (checked[j] == 1)
			{
				checked[j] = 0;
				cnt--;
				if (j>0 && checked[j - 1] == 0)
				{
					checked[j - 1] = 1;
					cnt++;
				}
				if (j < n - 1 && checked[j + 1] == 0)
				{
					checked[j + 1] = 1;
					cnt++;
					j++;  //原来值为0的洞现在为1后是不能往相邻的洞跳，故继续检查下一个洞
				}
			}
		}
	}
	return flag;
}
 
int main()
{
	int n, k;  //  n为洞的个数，k为检查的天数
	cin >> n >> k;
	vector <int> holeNums(k, 0);  //  holeNums为每天检查的洞的编号
	for (auto &m : holeNums)
		cin >> m;
	vector<int> checked(n, 1);  //  checked为每天检查的洞中是否有兔子,可能有兔子则值为1，
	                            //  一定没兔子则值为0，全部初始化为1；
	bool flag = canCatchRibbit(holeNums, checked);
	if (!flag)
		cout << "Yes" << endl;
	else
		cout << "No" << endl;
	return 0;
}
```

