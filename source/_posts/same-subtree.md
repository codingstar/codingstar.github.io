title: 判断一棵树是否是另一棵树的子树
date: 2016-04-22 10:09:24
tags: [算法, 积累, 搜索, 笔试]
---
昨天做到一道百度的笔试题，感觉对深搜的理解很有帮助，记录一下。

# 题目描述
给定两个子树，树节点的数值唯一，问第二棵树是否是第一棵树的子树。是就返回1，否则-1。
给定树节点的定义为：
```
struct tnode
{
	int value;
	tnode* left;
	tnode* right;
	tnode(int v) {   //这个构造函数是我为了调试时构造节点方便而写的
		value = v;
		left = NULL;
		right = NULL;
	}
};
```
<!--more-->
# 考虑思路
这题题意清晰，数据量没有给，应该也不是很大。有其他小伙伴认为可以遍历两个树，得到两个字符串，看是否第二个是第一个的子串。这个想法可能有一定的道理，但是同一个字符串，出现在遍历出来的字符串中的不同位置，也不一定代表就是子树，因为重复的字符串首先在第一个树中要是一个完整的子树才行。要进行这样的判断，太耗时耗力了，还不如考虑直接暴力搜索。

如果是暴搜的话，就要考虑如何搜。最直观的想法是，用第一层dfs枚举母树（第一棵树）的每个节点，然后用第二层dfs（或者bfs也行）遍历当前两棵子树，看是否一模一样。一旦发现相同的，就返回1。最后找不到，就返回-1。

然后就是一些细节问题了，要考虑左右子树完全相同才行。

# 关键代码
```
bool issame(tnode* mu_root, tnode* zi_root) {   //判断两棵树是否相同
	if (mu_root->value != zi_root->value)  //当前节点不同，树肯定不同
		return 0;
	if (mu_root->left==NULL && mu_root->right==NULL) { //当前节点相同且母树为叶节点
        if (zi_root->left==NULL && zi_root->right==NULL)//子树也是页节点
		      return 1;
        return 0;
	}
	if (mu_root->left != NULL) {
		if (zi_root->left == NULL)
			return 0;
		bool bl = issame(mu_root->left, zi_root->left); //查看左子树
		if (!bl)
			return 0;
	}
	else if (zi_root->left != NULL) 	//母树没有左子树但子树有
		return 0;
	if (mu_root->right != NULL) {
		if (zi_root->right == NULL)
			return 0;
		bool bl = issame(mu_root->right, zi_root->right); //查看右子树
		if (!bl)
			return 0;
	}
	else if (zi_root->right != NULL)	//母树没有右子树但子树有
		return 0;
    //左右子树都相同，才算相同
	return 1;
}

bool dfs(tnode* mu_root, tnode* zi_root) {  // 第一层dfs枚举
	if (mu_root->left==NULL && mu_root->right==NULL) { //若是叶子节点
        //直接返回是否同为叶节点且相等
		return mu_root->value==zi_root->value && zi_root->left==NULL && zi_root->right==NULL;
	}
	bool res = issame(mu_root, zi_root); //看以mu_root和zi_root为根节点的两个树是否相同
	if (res)
		return 1;
    //若不同，继续深搜枚举
	if (mu_root->left != NULL) {   //左子节点
		bool res_l = dfs(mu_root->left, zi_root);
		if (res_l)
			return 1;
	}
	if (mu_root->right != NULL) {  //右子节点
		bool res_r = dfs(mu_root->right, zi_root);
		if (res_r)
			return 1;
	}
	return 0;
}

int solve(tnode* root1, tnode* root2) { //解决问题的主要函数
	bool ans = dfs(root1, root2);
	if (!ans)
		return -1;
	return ans;
}
```
