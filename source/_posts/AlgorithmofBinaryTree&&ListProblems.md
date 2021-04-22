---
title: Algorithm of BinaryTree&&List
date: 2021-04-22 11:58:25
updated: 2020-04-22 11:58:25
categories: [algorithm]
---

### 剑指Offer刷题经验集

```
-专栏
-二叉树
-链表
```

#### Get Start

##### 1.Offer06从尾到头打印链表

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

示例 1：输入：head = [1,3,2]
                       输出：[2,3,1]

```c++
class Solution {
public:
    vector<int> reversePrint(ListNode* head) {
    if(head==nullptr)return vector<int>();
    if(head->next==nullptr)return vector<int>{head->val};
    ListNode* pre=nullptr;
    ListNode* cur=head;
    ListNode* post=head->next;
    while(post!=nullptr){
      cur->next=pre;
      pre=cur;
      cur=post;
      post=post->next;
    }
    cur->next=pre;
    vector<int> ans;
    while(cur!=nullptr){
        ans.push_back(cur->val);
        cur=cur->next;
    }
    return ans;
    }
};
//本题我采用的思路是三指针反转链表，再从链表尾一次遍历反转后的链表实现从尾到头顺序的打印。
//当然本题也可以采用栈的数据结构。
//通过这个题我主要练习的应该是反转链表这个高频考点！
```



##### 2.Offer07重建二叉树

输入某二叉树的前序遍历和中序遍历的结果，请重建该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

Eg:

前序遍历 preorder = [3,9,20,15,7]
       中序遍历 inorder = [9,3,15,20,7]
返回如下的二叉树：

```
    3
   / \
  9  20
    /  \
   15   7
```

```
输入某二叉树的前序遍历和中序遍历的结果，请重建该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如，给出
前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
返回如下的二叉树：

    3
   / \
  9  20
    /  \
   15   7
```

```c++
class Solution {
private:
	map<int, int>mp;
	vector<int> preorder;
	TreeNode* constructTree(int root, int l, int r) {
		if (l > r)return nullptr;
		TreeNode* node = new TreeNode(preorder[root]);
		int index_in_inorder = mp[preorder[root]];
		node->left = constructTree(root + 1,  l, index_in_inorder - 1);
		node->right = constructTree(root + index_in_inorder - l + 1, index_in_inorder + 1, r);
		return node;
	}
public:
	TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        this->preorder=preorder;
		for (int i = 0; i < inorder.size(); i++) {
			mp[inorder[i]] = i;
		}
		return constructTree(0, 0, inorder.size() - 1);
	}
};
//通过递归调用左右子树的方式建树
//核心在于每一次通过寻找到中序遍历的根节点的位置确定左右子树的长度，再通过左右子树的长度和先序遍历的序列确定下一个左右子树的各自的根节点的位置。
//递归构造的结束条件非常类似大多数的递归，通过左边界大于右边界的方式来终止。
//因为本道题目不含有重复的数字，每一个数字可以看作是一个唯一且独特的节点，我们需要知道每一次遍历过程中的根节点在先序序列和中序序列中各自的位置，可以通过该节点的val值建立map表来查询处理。
```



##### 3.Offer18删除链表中的节点

```c++
class Solution {
public:
    ListNode* deleteNode(ListNode* head, int val) {
    ListNode* yummyhead=new ListNode();
    yummyhead->next=head;
    if(head->val==val)return head->next;
    while(head->next!=nullptr){
        if(head->next->val==val){
            head->next=head->next->next;
            break;
        }
        else head=head->next;
    }
    return yummyhead->next;
    }
};
//哑节点（即指向头节点之前一个位置的节点），主要作用是方便代码适应头节点这样的特殊位置上的节点。
```

##### 4.Offer22返回链表中倒数第k个节点

```c++
class Solution {
public:
	ListNode* getKthFromEnd(ListNode* head, int k) {
		ListNode* front = head;
		ListNode* before = head;
		int i = 0;
		while (front->next != nullptr) {
			front = front->next;
			i++;
			if (i >= k) {
				before = before->next;
			}

		}
		return before;
	}
};
//双指针
```

##### 5.Offer26树的子结构

```
输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)

B是A的子结构， 即 A中有出现和B相同的结构和节点值。

例如:
给定的树 A:

     3
    / \
   4   5
  / \
 1   2
给定的树 B：

   4 
  /
 1
返回 true，因为 B 与 A 的一个子树拥有相同的结构和节点值。

示例 1：

输入：A = [1,2,3], B = [3,1]
输出：false

示例 2：

输入：A = [3,4,5,1,2], B = [4,1]
输出：true
```

