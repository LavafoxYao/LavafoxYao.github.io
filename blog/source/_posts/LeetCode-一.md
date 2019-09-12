---
title: LeetCode 一
date: 2019-09-12 17:11:19
tags: LeetCode
categories: 练习
---

前言:

已经进入研究生的生活了,每天需要上课看论文帮老师做点项目,还是抽点时间来刷刷LeetCode,坚持每天起码有一个题的产出.刷题也是一个唯手熟尔的事.

<!--more-->

## 1.两个数之和
#### 暴力解法
```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int> re_vec(2);                        //返回一个vector
        for(int i = 0 ;i < nums.size() - 1 ;++i)
        {
            for(int j = i + 1 ;j < nums.size() ;++j)
            {
                if(target == nums[i] + nums[j] )
                {
                    re_vec[0] = i;
                    re_vec[1] = j;
                    return re_vec;
                }
            }
        }
        return re_vec;
    }
};
```
#### 一次循环
```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int> re_vec(2);                        //返回一个vector
        map< int, int > in_map;
        int in_temp = 0;
        for(int i = 0 ; i < nums.size() ; ++i)
        {
            in_map.insert(map<int,int>::value_type(nums[i],i)); //将所有数据插入map
            if(in_map.count(target - nums[i]) && (in_map[target - nums[i]] != i) )
            {
                //判断target与nums[i]之差的值是否存在于哈希表中
                //并且插入的值不能与i相等
                re_vec[0] = in_map[target - nums[i]];
                re_vec[1] = i;
                break;
            }
        }
       return re_vec;
    }
};
```
## 2.两数相加
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
   ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
       //两个链表一起遍历
       //用一个flag来标记是否需要进位
       //把相加的结果存放到一个新链表中
       bool carry_flag = false;
       int node_val = 0;
       ListNode* l3 = new ListNode(-1);
       ListNode* temp_ptr = l3;  
       while(l1 && l2)           //若两个链表遍历过程中都不为空
       {
           node_val = l1->val + l2->val;
           if(carry_flag)
               node_val++;       //若上两个节点相加进位则进一
            if(node_val >= 10)   //若本位两数之和大于等于10,则设置为true
            {
                carry_flag = true;
                node_val -= 10;
            }
           else
               carry_flag = false;
    
           ListNode* node_buf = new ListNode(node_val);
           temp_ptr->next = node_buf;
           temp_ptr = temp_ptr->next;
           l1 = l1->next;
           l2 = l2->next;
          
       }
       while(l1)
       {
          node_val = l1->val;
           if(carry_flag)
               node_val++;
            if(node_val >= 10)   //若本位两数之和大于等于10,则设置为true
            {
                carry_flag = true;
                node_val -= 10;
            }
           else
               carry_flag = false;
           ListNode* node_buf = new ListNode(node_val);
           temp_ptr->next = node_buf;
           temp_ptr = temp_ptr->next;
           l1 = l1->next;
       }
       while(l2)
       {
           node_val = l2->val;
           if(carry_flag)
               node_val++;
            if(node_val >= 10)   //若本位两数之和大于等于10,则设置为true
            {
                carry_flag = true;
                node_val -= 10;
            }
           else
               carry_flag = false;
         
           ListNode* node_buf = new ListNode(node_val);
           temp_ptr->next = node_buf;
           temp_ptr = temp_ptr->next;
           l2 = l2->next;
       }
       if(carry_flag)
       {
           ListNode* node_buf = new ListNode(1);
           temp_ptr->next = node_buf;
           temp_ptr = temp_ptr->next;
       }
       return l3->next;
   }
};

