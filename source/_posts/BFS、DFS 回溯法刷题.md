---
title:  BFS、DFS 回溯法刷题
date:  2021-03-09 00:02:39
categories: 
- 刷题
tags:
- javascript
- 数据结构和算法
- BFS、DFS 回溯法
---

按照算法和数据结构进行分类，一起来刷题，用于自己在面试前查漏补缺。我的意向岗位是前端，选择用javascript来刷题，优点是动态语言，语法简单，缺点是遇见复杂数据结构会出现较难的写法，如堆、并查集，每题对应leetcode的题号。本篇是BFS、DFS 回溯法
<!-- more -->

## 专题部分

### BFS、DFS 回溯法

#### [17. 电话号码的字母组合](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)

给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。答案可以按 **任意顺序** 返回。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210710154048.png)

 **示例 1：**

```
输入：digits = "23"
输出：["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

**示例 2：**

```
输入：digits = ""
输出：[]
```

**示例 3：**

```
输入：digits = "2"
输出：["a","b","c"]
```

**提示：**

- `0 <= digits.length <= 4`
- `digits[i]` 是范围 `['2', '9']` 的一个数字。

使用回溯发怎么保存结果，可以数组保存路径

```javascript
/**
 * @param {string} digits
 * @return {string[]}
 */
let letterMap = [ 
    " ",    // 0
    "",     // 1
    "abc",  // 2
    "def",  // 3
    "ghi",  // 4
    "jkl",  // 5
    "mno",  // 6
    "pqrs", // 7
    "tuv",  // 8
    "wxyz"  // 9
];
var letterCombinations = function(digits) {
    if (digits.length === 0) return [];
    let res = [];
    let aux = [];
    let len = digits.length;
    let backTrack = function (index) {
        if (index >= len) {
            res.push([...aux].join(''));
            return;
        }
        let digit = digits[index];
        let string = letterMap[digit];
        for(let i = 0 ; i < string.length ; i++) {
            aux.push(string[i]);
            backTrack(index + 1);
            aux.pop();
        }
    }
    backTrack(0);
    return res;
};
```

#### [22. 括号生成](https://leetcode-cn.com/problems/generate-parentheses/)

数字 `n` 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。 

**示例 1：**

```
输入：n = 3
输出：["((()))","(()())","(())()","()(())","()()()"]
```

**示例 2：**

```
输入：n = 1
输出：["()"]
```

**提示：**

- `1 <= n <= 8`

同时记录`(`和`)`的数量

```javascript
/**
 * @param {number} n
 * @return {string[]}
 */
var generateParenthesis = function(n) {
    if (n == 0) return [];
    // 记录所有合法的括号组合
    let res = [];
    // 回溯过程中的路径
    let track = "";
    // 可用的左括号和右括号数量初始化为 n
    trackback(n, n, track, res);
    return res;
};

function trackback (left, right, track, res){
    // 若左括号剩下的多，说明不合法
    if (right < left) return;
    // 数量小于 0 肯定是不合法的
    if (left < 0 || right < 0) return;
    // 当所有括号都恰好用完时，得到一个合法的括号组合
    if (left === 0 && right === 0){
        res.push(track);
        return;
    }
   // 尝试放一个左括号
   // 选择
    trackback(left - 1, right, track + '(', res);
    // 撤消选择

    // 尝试放一个右括号
    // 选择
    trackback(left, right - 1, track + ')', res);
    // 撤消选择
};
```

#### [39. 组合总和](https://leetcode-cn.com/problems/combination-sum/)

给定一个**无重复元素**的数组 `candidates` 和一个目标数 `target` ，找出 `candidates` 中所有可以使数字和为 `target` 的组合。

`candidates` 中的数字可以无限制重复被选取。

**说明：**

- 所有数字（包括 `target`）都是正整数。
- 解集不能包含重复的组合。 

**示例 1：**

```
输入：candidates = [2,3,6,7], target = 7,
所求解集为：
[
  [7],
  [2,2,3]
]
```

**示例 2：**

```
输入：candidates = [2,3,5], target = 8,
所求解集为：
[
  [2,2,2,2],
  [2,3,3],
  [3,5]
]
```

**提示：**

- `1 <= candidates.length <= 30`
- `1 <= candidates[i] <= 200`
- `candidate` 中的每个元素都是独一无二的。
- `1 <= target <= 500`

保留完成的路径，需要剪枝优化路径

```javascript
/**
 * @param {number[]} candidates
 * @param {number} target
 * @return {number[][]}
 */
