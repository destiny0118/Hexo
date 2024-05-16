---
title: 树：字典树（前缀树、TrieTree）
date: 2023-02-28
description: 字典树，又称单词查找树，Trie树(==Retrieval Tree==)，一种树形数据结构，用于高效的存储和检索字符串数据集中的键。
top: 10
---

# 字典树（前缀树、TrieTree）

> 字典树，又称单词查找树，Trie树(==Retrieval Tree==)，一种树形数据结构，用于高效的存储和检索字符串数据集中的键。
>
> 用于统计、排序和保存大量的字符串
>
> 优点：利用字符串的公共前缀来减少查询时间，最大限度地减少无谓的字符串比较
>
> 给定字符串集合构建一颗前缀树，判断前缀树中是否存在字符串或该字符串的前缀
> 字典树用于判断字符串是否存在或者是否具有某种字符串前缀

==结点表示从根结点到本结点的路径构成的字符串是否有效==

![image-20220920210823099](https://raw.githubusercontent.com/destiny0118/picgo/master//202209202108218.png)



## 数据结构

> 字典树可以视为set，用于判断key是否存在；；但同时字典树也可以作为map，只需要再相应节点添加value值。

[详解前缀树「TrieTree 汇总级别整理 🔥🔥🔥」 - 字符串的前缀分数和 - 力扣（LeetCode）](https://leetcode.cn/problems/sum-of-prefix-scores-of-strings/solution/by-lfool-w82u/)

> - 根据字符串集合构建前缀树
> - 目标字符串是否存在于前缀树中
> - 寻找目标字符串的前缀

```c++
//两种存储方式
struct TrieNode{
    TrieNode* son[26]={};
    boolean val; //路径构成的字符串是否有效
};
TrieNode *root=new TrieNode;

//构建前缀树
void insert(string word){
    TrieNode *cur=root;
    for(char c: word){
        int id=c-'a';	//当前字符应该存入哪一个孩子结点
        if(cur->son[id]==nullptr)
            cur->son[id]=new TrieNode;
        cur=cur->son[id];
    }
    cur->val=true;		//表示当前字符串有效
}

//查询给定的字符串在前缀树中是否存在
bool query(string word){
    TrieNode *cur=root;
    for(char ch:word){
        int id=ch-'a';
        //字符串路径不存在
        if(cur->son[id]==nullptr)
            return false;
       	cur=cur->son[id];
    }
    //路径存在，路径终点处的结点是否有效
    return cur->val;
}

//是否有word有前缀prefix
bool startsWith(string prefix) {
    TrieNode *cur=root;
    for(char ch:prefix){
        int id=ch-'a';
        if(cur->son[id]==nullptr)
            return false;
        cur=cur->son[id];
    }
    //查询完前缀
    return true;
}

//寻找单词的最短前缀
string shortestPrefixOf(string word){
    TrieNode *cur=root;
    string ret="";
    for(char ch: word){
        int id=ch-'a';
        if(cur->val)
            return ret;
        if(cur->son[id]==nullptr)
            return "";
        ret+=ch;
        cur=cur->son[id];
    }
    return "";
}

//含有通配符的匹配
bool search(string word){
    return query(root,word,0);
}

bool query(TrieNode *cur, string &word,int index){
    //判断空节点要放在最前面，因为含有.时会遍历26个子节点
    if(cur==nullptr)
        return false;
    if(index==word.size())
        return true;
    
    if(word[index]=='.'){
        for(int i=0;i<26;i++){
            if(query(cur->son[i],word,index+1))
                return true;
        }
        return false;
    }else{
        int id=word[index]-'a';
        return query(cur->son[id],word,index+1);
    }
    
}
```

```c++
//哈希表存储
struct TrieNode{
    unordered_map<char,TrieNode*> son;
    boolean val;
}
```



- [ ] [208. 实现 Trie (前缀树)](https://leetcode.cn/problems/implement-trie-prefix-tree/)
- [ ] [211. 添加与搜索单词 - 数据结构设计](https://leetcode.cn/problems/design-add-and-search-words-data-structure/)(通配符匹配.)
- [ ] [676. 实现一个魔法字典](https://leetcode.cn/problems/implement-magic-dictionary/)（修改一个字符的匹配，类似于存在一个.)

