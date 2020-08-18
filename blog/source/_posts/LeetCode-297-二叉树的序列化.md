---
title: LeetCode 297.二叉树的序列化
date: 2020-08-19 00:41:26
tags: ['二叉树', '序列化', 'C++']
categories: 题解
---

什么叫序列化？

<!--more-->

#### [297. 二叉树的序列化与反序列化](https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/)

> 序列化是将一个数据结构或者对象转换为连续的比特位的操作，进而可以将转换后的数据存储在一个文件或者内存中，同时也可以通过网络传输到另一个计算机环境，采取相反方式重构得到原数据。
>
> 请设计一个算法来实现二叉树的序列化与反序列化。这里不限定你的序列 / 反序列化算法执行逻辑，你只需要保证一个二叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。
>
>  **示例:**  
>
> ```
> 你可以将以下二叉树：
> 
>     1
>    / \
>   2   3
>      / \
>     4   5
> 
> 序列化为 "[1,2,3,null,null,4,5]"
>  
> ```

##### 什么是序列化?
对于序列化的定义维基是这样解释： **序列化（serialization）在计算机科学的资料处理中，是指将数据结构或对象状态转换成可取用格式（例如存成文件，存于缓冲，或经由网络中发送），以留待后续在相同或另一台计算机环境中，能恢复原先状态的过程。依照序列化格式重新获取字节的结果时，可以利用它来产生与原始对象相同语义的副本。对于许多对象，像是使用大量引用的复杂对象，这种序列化重建的过程并不容易。面向对象中的对象序列化，并不概括之前原始对象所关系的函数。这种过程也称为对象编组（marshalling）。从一系列字节提取数据结构的反向操作，是反序列化（也称为解编组、deserialization、unmarshalling）。**

简单的说就是像树这种结构，存储节点的内存都是分散在内存中的，想一次性通过网络`IO`发送出去是不太现实的，于是大佬们就想到一种方法可以保存发送前的状态，能够使得接收方通过某种方式对接收的数据解析，然后重构原来的这颗树，使得好似网络`IO`传输了一整棵`树`。

###### 思路

利用树的前序遍历将树节点之间的相对顺序保存下来，其中如果遇到`NULL`则在序列化过程中特殊处理，这里我使用的是`#`来代表改节点为空。在`C++`中使用字符流就不得不提及`ostringstream`与`istringstream`.

关于这两个函数的讲解，可以看看这个连接[字符串流的用法]( http://vcsos.com/article/pageSource/160122/20160122233408.shtml )

反序列化就是上述过程的反过程。

```C++
class Codec {
public:

    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        ostringstream out;
        serialize(root, out);
        return out.str();
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        istringstream in(data);
        return deserialize(in);
    }

    void serialize(TreeNode* root, ostringstream& out){
        // 利用二叉树的先序遍历使得[1,2,3,null,null,4,5]
        // 变成 [1 2 # # 3 4 # # 5 # # ]
        if (!root){ 
            // 处理空节点的情况 为了配合istringstrm我们给每个值后面加空格
            out<<"#"<<" ";   
            return ;
        }
        out<<root->val<<" ";
        serialize(root->left, out);
        serialize(root->right, out);
    }

     TreeNode* deserialize(istringstream& in){
         string s;
         in >> s;
         if (s == "#")  return nullptr;
         TreeNode* root = new TreeNode(stoi(s));
         root->left = deserialize(in);
         root->right = deserialize(in);
         return root;
     }
};

```

