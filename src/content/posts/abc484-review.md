---
title: AtCoder Beginner Contest 484
published: 2026-11-14
description: 赛中通过了A、B、C，后序把剩下的题目与知识点补上
tags:
  - AtCoder
  - ABC
  - 比赛复盘
category: 算法竞赛
draft: false
---

这场 [ABC 484](https://atcoder.jp/contests/abc484) 一共 100 分钟，我在赛中通过了 A、B、C。A、B 加起来用了约 10 分钟，C 却花了却因为条件判断逻辑不正确，一直卡在了一个点，导致后面没有时间完成后面的题。

## 做题记录

| 题目  | 思考、编码与调试用时 | 赛中结果 | 主要卡点         |
| --- | ---------: | ---- | ------------ |
| A   |     约 7 分钟 | AC   | 无明显卡点        |
| B   |     约 3 分钟 | AC   | 无明显卡点        |
| C   |    约 70 分钟 | AC   | 两种移动方向的判断有漏洞 |

**补题清单：** D、E、F、G。

## C 题：两个方向都要尝试

C题有三种情况可以输出`"Yes"`，`A` 数组等于`B`数组，不同区间中`A`把区间起点的元素移动到了最后面得到`B`，不同区间中`A`把区间终点的元素移动到了最前面得到`B`。（先从0向n遍历直到寻找到两数组元素不同时，记录当前位置为`posl`，再从n-1向posl遍历，记录右侧不同位置为`posr`，`[posl,posr]`则为不同区间）

在寻找posl时，就可以判断第一种情况是否成立，当`posl==n-1`A数组等于B数组直接输出`"Yes"`；

若不同区间存在，我们用两个bool变量初判断是那种类型的移动，c1代表A是否把区间起点的元素移动到了最后面得到B，c2则代表A把区间终点的元素移动到了最前面得到B；

进行了初次判断后，我们再对不同区间内的元素进行判断。由于只进行了一次移动，我们只需要根据移动类型判断是否满足对应的变换，若不满足则相对应的bool变量变为false

核心代码：
```cpp

bool c1=(a[posl]==b[posr]);
for(int i=posl+1;i<=posr;i++)
{
	if(a[i]!=b[i-1])
	    c1=false;
}
for(int i=posl;i<posr;i++)
{
    if(a[i]!=b[i+1])
        c2=false;
}	
cout<<("Yes":"No"?(c1||c2))<<'\n';
```
卡点分析：最初我只是使用if . . . else if根据是否满足c1再对不同区间里的进行c1对应的判断，因此忽略了对c2条件的判断，这是一种非此即彼的误判 。

学长提供的思路：对两数组从左向右读取，遇到不同后进行操作
```cpp
int l=-1;
bool ans=true;
bool shiftleft=true,shiftright=true;
for(int i=0;i<n;i++)
{
	if(l==-1)//还未记录时
	{
		if(a[i]==b[i])continue;
		l=i;
	}
	if(i>l)
	{
		if (a[i]!= b[i-1])shiftLeft=false;
        if (a[i-1]!=b[i])shiftRight=false;
	}
	if(a[i]!=b[i])
	{
		ans=((shiftleft&&a[l]==b[i])||(shiftright&&a[i]==b[l]));//最终记录的答案等价于(shiftleft&&a[posl]==b[posr])||(shiftright&&a[posr]==b[posl])
	}
}

cout<<(ans?"Yes\n":"No\n");
```

## D题：把相邻相同元素的重复计算弄清楚

补D题时，我在草稿纸上大致的找了一下规律。对于第i个元素，有i个空位供他插入，若他前面相邻元素与他相等，则跳过他，寻找第i-1个元素可插入的位置（因为可插入的点相同）。确定下了i，若前面有j个相同元素，则有i-j种情况。

核心代码：
```cpp
int n;
  cin>>n;
  vector<int> a(n);
  unordered_map<int,int> cnt;
  for(int i=0;i<n;i++)
  {
    cin>>a[i];
    cnt[a[i]]++;
  }
  long long ans=1;
  for(int i=n-1;i>0;i--)
  {
    long long sum=i;
    cnt[a[i]]--;
    if(a[i]==a[i-1])continue;
    sum-=cnt[a[i]];
    ans+=sum;
  }
  cout<<ans<<'\n';
```