var combinationSum = function(candidates, target) {
    let res = [], cur = [];
    // 从小到大排序，方便剪枝
    candidates.sort((a, b) => a - b);
    dfs(target, 0);
    return res;
    // residue，剩下的树，begin开始的位置
    function dfs(residue, begin){
        if(residue === 0){
            res.push([...cur]);
            return ;
        }
        for(let i = begin; i < candidates.length; i++){
            if(residue - candidates[i] < 0){
                return;
            }
            // 加入当前的树
            cur.push(candidates[i]);
            // DFS
            dfs(residue - candidates[i], i);
            // 撤回
            cur.pop();
        }
    }
};
```

写得完整一些

```javascript
/**
 * @param {number[]} candidates
 * @param {number} target
 * @return {number[][]}
 */
var combinationSum = function(candidates, target) {
    let res = [];
    let len = candidates.length;
    candidates.sort((a, b) => a - b);
    dfs(candidates, len, target, 0, [], res);
    return res;
};
 /**
 * @param candidates 数组输入
 * @param len        输入数组的长度，冗余变量
 * @param residue    剩余数值
 * @param begin      本轮搜索的起点下标
 * @param path       从根结点到任意结点的路径
 * @param res        结果集变量
 */
function dfs(candidates, len, residue, begin, path, res) {
    if (residue == 0) {
        // 由于 path 全局只使用一份，到叶子结点的时候需要做一个拷贝
        res.push([...path]);
        return;
    }
    for (let i = begin; i < len; i++) {
        // 在数组有序的前提下，剪枝
        if (residue < candidates[i]) break;

        // 回溯
        path.push(candidates[i]);
        dfs(candidates, len, residue - candidates[i], i, path, res);
        path.pop();
    }
}
```

#### 46.[全排列](https://leetcode-cn.com/problems/permutations/)

给定一个 **没有重复** 数字的序列，返回其所有可能的全排列。

**示例:**

```
输入: [1,2,3]
输出:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

直接用track记录路径

```javascript
/**
 * @param {number[]} nums
 * @return {number[][]}
 */
/* 主函数，输入一组不重复的数字，返回它们的全排列 */
var permute = function(nums) {
    const res = [];

    // 记录「路径」
    const track = [];
    const n = nums.length;
    const isVisited = new Array(n).fill(false);
    backtrack(nums, track);
    return res;

    // 路径：记录在 track 中
    // 选择列表：nums 中不存在于 track 的那些元素
    // 结束条件：nums 中的元素全都在 track 中出现
    function backtrack (nums, track){
        // 触发结束条件
        if (track.length === n){
            res.push([...track]);
            return;
        }
        for (let i = 0; i < n; i++){
            // 排除不合法的选择
            if (!isVisited[i]){
                // 做选择
                isVisited[i] = true;
                track.push(nums[i]);
                // 进入下一层决策树
                backtrack(nums, track);
                // 取消选择
                isVisited[i] = false;
                track.pop();
            }
        }
    }
};
```

#### 47.[全排列 II](https://leetcode-cn.com/problems/permutations-ii/)

给定一个可包含重复数字的序列 `nums` ，**按任意顺序** 返回所有不重复的全排列。

**示例 1：**

```
输入：nums = [1,1,2]
输出：
[[1,1,2],
 [1,2,1],
 [2,1,1]]
```

**示例 2：**

```
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

**提示：**

- `1 <= nums.length <= 8`
- `-10 <= nums[i] <= 10`

注意通过排序和跳过相同的数进行剪枝

```javascript
/**
 * @param {number[]} nums
 * @return {number[][]}
 */
var permuteUnique = function(nums) {
    /**
     * 回溯法
     * backtrack 解题题公式
     */
    const res = [];
    const len = nums.length;
    const used = new Array(len);
    // 升序排序
    nums.sort((a, b) => a - b);

    function dfs (path) {
        // 个数选够了
        if (path.length == len) {
            // path的拷贝 加入解集
            res.push([...path]);
            // 结束当前递归 结束当前分支
            return;
        }

        // 枚举出所有的选择
        for (let i = 0; i < len; i++) {
            // 避免产生重复的排列
            if (nums[i - 1] == nums[i] && i - 1 >= 0 && !used[i - 1]) continue;
            // 这个数使用过了，跳过。
            if (used[i]) continue;
            // make a choice
            path.push(nums[i]);
            // 记录路径上做过的选择
            used[i] = true;
            // explore，基于它继续选，递归
            dfs(path);
            // undo the choice
            path.pop();
            // 也要撤销一下对它的记录
            used[i] = false;
        }
    };

    dfs([]);
    return res;
};
```

#### 51.[N 皇后](https://leetcode-cn.com/problems/n-queens/)

**n 皇后问题** 研究的是如何将 `n` 个皇后放置在 `n×n` 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 `n` ，返回所有不同的 **n 皇后问题** 的解决方案。

每一种解法包含一个不同的 **n 皇后问题** 的棋子放置方案，该方案中 `'Q'` 和 `'.'` 分别代表了皇后和空位。

**示例 1：**

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210419095805.jpeg)

```
输入：n = 4
输出：[[".Q..","...Q","Q...","..Q."],["..Q.","Q...","...Q",".Q.."]]
解释：如上图所示，4 皇后问题存在两个不同的解法。
```

**示例 2：**

```
输入：n = 1
输出：[["Q"]]
```

**提示：**

- `1 <= n <= 9`
- 皇后彼此不能相互攻击，也就是说：任何两个皇后都不能处于同一条横行、纵行或斜线上。

```javascript
/**
 * @param {number} n
 * @return {string[][]}
 */