```c++
class Solution {
private:
	TreeNode* ans;
	void findtar(TreeNode* root, TreeNode* tar) {
		if (root == nullptr)return;
		if (root->val == tar->val)ans = root;
		// cout << root->val << endl;
		findtar(root->left, tar);
		findtar(root->right, tar);
	}
	bool isSimilar(TreeNode* a, TreeNode* b) {
		if (a == nullptr&&b != nullptr)return false;
		else if (a != nullptr&&b == nullptr)return true;
		else if (a == nullptr&&b == nullptr)return true;
		else if (a->val == b->val) return isSimilar(a->left, b->left) && isSimilar(a->right, b->right);
		else return false;
	}
public:
	bool isSubStructure(TreeNode* A, TreeNode* B) {
        if(B==nullptr) return false;
		findtar(A, B);
		if (ans == nullptr)return false;
		return isSimilar(ans, B);
	}
};
//递归思想判别相同树
//先寻找和B的root节点值相同的在A中的节点x，然后判断x的子树中是否含有和B的结构相同的部分。
```

##### 6.Offer27二叉树的镜像

```
请完成一个函数，输入一个二叉树，该函数输出它的镜像。

例如输入：

     4
   /   \
  2     7
 / \   / \
1   3 6   9
镜像输出：

     4
   /   \
  7     2
 / \   / \
9   6 3   1

示例 1：
输入：root = [4,2,7,1,3,6,9]
输出：[4,7,2,9,6,3,1]

```

```c++
class Solution {
    private:
    void domirror(TreeNode* t){
        if(t==nullptr)return;
     TreeNode* tmp=t->left;
     t->left=t->right;
     t->right=tmp;
     domirror(t->left);
     domirror(t->right);
    }
public:
    TreeNode* mirrorTree(TreeNode* root) {
     domirror(root);
     return root;
    }
};
//二叉树的问题经常用到递归
//递归还是所有方法中我个人认为最具有挑战性的一类
//尽管本题很普通，还是想放上来多呈现一些递归使用的例子
```

##### 7.Offer28对称的二叉树

```
请实现一个函数，用来判断一棵二叉树是不是对称的。如果一棵二叉树和它的镜像一样，那么它是对称的。

例如，二叉树 [1,2,2,3,4,4,3] 是对称的。

    1
   / \
  2   2
 / \ / \
3  4 4  3
但是下面这个 [1,2,2,null,3,null,3] 则不是镜像对称的:

    1
   / \
  2   2
   \   \
   3    3
示例 1：
输入：root = [1,2,2,3,4,4,3]
输出：true
示例 2：
输入：root = [1,2,2,null,3,null,3]
输出：false
```

```c++
class Solution {
private:
	void buildMirrorTree(TreeNode* t) {
		if (t == nullptr)return;
		TreeNode* tmp = t->left;
		t->left = t->right;
		t->right = tmp;
		buildMirrorTree(t->left);
		buildMirrorTree(t->right);
	}
	bool isSimilar(TreeNode* a, TreeNode* b) {
		if (a == nullptr&&b == nullptr)return true;
		if (a == nullptr&&b != nullptr)return false;
		if (a != nullptr&&b == nullptr)return false;
		if (a->val == b->val)return isSimilar(a->left, b->left) && isSimilar(a->right, b->right);
		return false;
	}
	void prePrintTree(TreeNode* t) {
		if (t == nullptr) {
			cout << "null" << " ";
			return;
		}
		cout << t->val << " ";
		prePrintTree(t->left);
		prePrintTree(t->right);
	}
	void bfsPrintTree(TreeNode* t) {
		queue<TreeNode*>s;
		s.push(t);
		while (!s.empty()) {
			TreeNode* tmp = s.front();
			s.pop();
			if (tmp == nullptr)cout << "null" << " ";
			else {
				cout << tmp->val << " ";
				s.push(tmp->left);
				s.push(tmp->right);
			}
		}
	}
	TreeNode* TreeClone(TreeNode* root)
	{
		if (root == nullptr)return nullptr;
		TreeNode* new_root = new TreeNode(root->val);
		new_root->left = TreeClone(root->left);
		new_root->right = TreeClone(root->right);
		return new_root;
	}
public:
	bool isSymmetric(TreeNode* root) {
		TreeNode* mirror_root = TreeClone(root);
		buildMirrorTree(mirror_root);
		return isSimilar(root, mirror_root);
	}
};
//本体代码量大主要原因是我写了一些和解决该问题无关的函数
//这些函数包括先序打印二叉树，深拷贝一个二叉树，层序遍历二叉树等，都是比较实用的测试结果的工具。
```

