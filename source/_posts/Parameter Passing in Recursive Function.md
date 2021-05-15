---

title: Parameter Passing in Recursive Function
date: 2021-05-15 14:00:25
updated: 2020-05-15 14:00:25
categories: [tech]
---

### N皇后问题的一些思考

```
-从N皇后问题探究递归和回溯的写法模板和需要思考的核心问题
-如何为递归函数设置传递参数？递归函数需要怎样的参数？
-从N皇后问题看递归函数传递参数的空间复杂度
-PLAN1:我们可以把需要用到的二维矩阵定义成类成员变量
-PLAN2：我们可以在递归传参的时候传递引用
-PLAN3：一般的递归函数传递参数即拷贝传参
```

#### Get Start

##### 1.回溯模板

```
void backtracking(参数) {
    if (终止条件) {
        存放结果;
        return;
    }
    for (选择：本层集合中元素（树中节点孩子的数量就是集合的大小）) {
        处理节点;
        backtracking(路径，选择列表); // 递归
        回溯，撤销处理结果
    }
}
```



##### 2.引用传参写法

```c++
class Solution {
private:
vector<vector<string>> result;
// n 为输入的棋盘大小
// row 是当前递归到***的第几行了
void backtracking(int n, int row, vector<string>& chessboard) {
    if (row == n) {
        result.push_back(chessboard);
        return;
    }
    for (int col = 0; col < n; col++) {
        if (isValid(row, col, chessboard, n)) { // 验证合法就可以放
            chessboard[row][col] = 'Q'; // 放置皇后
            backtracking(n, row + 1, chessboard);
            chessboard[row][col] = '.'; // 回溯，撤销皇后
        }
    }
}
bool isValid(int row, int col, vector<string>& chessboard, int n) {
    int count = 0;
    // 检查列
    for (int i = 0; i < row; i++) { // 这是一个剪枝
        if (chessboard[i][col] == 'Q') {
            return false;
        }
    }
    // 检查 45度角是否有皇后
    for (int i = row - 1, j = col - 1; i >=0 && j >= 0; i--, j--) {
        if (chessboard[i][j] == 'Q') {
            return false;
        }
    }
    // 检查 135度角是否有皇后
    for(int i = row - 1, j = col + 1; i >= 0 && j < n; i--, j++) {
        if (chessboard[i][j] == 'Q') {
            return false;
        }
    }
    return true;
}
public:
    vector<vector<string>> solveNQueens(int n) {
        result.clear();
        std::vector<std::string> chessboard(n, std::string(n, '.'));
        backtracking(n, 0, chessboard);
        return result;
    }
};
```

时间空间复杂度：

```
4 ms	7.3 MB
```

##### 3.不使用引用的传参写法 

```c++
class Solution {
private:
vector<vector<string>> result;
// n 为输入的棋盘大小
// row 是当前递归到***的第几行了
void backtracking(int n, int row, vector<string> chessboard) {
    if (row == n) {
        result.push_back(chessboard);
        return;
    }
    for (int col = 0; col < n; col++) {
        if (isValid(row, col, chessboard, n)) { // 验证合法就可以放
            chessboard[row][col] = 'Q'; // 放置皇后
            backtracking(n, row + 1, chessboard);
            chessboard[row][col] = '.'; // 回溯，撤销皇后
        }
    }
}
bool isValid(int row, int col, vector<string> chessboard, int n) {
    int count = 0;
    // 检查列
    for (int i = 0; i < row; i++) { // 这是一个剪枝
        if (chessboard[i][col] == 'Q') {
            return false;
        }
    }
    // 检查 45度角是否有皇后
    for (int i = row - 1, j = col - 1; i >=0 && j >= 0; i--, j--) {
        if (chessboard[i][j] == 'Q') {
            return false;
        }
    }
    // 检查 135度角是否有皇后
    for(int i = row - 1, j = col + 1; i >= 0 && j < n; i--, j++) {
        if (chessboard[i][j] == 'Q') {
            return false;
        }
    }
    return true;
}
public:
    vector<vector<string>> solveNQueens(int n) {
        result.clear();
        std::vector<std::string> chessboard(n, std::string(n, '.'));
        backtracking(n, 0, chessboard);
        return result;
    }
};
```

时间空间复杂度：

```c++
88 ms	49.2 MB
```

##### 4.使用类成员变量减少参数传递的写法

```c++
class Solution{
private:
  int n;
  vector<vector<string>> ans;
  vector<string> cur;
  bool isvalid(int row,int col){
     for(int i=0;i<row;i++){
        if(cur[i][col]=='Q')return false;
     }
     for(int i=row-1,j=col-1;i>=0&&j>=0;i--,j--){
         if(cur[i][j]=='Q')return false;
     }
      for(int i=row-1,j=col+1;i>=0&&j<n;i--,j++){
         if(cur[i][j]=='Q')return false;
     }
     return true;
  }
  void backtracking(int row){
      if(row==n){
          ans.push_back(cur);
          return;
      }
      for(int i=0;i<n;i++){
          if(isvalid(row,i)){
              cur[row][i]='Q';
              backtracking(row+1);
              cur[row][i]='.';
          }
      }
  }
public:
    vector<vector<string>> solveNQueens(int n) {
      this->n=n;
      cur=vector<string>(n,string(n,'.'));
      backtracking(0);
      return ans;
    }
 }
```

时间空间复杂度：

```
4 ms	7.3 MB
```

#### Conclusion

递归和回溯如果缺乏经验可以回忆模板的写法。

在写递归函数的时候尤其是传递参数的规模比较大的时候，要避免拷贝传参的情况，尽量使用引用，

或者通过类成员变量来减少不必要的参数传递。