var solveNQueens = function(n) {
    let res = [];
    backtrack(n, []);
    return res;

    function backtrack(n, tmp) {
        if (tmp.length == n) {
            let arr = [];
            for (let i = 0; i < n; i++) {
                arr[i] = ".".repeat(tmp[i]) + "Q" + ".".repeat(n - tmp[i] - 1);
            }
            res.push(arr);
        }
        for (let j = 0; j < n; j++) {
            if (isValid(tmp, j)) {
                tmp.push(j);
                backtrack(n, tmp);
                tmp.pop();
            }
        }
    }
    function isValid(arr, j) {
        let i = arr.length;
        for (let x = 0; x < i; x++) {
            let y = arr[x];
            if  (y == j || x + y == i + j || x - y == i - j) return false;
        }
        return true;
    }
};
```

回溯

```javascript
var solveNQueens = function(n) {
    let res = [];
    dfs([], 0);
    return res;
    // 递归调用，初始为空数组（每行皇后的列位置），从第0行开始
    function dfs (subRes, row) {
        if(row === n) {
            // 将每行展开为..Q..形式
            return res.push(subRes.map(i => '.'.repeat(i) + 'Q' + '.'.repeat(n - i -1)));
        }

        // 遍历每一列，使得目前行数的皇后在第col列 
        for (let col = 0; col < n; col++ ) {
            // 遍历数组使得皇后不互相攻击
            if(!subRes.some((j, k) => (j === col || Math.abs(j - col) === Math.abs(k - row)))) {
                // 执行操作
                subRes.push(col);
                // 回溯
                dfs(subRes, row + 1);
                // 撤回
                subRes.pop();
            }
        }
    }
};
```

#### 52.[N皇后 II](https://leetcode-cn.com/problems/n-queens-ii/)

**n 皇后问题** 研究的是如何将 `n` 个皇后放置在 `n×n` 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 `n` ，返回 **n 皇后问题** 不同的解决方案的数量。

**示例 1：**

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210419095744.jpeg)

```
输入：n = 4
输出：2
解释：如上图所示，4 皇后问题存在两个不同的解法。
```

**示例 2：**

```
输入：n = 1
输出：1
```

**提示：**

- `1 <= n <= 9`
- 皇后彼此不能相互攻击，也就是说：任何两个皇后都不能处于同一条横行、纵行或斜线上。

```javascript
/**
 * @param {number} n
 * @return {number}
 */
var totalNQueens = function(n) {
    let res = 0;
    backtrack(n, []);
    return res;

    function backtrack(n, tmp) {
        if (tmp.length === n) {
            // 已回溯到最后一行，找到一种方案
            res++;
            return;
        }
        // 尝试当前行放入第j列
        for (let j = 0; j < n; j++) {
            // 如果可行，放入当前列
            if (isValid(tmp, j)) {
                // 记录当前选择
                tmp.push(j);
                //继续下一次递归
                backtrack(n, tmp);
                //撤销当前选择
                tmp.pop();
            }
        }

    }
    // 判断当前列是否有效
    function isValid(arr, col) {
        let len = arr.length;
        for (let x = 0; x < len; x++) {
            let y = arr[x];
            if (y === col || x - y === len - col || x + y === col + len) return false;
        }
        return true;
    }
};
```

#### [78. 子集](https://leetcode-cn.com/problems/subsets/)

给你一个整数数组 `nums` ，数组中的元素 **互不相同** 。返回该数组所有可能的子集（幂集）。

解集 **不能** 包含重复的子集。你可以按 **任意顺序** 返回解集。 

**示例 1：**

```
输入：nums = [1,2,3]
输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