##### 8.Offer32-1 从上到下打印二叉树

```
从上到下打印出二叉树的每个节点，同一层的节点按照从左到右的顺序打印。
例如:
给定二叉树: [3,9,20,null,null,15,7],

    3
   / \
  9  20
    /  \
   15   7

返回：
[3,9,20,15,7]

```

```c++
class Solution {
    private:
    vector<int> ans;
    void bfsPrintTree(TreeNode* t) {
		queue<TreeNode*>s;
		s.push(t);
		while (!s.empty()) {
			TreeNode* tmp = s.front();
			s.pop();
			if(tmp!=nullptr) {
			    ans.push_back(tmp->val);
				s.push(tmp->left);
				s.push(tmp->right);
			}
		}
	}
public:
    vector<int> levelOrder(TreeNode* root) {
       bfsPrintTree(root);
       return ans;
    }
};
//迭代 队列 BFS
```

##### 9.Offer-32-2从上到下打印二叉树

```c++
从上到下按层打印二叉树，同一层的节点按从左到右的顺序打印，每一层打印到一行。
例如:
给定二叉树: [3,9,20,null,null,15,7],

    3
   / \
  9  20
    /  \
   15   7

返回其层次遍历结果：
[
  [3],
  [9,20],
  [15,7]
]

```

```c++
class Solution {
    private:
    vector<vector<int>> ans;
    void bfsPrintTree(TreeNode* t) {
		queue<TreeNode*>s;
		s.push(t);
        int layer=0;
        
		while (!s.empty()) {
            ans.push_back(vector<int>());
            int count=s.size();
            while(count){
			TreeNode* tmp = s.front();
			s.pop();
			if(tmp!=nullptr) {
			ans[layer].push_back(tmp->val);
				s.push(tmp->left);
				s.push(tmp->right);
			}
            count--;
		}
        layer++;
        }
	}
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
     bfsPrintTree(root);
     ans.erase(ans.end()-1);
     return ans;
    }
};
//关注队列层次遍历常用的count方法！
```

##### 10.Offer-32-3从上到下打印二叉树

```
请实现一个函数按照之字形顺序打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印，其他行以此类推。
例如:
给定二叉树: [3,9,20,null,null,15,7],

    3
   / \
  9  20
    /  \
   15   7
返回其层次遍历结果：
[
  [3],
  [20,9],
  [15,7]
]

```

```c++
class Solution {
        private:
    vector<vector<int>> ans;
    void bfsPrintTree(TreeNode* t) {
		queue<TreeNode*>s;
		s.push(t);
        int layer=0;
        while (!s.empty()) {
            ans.push_back(vector<int>());
            int count=s.size();
            while(count){
			TreeNode* tmp = s.front();
			s.pop();
			if(tmp!=nullptr) {
			ans[layer].push_back(tmp->val);
				s.push(tmp->left);
				s.push(tmp->right);
			}
            count--;
		}
        layer++;
        }
	}
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
     bfsPrintTree(root);
     ans.erase(ans.end()-1);
     for(int i=0;i<ans.size();i++){
         if(i%2==1){
             reverse(ans[i].begin(),ans[i].end());
         }
     }
    return ans;
    }
};
//reverse函数的使用
```

##### 11.Offer33二叉搜索树的后序遍历序列

```
输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历结果。如果是则返回 true，否则返回 false。假设输入的数组的任意两个数字都互不相同。
参考以下这颗二叉搜索树：

     5
    / \
   2   6
  / \
 1   3

示例 1：
输入: [1,6,3,2,5]
输出: false
示例 2：
输入: [1,3,2,6,5]
输出: true
```

```c++
class Solution {
private:
bool recur(vector<int>& v,int l,int r){
  if(l>=r)return true;
  int l_begin=l,l_end=l;
  int r_begin,r_end;
  int root=v[r];
  while(v[l_end]<root){
      l_end++;
  }
  r_begin=l_end;
  r_end=r_begin;
  l_end-=1;
  while(v[r_end]>root){
      r_end++;
  }
  return r_end==r&&recur(v,l_begin,l_end)&&recur(v,r_begin,r_end-1);
}
public:
    bool verifyPostorder(vector<int>& postorder) {
        return recur(postorder,0,postorder.size()-1);
    }
};
    //the key of solving this problem is to find that the root can  always divide the tree into 2 subtrees, and we can use that cahracter to make recurrsion solution
```