```

## 3. 无重复字符的最长子串*
第一次做没有思路.
数组
```c++
class Solution {
public:
   	int lengthOfLongestSubstring(string s)
	{
		//用一个string来存放子串 sub_str;
		//先遍历s
		//将每个s中的元素都放到sub_str中逐个比较,不存在的加入到sub_str
		//若sub_str不存在的元素则sub_str长度len ++
		//若sub_str存在该元素,则将该len保存,并将sub_str赋为空串
		//重复上述过程
		string sub_str("");
		int len = 0;
		int start = 0;  //查找开始的位置
		for (string::size_type i = 0; i < s. length() ; ++i)
		{
			if ( string::npos   == sub_str.find(s[i]) )//若不存在
				sub_str += s[i];
			else {                          			//若存在
				len = len > sub_str.length() ? len : sub_str.length();   
            //多次比较取最长
				i = s.find_first_of(s[i], start);				
            //start是记录每次sub_str在s中遍历的起点,从start开始找s[i],找到了返回下标
				start = i + 1;   //start是重复字符的起点,所以start+1  									            
				sub_str = "";  //重置数组
			}
		}
		return  len > sub_str.length() ? len : sub_str.length();
	}
};
```

## 4. 寻找两个有序数组的中位数*
思路:想把两个数组合并成一个数组,合并后的数组若是奇数则取中间的的数作为中位数,若为偶数则中间数和中间数的后一位相加除以2,但题目要求时间复杂度为O(log(m+n)),这样就有点卡住了.


## 5.最长回文字符串*
暴力解法
效率太低没有ac过...有个马拉车算法就是找最大回文字符串,没看太懂...
```c++
class Solution {
public:
  string longestPalindrome(string s) {
		//判断字符串是否是回文可以用std::reverse(begin,end)
		//两次循环找出最大回文串
		string res("");
		string temp_str("");
		int len = 0;
		for (string::size_type i = 0; i < s.length(); ++i)
		{
			for (string::size_type j = i; j < s.length(); ++j)
			{
				temp_str += s[j];
				string temp = temp_str;
				std::reverse(temp.begin(),temp.end());
				if (temp == temp_str)
					res = res.length() > temp.length() ? res : temp;
			}
			temp_str = "";
		}
		return res;
	
	}
};
```
## 6.Z字形变换
开始看这个题目就是一脸懵逼,后来看网友的解析看懂是什么意思之后,自己找规律实现
```c++
class Solution {
public:
	string convert(string s, int numRows) {
		//将每个字符标上序号,找到规律,第一行和最后一行间距都相同,step
		//其间的所有行都是 step - curRow*2 和 2*curRow
		//从下标0开始标记,以z的方式读,每个字符的位置都符合
		//cur_rows代表当前行数
		// cur_rows % step 或则 cur_row % step == (step - cur_row) 
		if (numRows == 1)
			return s;
		string ret("");
		int step = numRows*2 - 2;			//step = numRows +( numRows -2);
		for (string::size_type i = 0; i < numRows; ++i)
		{
			for (string::size_type j = 0; j < s.length(); ++j)
			{
				if (j % step == i ||  j % step == (step - i)) 
                //找规律
				ret += s[j];
			}
		}
        return ret;
	}
};
```

## 7.整数反转
题目看起来很简单,但是总是出现溢出的问题;
0x8000000 int中最小的负数,
0x7fffffff 代表int中最大的正数,
这种题目主要考察的也是边界值的处理.
```c++
class Solution {
public:
    int reverse(int x) {
        //判断该数是否大于0,确定正负号是否存在
        //将数字分解,再组合
        int sign = 1;
        if(0 == x || x == 0x80000000) //当x为int中最小的负数转换成正数会溢出
            return 0;
        if(x < 0)
        {
             sign = -1;
             x = -x;
        }
        int temp_val = 0;
        int re_val = 0;
        int count = 0;
      while (x)
		{
			temp_val = x % 10;
			x /= 10;
			if (0 == re_val && 0 == temp_val)
				continue;
			if (re_val > 0x7fffffff / 10) //防止向上溢出
				return 0;
			re_val *= 10;
			re_val += temp_val;

		}
        return re_val * sign ;
        
    }
};
```