**示例 2：**

```
输入：nums = [0]
输出：[[],[0]]
```

**提示：**

- `1 <= nums.length <= 10`
- `-10 <= nums[i] <= 10`
- `nums` 中的所有元素 **互不相同**

回溯法，遍历各种可行方法，路径重复剪枝

```js
/**
 * @param {number[]} nums
 * @return {number[][]}
 */
// 数学归纳法
var subsets = function(nums) {
    const res = [];

    // 记录「路径」
    const track = [];
    backtrack(nums, 0, track);
    return res;

    function backtrack (nums, start, track){
        res.push(track);
        
        for (let i = start; i < nums.length; i++){
            // 排除不合法的选择
            if (!track.includes(nums[i])){
                // 做选择
                track.push(nums[i]);
                // 进入下一层决策树
                backtrack(nums, i + 1, [...track]);
                // 取消选择
                track.pop();
            }
        }
    }
};
```

数学归纳法

```js
/**
 * @param {number[]} nums
 * @return {number[][]}
 */
// 数学归纳法
var subsets = function(nums) {
    // base case，返回一个空集
    if (nums.length == 0) return [[]];
    let n = nums.pop();
    // 先递归算出前面元素的所有子集
    let res = subsets(nums);

    let len = res.length;
    for (let i = 0; i < len; i++) {
        // 然后在之前的结果之上追加
        res.push([...res[i], n]);
    }
    return res;
};
```

将上述数学归纳法中的递归改为回溯

```js
/**
 * @param {number[]} nums
 * @return {number[][]}
 */
// 数学归纳法
var subsets = function(nums) {
    let res=[[]];
    for(let i = 0; i < nums.length; i++){
        for(let j = 0, len = res.length; j < len; j++){
            res.push(res[j].concat(nums[i]));
        }
    }
    return res;
};
```

#### [79. 单词搜索](https://leetcode-cn.com/problems/word-search/)

给定一个 `m x n` 二维字符网格 `board` 和一个字符串单词 `word` 。如果 `word` 存在于网格中，返回 `true` ；否则，返回 `false` 。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

**示例 1：**

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210711174412.jpeg)

```
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
输出：true
```

示例 2：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210711174506.jpeg)

```
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "SEE"
输出：true
```

示例 3：

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210711174617.jpeg)

```
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCB"
输出：false
```


提示：

m == board.length
n = board[i].length
1 <= m, n <= 6
1 <= word.length <= 15
board 和 word 仅由大小写英文字母组成


进阶：你可以使用搜索剪枝的技术来优化解决方案，使其在 board 更大的情况下可以更快解决问题？

```javascript
/**
 * @param {character[][]} board
 * @param {string} word
 * @return {boolean}
 */
var exist = function(board, word) {
    if (board.length === 0 || board[0].length === 0) return false;
    if (word.length === 0) return true;
    let row = board.length;
    let col = board[0].length;
    for (let i = 0; i < row; i ++) {
        for (let j = 0; j < col; j++) {
            // 存在一个序列即返回true
            if (backtrack(i, j, 0)) return true;
        }
    }
    return false;
    // 行，列，当前索引
    function backtrack(i, j, index) {
        if(i >= row || i < 0 || j >= col || j < 0) return false;
        const cur = board[i][j];
        if (cur !== word[index]) return false;
        if (index === word.length - 1) return true;
        // 该位置的数对应单次的当前索引
        // 修改当前节点状态
        board[i][j] = 0;
        // 利用短路效应剪枝回溯
        const res = backtrack(i + 1, j, index + 1) || backtrack(i - 1, j, index + 1) || backtrack(i, j + 1, index + 1) || backtrack(i, j - 1, index + 1);
        // 回改当前节点状态
        board[i][j] = cur;
        return res;
    }
};
```

当然也可以用额外的空间存储