##### 12.Offer34二叉树中和为某一值的路径

```
输入一棵二叉树和一个整数，打印出二叉树中节点值的和为输入整数的所有路径。从树的根节点开始往下一直到叶节点所经过的节点形成一条路径。
示例:
给定如下二叉树，以及目标和 target = 22，

              5
             / \
            4   8
           /   / \
          11  13  4
         /  \    / \
        7    2  5   1
返回:
[
   [5,4,11,2],
   [5,8,4,5]
]
```

```c++
class Solution {
private:
	vector<vector<int>> ans;
	vector<int> path;
	int tar;
	void recur(TreeNode* cur) {
		if (cur != nullptr) {
			path.push_back(cur->val);
			tar -= cur->val;
			if (tar != 0 || cur->left != nullptr || cur->right != nullptr) {
				recur(cur->left);
				recur(cur->right);
			}
			else {
				ans.push_back(path);
			}
			path.pop_back();
			tar += cur->val;
		}
	}
public:
	vector<vector<int>> pathSum(TreeNode* root, int target) {
		tar = target;
		recur(root);
		return ans;
	}
};
//how to implement backtracking method in code
```

##### 13.Offer35复杂链表的复制

##### 14.Offer36二叉搜索树和双向链表

##### 15.Offer37序列化二叉树

```
请实现两个函数，分别用来序列化和反序列化二叉树。
示例: 
你可以将以下二叉树：

    1
   / \
  2   3
     / \
    4   5

序列化为 "[1,2,3,null,null,4,5]"
```

```c++
 class Codec{
public:

	// Encodes a tree to a single string.
	string serialize(TreeNode* root) {
		queue<TreeNode*> q;
		queue<TreeNode*> record;
		q.push(root);
		while (!q.empty()) {
			TreeNode* tmp = q.front();
			q.pop();
			record.push(tmp);
			if (tmp ==nullptr)continue;
			q.push(tmp->left);
			q.push(tmp->right);
		}
		string res;
		while (!record.empty()) {
			TreeNode* tmp = record.front();
		if (tmp == nullptr)res = res.append("null ");
			else {
				res = res.append(to_string(tmp->val)+" ");
			}
			record.pop();
		}
        //    cout<<res<<endl;
		return res;
	}

	// Decodes your encoded data to tree.
	TreeNode* deserialize(string data) {
		stringstream input(data);
		vector<string> res;
		string tmp;
		while (input >> tmp) {
			res.push_back(tmp);
		}
        	// cout << res.size()<<endl;
		queue<TreeNode*>q;
		int i = 1;
		int cal = 0;
		if (res[0] == "null")return nullptr;
		TreeNode* ans = new TreeNode(stoi(res[0]));
		q.push(ans);
		while (i < res.size()&&!q.empty()) {
			if (res[i] != "null") {
				TreeNode* t = new TreeNode(stoi(res[i]));
				q.push(t);
				if (cal % 2 == 0)q.front()->left = t;
				else {
					q.front()->right = t;
					q.pop();
				}
			}
			else {
				if (cal % 2 == 0)q.front()->left = nullptr;
				else {
					q.front()->right = nullptr;
					q.pop();
				}
			}
			cal++;
            i++;
		}
		return ans;
	}
};
//这道题感觉就没什么学的，主要是了解了一下stringtream分割空格符字符串，int转string，string转int，
//以及很重要的是string中用+连接字符串比用append函数慢很多很多！！！
// Your Codec object will be instantiated and called as such:
// Codec codec;
// codec.deserialize(codec.serialize(root));
```

##### 16.Offer54二叉搜索树的第k大个节点

```
给定一棵二叉搜索树，请找出其中第k大的节点。
示例 1:
输入: root = [3,1,4,null,2], k = 1
   3
  / \
 1   4
  \
   2
输出: 4
示例 2:
输入: root = [5,3,6,2,4,null,null,1], k = 3
       5
      / \
     3   6
    / \
   2   4
  /
 1
输出: 4
```