```javascript
/**
 * @param {character[][]} board
 * @param {string} word
 * @return {boolean}
 */
var exist = function(board, word) {
    const m = board.length;
    const n = board[0].length;
    // 二维矩阵used
    const used = new Array(m);
    for (let i = 0; i < m; i++) {
        used[i] = new Array(n);
    }
    // 判断当前点是否是目标路径上的点
    function canFind (row, col, i) {
        // row col是当前点的坐标，i是当前考察的字符索引
        // 递归的出口
        if (i > word.length - 1) {  
            return true;
        }
        // 当前点要存在
        if (row < 0 || row >= m || col < 0 || col >= n) { 
            return false;
        }
        // 当前的点已经走过，或当前点就不是目标点
        if (used[row][col] || board[row][col] != word[i]) { 
            return false;
        }
        // 排除掉这些false情况，当前点是没问题的，可以继续递归考察
        // used记录一下当前点被访问了
        used[row][col] = true; 
        const canFindRest =
            canFind(row + 1, col, i + 1) ||
            canFind(row - 1, col, i + 1) ||
            canFind(row, col + 1, i + 1) ||
            canFind(row, col - 1, i + 1); 
        // 基于当前点，可以为剩下的字符找到路径
        if (canFindRest) { 
            return true;    
        }
        // 找不出，返回false，继续考察别的分支，并撤销当前点的访问状态。
        used[row][col] = false; 
        return false;
    };

    for (let i = 0; i < m; i++) {
        for (let j = 0; j < n; j++) {
            // 找到dfs的起点
            if (board[i][j] == word[0] && canFind(i, j, 0)) {
                // 找到起点，且dfs的结果也true，则找到了目标路径
                return true;
            }
        }
    }
    // 怎么样都没有返回true，则返回false
    return false;
};
```

#### 200.[岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)

给你一个由 `'1'`（陆地）和 `'0'`（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

**示例 1：**

```
输入：grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
输出：1
```

**示例 2：**

```
输入：grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
输出：3
```

**提示：**

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 300`
- `grid[i][j]` 的值为 `'0'` 或 `'1'`

DFS深度优先

```javascript
/**
 * @param {character[][]} grid
 * @return {number}
 */
var numIslands = function(grid) {
    let row = grid.length;
    if(!row) return 0;
    let col = grid[0].length;
    let res = 0;
    // DFS，将连在一起的几个 ‘1’全部转为‘0’
    function DFS(grid,r,c){
        grid[r][c]='0';
        if(r-1>=0&&grid[r-1][c] =='1') DFS(grid,r-1,c);
        if(r+1<row&&grid[r+1][c] =='1') DFS(grid,r+1,c);
        if(c-1>=0&&grid[r][c-1] =='1') DFS(grid,r,c-1);
        if(c+1<col&&grid[r][c+1] =='1') DFS(grid,r,c+1);
    }

    for(let i = 0;i<row;++i){
        for(let j = 0;j<col;++j){
 
            if(grid[i][j] == '1'){

                res++;

                DFS(grid,i,j)
            }
        }
    }
    return res
};
```

BFS

```javascript
/**
 * @param {character[][]} grid
 * @return {number}
 */
var numIslands = function(grid) {
    if (grid.length == 0) return 0;
    let lenx = grid.length;
    let leny = grid[0].length;
    let num_islands = 0;
    for(let i = 0; i < lenx; i++) {
        for(let j = 0; j < leny; j++){
            if (grid[i][j] == 1) {
               num_islands++;
               grid[i][j] = 0;
               neighbors = new Array();
               neighbors.push([i, j]);
               while (neighbors.length != 0){
                   let x = neighbors[0][0];
                   let y = neighbors[0][1];
                   if (x - 1 >= 0 && grid[x - 1][y] == 1) {
                        neighbors.push([x - 1, y]);
                        grid[x - 1][y] = 0;
                    }
                    if (x + 1 < lenx && grid[x + 1][y] == 1) {
                        neighbors.push([x + 1, y]);
                        grid[x + 1][y] = 0;
                    }
                    if (y - 1 >= 0 && grid[x][y - 1] == 1) {
                        neighbors.push([x, y - 1]);
                        grid[x][y - 1] = 0;
                    }
                    if (y + 1 < leny && grid[x][y + 1] == 1) {
                        neighbors.push([x, y + 1]);
                        grid[x][y + 1] = 0;
                    }
                    neighbors.shift();
               }
            }
        }
    }
    return num_islands;
};
```

#### 寻找和为定值的多个数

题目描述

输入两个整数n和sum，从数列1，2，3.......n 中随意取几个数，使其和等于sum，要求将其中所有的可能组合列出来。

解法

```javascript
function getAllCombin(array, n, sum, temp) {
    if (temp.length === n) {
        if (temp.reduce((t, c) => t + c) === sum) {
            return temp;
        }
        return;
    }
    for (let i = 0; i < array.length; i++) {
        const current = array.shift();
        temp.push(current);
        const result = getAllCombin(array, n, sum, temp);
        if (result) {
            return result;
        }
        temp.pop();
        array.push(current);
    }
}
const arr = [1, 2, 3, 4, 5, 6];

console.log(getAllCombin(arr, 3, 10, []));
```