```c++
class Solution {
private:
     int count=0;
     int ans=0; 
     void dfs(TreeNode*cur){
         if(cur==nullptr)return;
         dfs(cur->left);
         count--;
         if(count==0)ans=cur->val;
         dfs(cur->right);
     }
      void dfs_nodes(TreeNode*cur){
         if(cur==nullptr)return;
         dfs_nodes(cur->left);
         count++;
         dfs_nodes(cur->right);
     }

public:
    int kthLargest(TreeNode* root, int k) {
          dfs_nodes(root);
          count=count-k+1;
          dfs(root);
          return ans;
    }
};
//本题有趣的地方在于查找第k[大]个节点
//我们常常受到中序遍历的思维定势，觉得只能通过递归的代码去找第k小的节点
//如果转变通常的思路把形如下方的代码改变为
// dfs_nodes(cur->left);
//       count++;
//dfs_nodes(cur->right);
//change to:
// dfs_nodes(cur->right);
//       count++;
//dfs_nodes(cur->left);
//不就迎刃而解了吗？
//我使用的仍然是普通中序遍历的思路
//因此需要先遍历整棵二叉树查找总共有多少个节点
//在通过普通的中序遍历找第size-k个节点。
```

##### 17.Offer55-1二叉树的深度

```
输入一棵二叉树的根节点，求该树的深度。从根节点到叶节点依次经过的节点（含根、叶节点）形成树的一条路径，最长路径的长度为树的深度。
例如：
给定二叉树 [3,9,20,null,null,15,7]，

    3
   / \
  9  20
    /  \
   15   7
返回它的最大深度 3 。
```

```c++
class Solution {
    private:
    int ans=0;
    void dfs(TreeNode* cur,int layer){
        if(cur==nullptr)return;
          ans=max(layer,ans);
        dfs(cur->left,layer+1);
        dfs(cur->right,layer+1);
    }
public:
    int maxDepth(TreeNode* root) {
          dfs(root,1);
          return ans;
    }
};
//双while+count=q.size();q--;写bfs层序遍历别忘了！
```

##### 18.Offer55-2平衡二叉树

```c++
输入一棵二叉树的根节点，判断该树是不是平衡二叉树。如果某二叉树中任意节点的左右子树的深度相差不超过1，那么它就是一棵平衡二叉树。
示例 1:
给定二叉树 [3,9,20,null,null,15,7]

    3
   / \
  9  20
    /  \
   15   7
返回 true 。
示例 2:
给定二叉树 [1,2,2,3,3,null,null,4,4]

       1
      / \
     2   2
    / \
   3   3
  / \
 4   4
返回 false 。
```

```c++
class Solution {
    private:
    bool ans=true;
    int depth_dfs(TreeNode*cur){
        if(cur==nullptr)return 0;
        int depthL=depth_dfs(cur->left);
        int depthR=depth_dfs(cur->right);
        if(abs(depthL-depthR)>1)ans=false;
        return max(depthL,depthR)+1;
    }

public:
    bool isBalanced(TreeNode* root) {
         depth_dfs(root);
         return ans;
    }
};
//递归
```

##### 19.Offer68-1二叉搜索树的最近公共祖先

![](/images/binarytree.png)

```
给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。
百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”
例如，给定如下二叉搜索树:  root = [6,2,8,0,4,7,9,null,null,3,5]
示例 1:
输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
输出: 6 
解释: 节点 2 和节点 8 的最近公共祖先是 6。
示例 2:
输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 4
输出: 2
解释: 节点 2 和节点 4 的最近公共祖先是 2, 因为根据定义最近公共祖先节点可以为节点本身。
```

```c++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        TreeNode* ans=root;
        while(ans!=nullptr){
        if((ans->val-p->val)*(ans->val-q->val)>0){
            if(ans->val-p->val>0)ans=ans->left;
            else ans=ans->right;
        }
        else return ans;
        }
        return ans;
    }
};
//这道题目迭代的方法非常简单和容易理解，学习这样的思路！
```

##### 20.Offer68-2二叉树的最近公共祖先

![](/images/binarytree2.png)

```
给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。
百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”
例如，给定如下二叉树:  root = [3,5,1,6,2,0,8,null,null,7,4]
示例 1:
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
输出: 3
解释: 节点 5 和节点 1 的最近公共祖先是节点 3。
示例 2:
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
输出: 5
解释: 节点 5 和节点 4 的最近公共祖先是节点 5。因为根据定义最近公共祖先节点可以为节点本身。
```

```c++
class Solution {
public:
    TreeNode* ans;
    bool dfs(TreeNode* root,int l,int r){
        if(root==nullptr)return false;
        bool lson=dfs(root->left,l,r);
        bool rson=dfs(root->right,l,r);
        if(lson&&rson||((lson||rson)&&(root->val==l||root->val==r)))ans=root;
        return lson||rson||root->val==l||root->val==r;
    }
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        int l=p->val;
        int r=q->val;
        bool s=dfs(root,l,r);
        return ans;
     }
};
//这个递归就用的很牛逼了，需要仔细学习思考一下！
//运用到了递归时自底向上的性质
```

