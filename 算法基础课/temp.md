---
date: 2025-10-21T22:01:00
tags:
  - C++
  - Algorithm
  - Graph Theory
  - Search
---

# 🎓 AcWing——算法基础课

![Language](https://img.shields.io/badge/Language-C%2B%2B-00599C?style=flat-square&logo=c%2B%2B)
![Topic](https://img.shields.io/badge/Topic-Graph%20%26%20Search-orange?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-lightgrey?style=flat-square)
![Created](https://img.shields.io/badge/Created-2025--10--22-lightgrey?style=flat-square)

> 本笔记为 **第三讲 · 搜索与图论** 的 C++ 模板与题解，内容涵盖 DFS、BFS、最短路、最小生成树、二分图等核心概念。

---

# 📖 第三讲 搜索与图论

## 🧭 1. DFS (深度优先搜索)

> **核心思想**：深度优先搜索（DFS）是一种用于遍历或搜索树或图的算法。它会沿着一条路径尽可能深地搜索，直到到达末端，然后回溯到上一个节点，继续探索其他未访问过的路径。DFS 通常通过递归或显式栈来实现，常用于解决排列组合、N皇后、寻找路径等问题。

### 1.1 排列数字

<details>
<summary><strong>🔗 练习平台与题解</strong></summary>

-   **洛谷**: [P1706 全排列问题](https://www.luogu.com.cn/problem/P1706)
-   **AcWing**: [842. 排列数字](https://www.acwing.com/problem/content/844/)

![image-20240130161746893](assets/image-20240130161746893.png)

> **解法思路**：
>
> 这是一个典型的回溯问题。我们按顺序为每个位置（`u`）选择一个可用的数字（`i`）。
>
> 1.  **递归终止条件**：当所有位置（`u > n` 或 `sz == n`）都已填满，输出当前排列。
> 2.  **选择列表**：对于当前位置 `u`，我们可以从 `1` 到 `n` 中选择一个尚**未使用**的数字。
> 3.  **路径与状态**：
>     -   `path[]` 数组记录当前排列。
>     -   `st[]` (或 `used[]`) 布尔数组标记某个数字是否已被使用。
> 4.  **回溯**：当填完下一个位置 `u+1` 并返回后，需要将当前位置 `u` 所做的选择撤销（即 `st[i] = false;`），以便尝试其他可用的数字。

> **写法 1: AcWing 题解**

```cpp
#include<iostream>
using namespace std;
const int N = 10;
int path[N]; //保存序列
bool st[N];  //数字是否被用过，标记数组 
int n; 

void dfs(int u)
{
    if(u > n) //数字填完了，输出
    {
        for(int i = 1; i <= n; i++) //输出方案
            cout << path[i] << " ";
        cout << endl;
    }
    else
    {
        for(int i = 1; i <= n; i++) //空位上可以选择的数字为:1 ~ n
        {
            if(!st[i]) //如果数字 i 没有被用过
            {
                path[u] = i; //放入空位
                st[i] = true; //数字被用，修改状态
                dfs(u + 1); //填下一个位
                //回溯：原路返回，把原来满足条件的位置的状态修改回来
                st[i] = false;
            }
        }
    }
}

int main()
{
    cin >> n;
    dfs(1);
    return 0;
}
```

> **写法 2: 洛谷 P1706 题解 (推荐)**

```cpp
#include<iostream>
#include<iomanip>
using namespace std;
const int N = 10;
int path[N];
bool used[N];
int sz;

void backtracking(int n) {
    if (sz == n) {
        for (int i = 0; i < n; i++) {
            cout << setw(5) << path[i];
        }
        cout << endl;
        return;
    }
    for (int i = 1; i <= n; i++) {
        if (used[i] == false) {
            path[sz++] = i;
            used[i] = true;
            backtracking(n);
            // 回溯
            sz--;
            used[i] = false;
        }
    }
}

int main() {
    int n;
    scanf("%d", &n);
    backtracking(n);
    return 0;
}
```

</details>

### 1.2 N-皇后问题

<details>
<summary><strong>🔗 练习平台与题解</strong></summary>

-   **洛谷**: [T247305 N皇后问题](https://www.luogu.com.cn/problem/T247305)
-   **AcWing**: [843. n-皇后问题](https://www.acwing.com/problem/content/845/)

![image-20240131095222180](assets/image-20240131095222180.png)
![image-20240131112533511](assets/image-20240131112533511.png)

> **解法思路**：
>
> 按行（或列）依次放置皇后，确保新放置的皇后不与任何已存在的皇后在同一列或同一对角线上。
>
> 1.  **按行搜索**：`dfs(y)` 或 `backtracking(row)` 表示在第 `y` 行放置皇后。
> 2.  **剪枝**：为了快速判断位置是否冲突，可以使用多种方法：
>     -   **方法1 (高效)**: 使用三个布尔数组 `col[]`, `dg[]`, `udg[]` 分别标记列、主对角线和副对角线。
>     -   **方法2 (直观)**: 每次放置前，向上检查当前列、左上对角线、右上对角线是否有皇后。
> 3.  **回溯**：当一行放置成功并递归到下一行后，需要撤销当前行的选择，以便在上一层递归中尝试其他列。

> **写法 1: AcWing 题解 (高效)**

```cpp
#include <iostream>
using namespace std;
const int N = 20;

int n;
char g[N][N];
bool col[N], dg[N * 2], udg[N * 2]; // 对角线数组大小要开2倍

// 按行搜索
void dfs(int y) {
    if (y == n) { // 所有行都已成功放置
        for (int i = 0; i < n; i ++ ) puts(g[i]);
        puts("");
        return;
    }

    // 遍历当前行的每一列
    for (int x = 0; x < n; x ++ ) {
        // 剪枝：判断列、主对角线、副对角线是否冲突。+n是为了保证下标非负
        if (!col[x] && !dg[y - x + n] && !udg[y + x]) {
            g[y][x] = 'Q';
            col[x] = dg[y - x + n] = udg[y + x] = true;
            dfs(y + 1);
            //回溯：产生了一种方案后原路返回，把原来满足条件的位置的状态修改回来
            col[x] = dg[y - x + n] = udg[y + x] = false;
            g[y][x] = '.';
        }
    }
}

int main() {
    cin >> n;
    for (int i = 0; i < n; i ++ ) {
        for (int j = 0; j < n; j ++ ) {
            g[i][j] = '.';
        }
    }
    dfs(0);
    return 0;
}
```

> **写法 2: 洛谷题解 (直观)**

```cpp
#include<iostream>
using namespace std;
const int N = 10;
int n;
char board[N][N];

bool isValid(int row, int col) {
    // 检查列
    for (int i = 0; i < row; i++) {
        if (board[i][col] == 'Q') {
            return false;
        }
    }
    // 检查左上对角线
    for (int i = row - 1, j = col - 1; i >= 0 && j >= 0; i--, j--) {
        if (board[i][j] == 'Q') {
            return false;
        }
    }
    // 检查右上对角线
    for (int i = row - 1, j = col + 1; i >= 0 && j < n; i--, j++) {
        if (board[i][j] == 'Q') {
            return false;
        }
    }
    return true;
}

void backtracking(int row) {
    if (row == n) {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                printf("%c", board[i][j]);
            }
            printf("\n");
        }
        printf("\n");
        return;
    }
    for (int i = 0; i < n; i++) {
        if (isValid(row, i)) {
            board[row][i] = 'Q';
            backtracking(row + 1);
            board[row][i] = '.';
        }
    }
}

int main() {
    scanf("%d", &n);
    for (int i=0; i<n; ++i) {
        for (int j=0; j<n; ++j) {
            board[i][j] = '.';
        }
    }
    backtracking(0);
    return 0;
}```


```



</details>

---



## 🌊 2. BFS (广度优先搜索)

> **核心思想**：广度优先搜索（BFS）是另一种图遍历算法。它从一个起始节点开始，首先访问其所有相邻节点，然后逐层向外扩展，访问更远的节点。BFS 总是能找到**无权图**中两点之间的最短路径。它通常通过队列来实现。

### 2.1 走迷宫

<details>
<summary><strong>🔗 练习平台与题解</strong></summary>

-   **牛客**: [走迷宫](https://www.nowcoder.com/practice/e88b41dc6e764b2893bc4221777ffe64)
-   **AcWing**: [844. 走迷宫](https://www.acwing.com/problem/content/846/)
- **洛谷**：

  https://www.luogu.com.cn/problem/P1238

  https://www.luogu.com.cn/problem/T567665

![image-20240131143848023](assets/image-20240131143848023.png)
![image-20240131161338393](assets/image-20240131161338393.png)

> **解法思路**：
>
> 迷宫问题是 BFS 的经典应用，因为题目要求最少步数，等价于图中的最短路径。
>
> 1.  **队列**：用一个队列 `q` 存储待访问的坐标。
> 2.  **距离/访问数组**：用一个二维数组 `d[N][N]` 存储从起点到每个点的最短距离。`d` 数组也兼具标记功能，`d[x][y] == -1` 表示该点未被访问过。或者用一个布尔数组 `visited` 单独标记。
> 3.  **流程**：
>     -   将起点入队，并标记为已访问，距离为0。
>     -   当队列不为空时，取出队头元素 `t`。
>     -   遍历 `t` 的四个方向，对于合法（未越界、可通行、未访问）的邻居：
>         -   更新其距离/状态，并将其入队。
> 4.  **终点**：当第一次到达终点时，其距离就是答案。

> **写法 1: AcWing 题解**

```cpp
#include <iostream>
#include <cstring>
#include <queue>
using namespace std;

typedef pair<int, int> PII;
const int N = 110;

int n, m;
int g[N][N]; // 迷宫
int d[N][N]; // 距离数组

int bfs() {
    queue<PII> q;
    memset(d, -1, sizeof d);

    q.push({0, 0});
    d = 0;

    int dx[] = {-1, 0, 1, 0}, dy[] = {0, 1, 0, -1};

    while (!q.empty()) {
        PII t = q.front();
        q.pop();

        for (int i = 0; i < 4; i++) {
            int x = t.first + dx[i], y = t.second + dy[i];
            // 判断是否越界、可走、未访问
            if (x >= 0 && x < n && y >= 0 && y < m && g[x][y] == 0 && d[x][y] == -1) {
                d[x][y] = d[t.first][t.second] + 1;
                q.push({x, y});
            }
        }
    }
    return d[n - 1][m - 1];
}

int main() {
    cin >> n >> m;
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            cin >> g[i][j];
        }
    }
    cout << bfs() << endl;
    return 0;
}
```



> **写法 2: 牛客题解**

```cpp
#include<iostream>
#include<queue>
using namespace std;
const int N = 1005;
char grid[N][N];
int n, m, srtX, srtY, dstX, dstY;
const int dir = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};

int bfs() {
    int cnt = 0;
    queue<pair<int, int>> que;
    if (grid[srtX][srtY] == '*') {
        return -1;
    }
    que.emplace(srtX, srtY);
    grid[srtX][srtY] = '*'; // 直接修改地图作为访问标记

    while (!que.empty()) {
        int t = que.size(); // 当前层的节点数
        while (t--) {
            auto [x, y] = que.front();
            que.pop();
            if (x == dstX && y == dstY) {
                return cnt;
            }
            for (int i = 0; i < 4; i++) {
                int nx = x + dir[i];
                int ny = y + dir[i];
                if (nx >= 1 && nx <= n && ny >= 1 && ny <= m && grid[nx][ny] == '.') {
                    que.emplace(nx, ny);
                    grid[nx][ny] = '*';
                }
            }
        }
        cnt++; // 层数加一
    }
    return -1;
}

int main() {
    scanf("%d %d", &n, &m);
    scanf("%d %d %d %d", &srtX, &srtY, &dstX, &dstY);
    for (int i = 1; i <= n; i++) {
        scanf("%s", grid[i] + 1);
    }
    printf("%d", bfs());
    return 0;
}
```

</details>

### 2.2 八数码

<details>
<summary><strong>🔗 练习平台与题解</strong></summary>

-   **洛谷**: [P1379 八数码难题](https://www.luogu.com.cn/problem/P1379)
-   **AcWing**: [845. 八数码](https://www.acwing.com/problem/content/847/)

![image-20240131163524526](assets/image-20240131163524526.png)

> **解法思路**：
>
> 这是一个状态空间搜索问题，可以将每个九宫格的布局看作图中的一个节点，移动操作看作边。求最少移动次数，就是求图中最短路。
>
> 1.  **状态表示**：用一个字符串（如 "12345678x"）来唯一表示九宫格的状态。
> 2.  **BFS 框架**：
>     -   用一个队列 `q` 存储状态字符串。
>     -   用一个 `unordered_map<string, int> d` 存储从初始状态到任一状态的最少步数，同样兼具判重功能。
> 3.  **状态转移**：
>     -   从队列中取出当前状态 `t`。
>     -   找到 'x' 的位置，并将其转换为二维坐标 `(x, y)`。
>     -   尝试向四个方向移动 'x'，生成新的状态字符串。
>     -   如果新状态未被访问过（即在 map `d` 中不存在），则更新其距离并将其入队。
> 4.  **终点**：当从队列中取出的状态等于目标状态 "12345678x" 时，返回其距离。

> **AcWing 题解代码**

```cpp
#include <iostream>
#include <string>
#include <queue>
#include <unordered_map>
#include <algorithm>
using namespace std;

int bfs(string start) {
    string end = "12345678x";
    queue<string> q;
    unordered_map<string, int> d;

    q.push(start);
    d[start] = 0;

    int dx[] = {-1, 0, 1, 0}, dy[] = {0, 1, 0, -1};

    while (!q.empty()) {
        string t = q.front();
        q.pop();

        if (t == end) return d[t];

        int dist = d[t];
        int k = t.find('x');
        int x = k / 3, y = k % 3; // 字符串下标转二维坐标

        for (int i = 0; i < 4; i++) {
            int a = x + dx[i], b = y + dy[i];
            if (a >= 0 && a < 3 && b >= 0 && b < 3) {
                string next_state = t;
                swap(next_state[k], next_state[a * 3 + b]); // 交换生成新状态
                if (!d.count(next_state)) { // 如果新状态未访问过
                    d[next_state] = dist + 1;
                    q.push(next_state);
                }
            }
        }
    }
    return -1;
}

int main() {
    string start;
    char c;
    for (int i = 0; i < 9; i++) {
        cin >> c;
        start += c;
    }
    cout << bfs(start) << endl;
    return 0;
}
```

</details>

---

## 🌲 3. 树与图的遍历

> **核心思想**：树和图的遍历是许多更复杂算法的基础。DFS 适合寻找所有解、深入探索路径的问题；BFS 适合寻找最短路（无权）、层序遍历的问题。时间复杂度均为 O(N+M)，其中 N 是点数，M 是边数。
>
> **深度优先遍历模板：**
>
> ```c++
> int dfs(int u)
> {
>     st[u] = true; // st[u] 表示点u已经被遍历过
> 
>     for (int i = h[u]; i != -1; i = ne[i])
>     {
>         int j = e[i];
>         if (!st[j]) dfs(j);
>     }
> }
> ```
>
> **广度优先遍历模板：**
>
> ```c++
> queue<int> q;
> st[1] = true; // 表示1号点已经被遍历过
> q.push(1);
> 
> while (q.size())
> {
>     int t = q.front();
>     q.pop();
> 
>     for (int i = h[t]; i != -1; i = ne[i])
>     {
>         int j = e[i];
>         if (!st[j])
>         {
>             st[j] = true; // 表示点j已经被遍历过
>             q.push(j);
>         }
>     }
> }
> ```





### 3.1 树的重心 (DFS 应用)

<details>
<summary><strong>🔗 练习平台与题解</strong></summary>

- **AcWing**: [846. 树的重心](https://www.acwing.com/problem/content/848/)

- **洛谷**：

  https://www.luogu.com.cn/problem/U104609

  https://www.luogu.com.cn/problem/U164672

![image-20240202144411302](assets/image-20240202144411302.png)
![image-20240202171248800](assets/image-20240202171248800.png)

> **解法思路**：
>
> 树的重心是指删除该节点后，剩余各个连通块中节点数的最大值最小的那个节点。
>
> 1.  **DFS 计算子树大小**：定义 `dfs(u)` 函数，其返回值为以 `u` 为根的子树的节点总数。
> 2.  **遍历与计算**：在 `dfs(u)` 的过程中，对于每个子节点 `j`，我们递归调用 `dfs(j)` 得到其子树大小 `s`。
>     -   那么，删除 `u` 后，以 `j` 为根的这个连通块大小就是 `s`。
>     -   同时，`u` 的上方也形成一个连通块，其大小为 `n - sum`，其中 `sum` 是以 `u` 为根的整个子树的大小。
> 3.  **更新答案**：在 `dfs(u)` 的末尾，我们收集所有以 `u` 的子节点为根的连通块大小，以及 `u` 上方连通块的大小，取它们的最大值 `res`。然后用 `res` 来更新全局的最小值 `ans`。

> **AcWing 题解代码**

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>
using namespace std;

const int N = 1e5 + 10, M = N * 2;
int h[N], e[M], ne[M], idx;
bool st[N];
int n;
int ans = N; // 存储重心的最大连通块大小

void add(int a, int b) {
    e[idx] = b, ne[idx] = h[a], h[a] = idx++;
}

// 返回以u为根的子树的节点数
int dfs(int u) {
    st[u] = true;
    int sum = 1; // 包含节点u本身
    int res = 0; // 存储删除u后，其子树构成的连通块的最大值

    for (int i = h[u]; i != -1; i = ne[i]) {
        int j = e[i];
        if (!st[j]) {
            int s = dfs(j); // s是子树j的大小
            res = max(res, s);
            sum += s;
        }
    }

    res = max(res, n - sum); // u上方的连通块大小
    ans = min(ans, res); // 更新全局答案

    return sum;
}

int main() {
    cin >> n;
    memset(h, -1, sizeof h);
    for (int i = 0; i < n - 1; i++) {
        int a, b;
        cin >> a >> b;
        add(a, b), add(b, a);
    }
    dfs(1);
    cout << ans << endl;
    return 0;
}
```

</details>

### 3.2 图中点的层次 (BFS 应用)

<details>
<summary><strong>🔗 练习平台与题解</strong></summary>

-   **AcWing**: [847. 图中点的层次](https://www.acwing.com/problem/content/849/)
-   **洛谷**：https://www.luogu.com.cn/problem/U322548

> **解法思路**：
>
> 求图中点的层次，等价于求有向图中从 1 号点到 n 号点的最短距离（边权为 1）。这是 BFS 的典型应用。
>
> 1.  **邻接表**：使用邻接表存储有向图。
> 2.  **BFS 框架**：
>     -   队列 `q` 存储待访问节点。
>     -   距离数组 `d[N]` 初始化为 -1，`d[1] = 0`。
>     -   从 1 号点开始 BFS，逐层扩展，更新每个可达节点的 `d` 值。
> 3.  **结果**：最终 `d[n]` 就是 1 号点到 n 号点的最短距离。如果 `d[n]` 仍为 -1，则表示不可达。

> **写法 1: 使用 C++ STL queue**

```cpp
#include <iostream>
#include <cstring>
#include <queue>
using namespace std;

const int N = 100010;
int h[N], e[N], ne[N], idx;
int d[N]; // 存储距离，-1表示未访问
int n, m;

void add(int a, int b) {
    e[idx] = b, ne[idx] = h[a], h[a] = idx++;
}

int bfs() {
    memset(d, -1, sizeof d);
    queue<int> q;

    q.push(1);
    d = 0;

    while (!q.empty()) {
        int t = q.front();
        q.pop();

        for (int i = h[t]; i != -1; i = ne[i]) {
            int j = e[i];
            if (d[j] == -1) {
                d[j] = d[t] + 1;
                q.push(j);
            }
        }
    }
    return d[n];
}

int main() {
    cin >> n >> m;
    memset(h, -1, sizeof h);
    while(m--) {
        int a, b;
        cin >> a >> b;
        add(a, b);
    }
    cout << bfs() << endl;
    return 0;
}
```

> **写法 2: 使用数组模拟队列**

```cpp
#include<iostream>
#include<cstring>
using namespace std;
const int N = 1e5 + 5;
int h[N], e[N], ne[N], d[N];
int q[N];
int idx;
int n, m;

void add(int a, int b) {
    e[idx] = b, ne[idx] = h[a], h[a] = idx++;
}

int bfs() {
    memset(d, -1, sizeof(d));
    int hh = 0, tt = 0;
    q = 1;
    d = 0;

    while (hh <= tt) {
        int cur = q[hh++];
        for (int i = h[cur]; i != -1; i = ne[i]) {
            int j = e[i];
            if (d[j] == -1) {
                d[j] = d[cur] + 1;
                q[++tt] = j;
            }
        }
    }
    return d[n];
}
int main() {
    idx = 0;
    memset(h, -1, sizeof(h));
    scanf("%d %d", &n, &m);
    for (int i = 0; i < m; i++) {
        int a, b;
        scanf("%d %d", &a, &b);
        add(a, b);
    }
    printf("%d", bfs());
    return 0;
}
```

</details>

---

## 📊 4. 拓扑排序

> **核心思想**：拓扑排序是对 **有向无环图（DAG）** 的顶点进行排序，使得对于图中每一条有向边 `(u, v)`，`u` 在排序中都出现在 `v` 之前。经典的实现是 **Kahn算法**：
> 1.  计算所有节点的入度。
> 2.  将所有入度为 0 的节点加入队列。
> 3.  当队列不为空时，出队一个节点 `t`，将其加入拓扑序列。
> 4.  遍历 `t` 的所有出边 `(t, j)`，将 `j` 的入度减 1。若 `j` 的入度变为 0，则将 `j` 入队。
> 5.  若最终拓扑序列的节点数不等于总节点数，说明图中存在环。
>
> 拓扑排序模板：
>
> ```c++
> bool topsort()
> {
>     int hh = 0, tt = -1;
> 
>     // d[i] 存储点i的入度
>     for (int i = 1; i <= n; i ++ )
>         if (!d[i])
>             q[ ++ tt] = i;
> 
>     while (hh <= tt)
>     {
>         int t = q[hh ++ ];
> 
>         for (int i = h[t]; i != -1; i = ne[i])
>         {
>             int j = e[i];
>             if (-- d[j] == 0)
>                 q[ ++ tt] = j;
>         }
>     }
> 
>     // 如果所有点都入队了，说明存在拓扑序列；否则不存在拓扑序列。
>     return tt == n - 1;
> }
> ```
>
> 





<details>
<summary><strong>🔗 练习平台与题解</strong></summary>

-   **洛谷**: [U153876 【模板】拓扑排序](https://www.luogu.com.cn/problem/U153876)
-   **AcWing**: [848. 有向图的拓扑序列](https://www.acwing.com/problem/content/850/)

![image-20240203114310300](assets/image-20240203114310300.png)

> **写法 1: AcWing 题解 (数组模拟队列)**

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>
using namespace std;

const int N = 100010;
int h[N], e[N], ne[N], idx;
int q[N]; // 数组模拟队列
int d[N]; // 存储点的入度
int n, m;

void add(int a, int b) {
    e[idx] = b, ne[idx] = h[a], h[a] = idx++;
}

bool topsort() {
    int hh = 0, tt = -1;
    // 将所有入度为0的点入队
    for (int i = 1; i <= n; i++) {
        if (d[i] == 0) q[++tt] = i;
    }

    while (hh <= tt) {
        int t = q[hh++];
        for (int i = h[t]; i != -1; i = ne[i]) {
            int j = e[i];
            d[j]--;
            // 如果此点入度-1后为0则入队
            if (d[j] == 0) q[++tt] = j;
        }
    }
    // 如果所有点都入队，说明存在拓扑序列
    return tt == n - 1;
}

int main() {
    cin >> n >> m;
    memset(h, -1, sizeof h);
    while (m--) {
        int a, b;
        cin >> a >> b;
        add(a, b);
        d[b]++;
    }

    if (topsort()) {
        for (int i = 0; i < n; i++) cout << q[i] << " ";
        cout << endl;
    } else {
        cout << -1 << endl;
    }
    return 0;
}
```

> **写法 2: 洛谷题解 (优先队列实现字典序最小)**
>
> 洛谷：https://www.luogu.com.cn/problem/U153876
>
> 题解：

```cpp
#include<iostream>
#include<cstring>
#include<queue>
#include<vector>
using namespace std;
const int N = 2e5 + 5;
int n, m;
vector<int> adj[N];
int inDegree[N];

void topSort() {
    priority_queue<int, vector<int>, greater<int>> que; // 优先队列保证字典序
    for (int i = 1; i <= n; i++) {
        if (inDegree[i] == 0) {
            que.push(i);
        }
    }

    vector<int> result;
    while (!que.empty()) {
        int cur = que.top();
        que.pop();
        result.push_back(cur);

        for (int neighbor : adj[cur]) {
            inDegree[neighbor]--;
            if (inDegree[neighbor] == 0) {
                que.push(neighbor);
            }
        }
    }

    if (result.size() == n) {
        for (int i = 0; i < n; i++) {
            printf("%d%c", result[i], i == n - 1 ? '\n' : ' ');
        }
    } else {
        printf("Error: The graph has a cycle.\n");
    }
}

int main() {
    scanf("%d %d", &n, &m);
    for (int i = 0; i < m; i++) {
        int a, b;
        scanf("%d %d", &a, &b);
        adj[a].push_back(b);
        inDegree[b]++;
    }
    topSort();
    return 0;
}
```

</details>

---

## 🗺️ 5. 最短路问题

> **核心思想**：最短路问题旨在寻找图中两点（单源）或所有点对（多源）之间的最短路径。不同算法适用于不同场景：
> -   **Dijkstra**: 适用于 **无负权边** 的图，是贪心算法的典范。
> -   **Bellman-Ford**: 适用于 **有负权边** 的图，可以检测 **负权环**，但效率较低。
> -   **SPFA**: Bellman-Ford 的队列优化版，通常比 Bellman-Ford 快，也能处理负权边和检测负环。
> -   **Floyd-Warshall**: 用于求解 **所有点对** 之间的最短路，可处理负权边，但不能处理负权环。

### 5.1 Dijkstra 算法

> **核心思想**：Dijkstra 算法通过维护一个集合 `S`，其中包含已找到最短路径的顶点。它不断地从 `S` 外部的顶点中选择一个距离源点最近的顶点 `t` 加入 `S`，然后用 `t` 来更新其邻居到源点的距离（称为“松弛”操作）。此过程重复 `n` 次。适用于**无负权边**的图。

<details>
<summary><strong>I. 朴素版 (邻接矩阵, O(n²))</strong></summary>

-   **AcWing**: [849. Dijkstra求最短路 I](https://www.acwing.com/problem/content/851/)

![image-20240203150841562](assets/image-20240203150841562.png)

> **解法思路**：
>
> 适用于稠密图（边数接近 n²）。
>
> 1.  初始化 `dist` 数组为无穷大，`dist[1] = 0`。
> 2.  进行 `n` 次迭代：
>     -   在所有未确定最短路的点中，找到 `dist` 值最小的点 `t`。
>     -   将 `t` 标记为已确定。
>     -   用 `t` 来更新所有与它相邻的点的 `dist` 值：`dist[j] = min(dist[j], dist[t] + g[t][j])`。

> [!important]
>
> 朴素`Dijkstra`算法模板：
>
> ```c++
> //朴素Dijkstra算法，时间复杂度O(n*n+m)，n表示点数，m表示边数
> 
> int g[N][N];  // 存储每条边
> int dist[N];  // 存储1号点到每个点的最短距离
> bool st[N];   // 存储每个点的最短路是否已经确定
> 
> // 求1号点到n号点的最短路，如果不存在则返回-1
> int dijkstra()
> {
>     memset(dist, 0x3f, sizeof dist);
>     dist[1] = 0;
> 
>     for (int i = 0; i < n - 1; i ++ )
>     {
>         int t = -1;     // 在还未确定最短路的点中，寻找距离最小的点
>         for (int j = 1; j <= n; j ++ )
>             if (!st[j] && (t == -1 || dist[t] > dist[j]))
>                 t = j;
> 
>         // 用t更新其他点的距离
>         for (int j = 1; j <= n; j ++ )
>             dist[j] = min(dist[j], dist[t] + g[t][j]);
> 
>         st[t] = true;
>     }
> 
>     if (dist[n] == 0x3f3f3f3f) return -1;
>     return dist[n];
> }
> ```
>
> 





> **AcWing 题解代码**

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>
using namespace std;

const int N = 510;
const int INF = 0x3f3f3f3f;

int g[N][N]; // 邻接矩阵
int d[N];    // 各个点到1号点的距离
bool st[N];  // 标记该点是否已经确定最小距离
int n, m;

int Dijkstra() {
    memset(d, 0x3f, sizeof d);
    d = 0;

    for (int i = 0; i < n; i++) {
        int t = -1;
        // 找到未确定中距离最近的点
        for (int j = 1; j <= n; j++)
            if (!st[j] && (t == -1 || d[t] > d[j]))
                t = j;
        
        if (t == -1) break;

        st[t] = true;

        // 用 t 更新其他点的距离
        for (int j = 1; j <= n; j++)
            d[j] = min(d[j], d[t] + g[t][j]);
    }

    if (d[n] == INF) return -1;
    return d[n];
}

int main() {
    cin >> n >> m;
    memset(g, 0x3f, sizeof g);

    while (m--) {
        int x, y, z;
        cin >> x >> y >> z;
        g[x][y] = min(g[x][y], z); // 防止重边，保留更小的距离
    }

    cout << Dijkstra() << endl;
    return 0;
}
```

</details>

<details>
<summary><strong>II. 堆优化版 (邻接表, O(m log n))</strong></summary>

-   **洛谷**: [P4779 【模板】单源最短路径（标准版）](https://www.luogu.com.cn/problem/P4779)
-   **AcWing**: [850. Dijkstra求最短路 II](https://www.acwing.com/problem/content/852/)

![image-20240204093023259](assets/image-20240204093023259.png)

> **解法思路**：
>
> 适用于稀疏图（边数远小于 n²）。朴素版中 `O(n)` 寻找最小 `dist` 值的步骤可以用**优先队列（最小堆）**优化到 `O(log n)`。
>
> 1.  用邻接表存图。
> 2.  使用 `priority_queue` 存储 `pair<distance, vertex>`，按 `distance` 升序排列。
> 3.  将 `{0, 1}` (距离0，节点1) 入堆。
> 4.  当堆不为空时，取出堆顶 `{dist, ver}`。
> 5.  如果 `ver` 已被访问，跳过。否则，标记 `ver` 为已访问。
> 6.  遍历 `ver` 的邻居 `j`，如果 `dist[j] > dist[ver] + w`，则更新 `dist[j]` 并将 `{dist[j], j}` 入堆。



> [!important]
>
> ==堆优化Dijkstra算法模板==
>
> ```c++
> //堆优化版Dijkstra算法，时间复杂度O(mlogn)，n表示点数，m表示边数
> 
> typedef pair<int, int> PII;
> 
> int n;      // 点的数量
> int h[N], w[N], e[N], ne[N], idx;       // 邻接表存储所有边
> int dist[N];        // 存储所有点到1号点的距离
> bool st[N];     // 存储每个点的最短距离是否已确定
> 
> // 求1号点到n号点的最短距离，如果不存在，则返回-1
> int dijkstra()
> {
>     memset(dist, 0x3f, sizeof dist);
>     dist[1] = 0;
>     priority_queue<PII, vector<PII>, greater<PII>> heap;
>     heap.push({0, 1});      // first存储距离，second存储节点编号
> 
>     while (heap.size())
>     {
>         auto t = heap.top();
>         heap.pop();
> 
>         int ver = t.second, distance = t.first;
> 
>         if (st[ver]) continue;
>         st[ver] = true;
> 
>         for (int i = h[ver]; i != -1; i = ne[i])
>         {
>             int j = e[i];
>             if (dist[j] > distance + w[i])
>             {
>                 dist[j] = distance + w[i];
>                 heap.push({dist[j], j});
>             }
>         }
>     }
> 
>     if (dist[n] == 0x3f3f3f3f) return -1;
>     return dist[n];
> }
> 
> ```



> **写法 1: AcWing 题解**

```cpp
#include <cstring>
#include <iostream>
#include <queue>
#include <vector>
using namespace std;

typedef pair<int, int> PII;
const int N = 150010;

int n, m;
int h[N], w[N], e[N], ne[N], idx;
int d[N];
bool st[N];

void add(int a, int b, int c)
{
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx ++ ;
}

int dijkstra()
{
    memset(d, 0x3f, sizeof d);
    d = 0;
    priority_queue<PII, vector<PII>, greater<PII>> heap;
    heap.push({0, 1}); // first是距离，second是点号

    while (heap.size())
    {
        PII t = heap.top();
        heap.pop();

        int ver = t.second;
        int distance = t.first;

        if (st[ver]) continue;
        st[ver] = true;
        
        for (int i = h[ver]; i != -1; i = ne[i])
        {
            int j = e[i];
            if (d[j] > distance + w[i])
            {
                d[j] = distance + w[i];
                heap.push({d[j], j});
            }
        }
    }

    if (d[n] == 0x3f3f3f3f) return -1;
    return d[n];
}

int main()
{
    cin >> n >> m;
    memset(h, -1, sizeof h);
    while (m--)
    {
        int a, b, c;
        cin >> a >> b >> c;
        add(a, b, c);
    }
    cout << dijkstra() << endl;
    return 0;
}
```

> **写法 2: 洛谷 P3371 题解**

```cpp
#include<iostream>
#include<cstring>
#include<queue>
using namespace std;
const int N = 1e4 + 5;
const int M = 5e5 + 10;
const int INF = 0x3f3f3f3f;
int h[N], e[M], ne[M], w[M], idx; // idx表示边的编号
bool visited[N];
int minDist[N];
int n, m, s;
void init(){
    memset(h, -1, sizeof(h));
    memset(w, -1, sizeof(w));
    memset(visited, false, sizeof(visited));
    memset(minDist, 0x3f, sizeof(minDist));
    idx = 0;
}
// a --> b, 权重为c 
void add(int a, int b, int c){
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx++;
}
struct cmp{
    // <节点, 源点到该节点的距离>
    bool operator() (const pair<int, int>&a, const pair<int, int>&b) const {
        return a.second > b.second;
    }
};
void Dijkstra_Heap(int srt){
    minDist[srt] = 0;
    // 小顶堆
    priority_queue<pair<int, int>, vector<pair<int, int>>, cmp>pq;
    pq.emplace(srt, 0);
    while(!pq.empty()){
        auto cur = pq.top();
        pq.pop();
        int to = cur.first, val = cur.second;
        if(visited[to]){
            continue;
        }
        visited[to] = true;
        for(int i = h[to];i!=-1;i=ne[i]){
            int j = e[i];
            if(!visited[j] && minDist[j] > minDist[to] + w[i]){
                minDist[j] = minDist[to] + w[i];
                pq.emplace(j, minDist[j]);
            }
        }
    }
    for(int i = 1; i <= n;i++){
        if(minDist[i] == INF){
            printf("%d ", (1 << 31) - 1);
        }else{
            printf("%d ", minDist[i]);
        }
    }
}
int main(){
    init();
    scanf("%d %d %d", &n, &m, &s);
    int a, b, c;
    for(int i = 0;i < m;i++){
        scanf("%d %d %d", &a, &b, &c);
        add(a, b, c);
    }
    Dijkstra_Heap(s);
    return 0;
}
```

</details>

### 5.2 Bellman-Ford 算法

> **核心思想**：Bellman-Ford 算法基于动态规划。它对图中的所有边进行 `n-1` 轮松弛操作。在第 `k` 轮松弛后，`dist[i]` 存储的是从源点出发、经过最多 `k` 条边到达 `i` 的最短路径长度。该算法可以处理**负权边**。如果在第 `n` 轮仍然可以松弛，说明图中存在**负权环**。



> [!note]
>
> Bellman_Ford算法模板
>
> ```c++
> //bellman_ford，时间复杂度O(nm)，n表示点数，m表示边数
> int n, m;       // n表示点数，m表示边数
> int dist[N];        // dist[x]存储1到x的最短路距离
> 
> struct Edge     // 边，a表示出点，b表示入点，w表示边的权重
> {
>     int a, b, w;
> }edges[M];
> 
> // 求1到n的最短路距离，如果无法从1走到n，则返回-1。
> int bellman_ford()
> {
>     memset(dist, 0x3f, sizeof dist);
>     dist[1] = 0;
> 
>     // 如果第n次迭代仍然会松弛三角不等式，就说明存在一条长度是n+1的最短路径，由抽屉原理，路径中至少存在两个相同的点，说明图中存在负权回路。
>     for (int i = 0; i < n; i ++ )
>     {
>         for (int j = 0; j < m; j ++ )
>         {
>             int a = edges[j].a, b = edges[j].b, w = edges[j].w;
>             if (dist[b] > dist[a] + w)
>                 dist[b] = dist[a] + w;
>         }
>     }
> 
>     if (dist[n] > 0x3f3f3f3f / 2) return -1;
>     return dist[n];
> }
> 
> ```
>
> 





<details>
<summary><strong>🔗 练习平台与题解</strong></summary>

-   **AcWing**: [853. 有边数限制的最短路](https://www.acwing.com/problem/content/855/)

![image-20240204105709396](assets/image-20240204105709396.png)
![4.PNG](assets/652_1aa3df28a4-4.PNG)
![2.PNG](assets/652_8fb5d1dca4-2.PNG)

> **解法思路**：
>
> 题目明确要求“最多经过 k 条边”，这正是 Bellman-Ford 算法的特长。
>
> 1.  用结构体数组存储所有边。
> 2.  进行 `k` 轮迭代。在每一轮中，遍历所有 `m` 条边 `(a, b, w)`，尝试用 `dist[a] + w` 来更新 `dist[b]`。
> 3.  **串联问题**：为防止在一轮迭代中，用本轮更新过的值去更新其他值（即一条路径上更新了多次），需要一个 `backup` 数组 `last[]` 来存储上一轮的 `dist` 值。`dist[b] = min(dist[b], last[a] + w)`。
> 4.  **不可达判断**：由于存在负权边，`dist` 可能被一个非常大的正数加上一个负数更新。因此，判断不可达不能用 `dist[n] == INF`，而应该用 `dist[n] > INF / 2`，这是一个安全的界限。

> **AcWing 题解代码**

```cpp
#include <cstring>
#include <iostream>
#include <algorithm>
using namespace std;

const int N = 510, M = 10010;
const int INF = 0x3f3f3f3f;

struct Edge {
    int a, b, w;
} edges[M];

int d[N];
int last[N]; // 备份数组
int n, m, k;

void bellman_ford() {
    memset(d, 0x3f, sizeof d);
    d = 0;

    // 迭代 k 次
    for (int i = 0; i < k; i++) {
        memcpy(last, d, sizeof d); // 备份上一轮的距离
        // 遍历所有边进行松弛
        for (int j = 0; j < m; j++) {
            auto e = edges[j];
            if (last[e.a] != INF) { // 只有当起点可达时才松弛
                d[e.b] = min(d[e.b], last[e.a] + e.w);
            }
        }
    }
}

int main() {
    cin >> n >> m >> k;
    for (int i = 0; i < m; i++) {
        int a, b, w;
        cin >> a >> b >> w;
        edges[i] = {a, b, w};
    }

    bellman_ford();

    if (d[n] > INF / 2) puts("impossible");
    else cout << d[n] << endl;

    return 0;
}
```

</details>

### 5.3 SPFA 算法

> **核心思想**：SPFA (Shortest Path Faster Algorithm) 是 Bellman-Ford 的队列优化版本。Bellman-Ford 每轮迭代会遍历所有边，但很多边的松弛操作是无效的。SPFA 的优化在于，只有当一个点 `u` 的 `dist` 值变小时，才有可能去更新它的邻居。因此，SPFA 维护一个队列，只将被成功松弛的节点入队。



> [!tip]
>
> ==SPFA算法模板==
>
> ```c++
> //时间复杂度平均情况下O(m)，最坏O(nm)，n表示点数，m表示边数
> 
> int n;      // 总点数
> int h[N], w[N], e[N], ne[N], idx;       // 邻接表存储所有边
> int dist[N];        // 存储每个点到1号点的最短距离
> bool st[N];     // 存储每个点是否在队列中
> 
> // 求1号点到n号点的最短路距离，如果从1号点无法走到n号点则返回-1
> int spfa()
> {
>     memset(dist, 0x3f, sizeof dist);
>     dist[1] = 0;
> 
>     queue<int> q;
>     q.push(1);
>     st[1] = true;
> 
>     while (q.size())
>     {
>         auto t = q.front();
>         q.pop();
> 
>         st[t] = false;
> 
>         for (int i = h[t]; i != -1; i = ne[i])
>         {
>             int j = e[i];
>             if (dist[j] > dist[t] + w[i])
>             {
>                 dist[j] = dist[t] + w[i];
>                 if (!st[j])     // 如果队列中已存在j，则不需要将j重复插入
>                 {
>                     q.push(j);
>                     st[j] = true;
>                 }
>             }
>         }
>     }
> 
>     if (dist[n] == 0x3f3f3f3f) return -1;
>     return dist[n];
> }
> ```
>
> 



<details>
<summary><strong>I. SPFA 求最短路</strong></summary>

-   **AcWing**: [851. spfa求最短路](https://www.acwing.com/problem/content/853/)
-   **洛谷**：https://www.luogu.com.cn/problem/U520024

![image-20240204145128620](assets/image-20240204145128620.png)

> **解法思路**：
>
> 1.  用队列 `q` 和布尔数组 `st`（标记节点是否在队列中）。
> 2.  源点 `1` 入队，`dist[1]=0`, `st[1]=true`。
> 3.  当队列不为空时，出队一个点 `t`，`st[t]=false`。
> 4.  遍历 `t` 的邻居 `j`，如果 `dist[j] > dist[t] + w`，则更新 `dist[j]`。
> 5.  如果 `j` 不在队列中，则将 `j` 入队，`st[j]=true`。

> **AcWing 题解代码**

```cpp
#include <cstring>
#include <iostream>
#include <algorithm>
#include <queue>
using namespace std;

const int N = 100010;
const int INF = 0x3f3f3f3f;

int n, m;
int h[N], w[N], e[N], ne[N], idx;
int d[N];
bool st[N]; // 标记某点是否在队列中

void add(int a, int b, int c) {
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx++;
}

int spfa() {
    memset(d, 0x3f, sizeof d);
    d = 0;

    queue<int> q;
    q.push(1);
    st = true;

    while (q.size()) {
        int t = q.front();
        q.pop();
        st[t] = false; // 出队后标记为不在队列中

        for (int i = h[t]; i != -1; i = ne[i]) {
            int j = e[i];
            if (d[j] > d[t] + w[i]) {
                d[j] = d[t] + w[i];
                if (!st[j]) { // 如果 j 不在队列中，则入队
                    q.push(j);
                    st[j] = true;
                }
            }
        }
    }

    if (d[n] == INF) return INF;
    return d[n];
}

int main() {
    cin >> n >> m;
    memset(h, -1, sizeof h);
    while (m--) {
        int a, b, c;
        cin >> a >> b >> c;
        add(a, b, c);
    }
    
    int res = spfa();
    if (res == INF) puts("impossible");
    else cout << res << endl;
    
    return 0;
}
```

</details>

<details>
<summary><strong>II. SPFA 判断负环</strong></summary>

-   **洛谷**: [P3385 【模板】负环](https://www.luogu.com.cn/problem/P3385)
-   **AcWing**: [852. spfa判断负环](https://www.acwing.com/problem/content/854/)

![image-20240204152907404](assets/image-20240204152907404.png)

> **解法思路**：
>
> 如果图中存在负环，那么在环上的点可以无限松弛，导致 SPFA 算法死循环。我们可以利用这一点来判断负环。
>
> 1.  **边数计数**：设置一个 `cnt` 数组，`cnt[x]` 记录从源点到 `x` 的最短路径所包含的边数。
> 2.  **抽屉原理**：在松弛 `dist[j] = dist[t] + w[i]` 时，同时更新 `cnt[j] = cnt[t] + 1`。
> 3.  **负环判断**：如果 `cnt[j] >= n`，说明从源点到 `j` 的最短路径经过了至少 `n` 条边，这意味着路径上至少有 `n+1` 个点。根据抽屉原理，其中必有重复的点，即形成了环。由于是在松弛过程中发现的，这个环一定是负权环。
> 4.  **初始状态**：为了能检测到所有可能不与 1 号点连通的负环，初始时需要将所有节点都加入队列。

> **写法 1: AcWing 题解**

```cpp
#include <cstring>
#include <iostream>
#include <queue>
using namespace std;

const int N = 2010, M = 10010;

int n, m;
int h[N], e[M], ne[M], w[M], idx;
bool st[N];
int d[N];
int cnt[N]; // cnt[x] 表示当前从源点到x的最短路的边数

void add(int a, int b, int c) {
    e[idx] = b, ne[idx] = h[a], w[idx] = c, h[a] = idx++;
}

bool spfa() {
    queue<int> q;
    // 把所有点都入队，以检测不和1号点连通的负环
    for (int i = 1; i <= n; i++) {
        q.push(i);
        st[i] = true;
    }

    while (q.size()) {
        int t = q.front();
        q.pop();
        st[t] = false;

        for (int i = h[t]; i != -1; i = ne[i]) {
            int j = e[i];
            if (d[j] > d[t] + w[i]) {
                d[j] = d[t] + w[i];
                cnt[j] = cnt[t] + 1;
                if (cnt[j] >= n) return true; // 发现负环
                if (!st[j]) {
                    q.push(j);
                    st[j] = true;
                }
            }
        }
    }
    return false;
}

int main() {
    cin >> n >> m;
    memset(h, -1, sizeof h);
    while (m--) {
        int a, b, c;
        cin >> a >> b >> c;
        add(a, b, c);
    }
    if (spfa()) puts("Yes");
    else puts("No");
    return 0;
}
```

> **写法 2: 洛谷 P3385 题解**

```cpp
#include<iostream>
#include<cstring>
#include<queue>
#include<vector>
using namespace std;

const int N = 2e3 + 5;
vector<pair<int, int>> grid[N];
int cnt[N];
int d[N];
int n, m;
bool inQue[N];

void init() {
    for (int i = 1; i <= n; i++) {
        grid[i].clear();
    }
    memset(d, 0x3f, sizeof(d));
    fill(inQue, inQue + n + 1, false);
    memset(cnt, 0, sizeof(cnt));
}

void add(int a, int b, int c) {
    grid[a].emplace_back(b, c);
}

bool spfa() {
    queue<int> que;
    que.push(1);
    inQue = true;
    d = 0;

    while (!que.empty()) {
        int cur = que.front();
        que.pop();
        inQue[cur] = false;

        for (auto& [neighbor, val] : grid[cur]) {
            if (d[neighbor] > d[cur] + val) {
                d[neighbor] = d[cur] + val;
                cnt[neighbor] = cnt[cur] + 1;
                if (cnt[neighbor] >= n) {
                    return true;
                }
                if (!inQue[neighbor]) {
                    que.push(neighbor);
                    inQue[neighbor] = true;
                }
            }
        }
    }
    return false;
}

int main() {
    int t;
    scanf("%d", &t);
    while (t--) {
        scanf("%d %d", &n, &m);
        init();
        for (int i = 0; i < m; i++) {
            int a, b, c;
            scanf("%d %d %d", &a, &b, &c);
            add(a, b, c);
            if (c >= 0) {
                add(b, a, c);
            }
        }
        if (spfa()) {
            printf("YES\n");
        } else {
            printf("NO\n");
        }
    }
    return 0;
}
```

</details>

### 5.4 Floyd 算法

> **核心思想**：Floyd 算法是一种用于求解**所有点对之间**最短路径的动态规划算法。其状态转移方程为 `d[i][j] = min(d[i][j], d[i][k] + d[k][j])`。这个方程的含义是：从 `i`到 `j` 的最短路径，要么是当前已知的路径，要么是经过中转点 `k` 的路径 (`i -> k -> j`)。通过枚举所有可能的中转点 `k`，最终可以得到所有点对间的最短路。

> [!tip]
>
> ==Floyd算法模板==
>
> ```c++
> 
> //时间复杂度O(n*n*n)
> //初始化
>     for (int i = 1; i <= n; i ++ )
>         for (int j = 1; j <= n; j ++ )
>             if (i == j) d[i][j] = 0;
>             else d[i][j] = INF;
> 
> // 算法结束后，d[a][b]表示a到b的最短距离
> void floyd()
> {
>     for (int k = 1; k <= n; k ++ )
>         for (int i = 1; i <= n; i ++ )
>             for (int j = 1; j <= n; j ++ )
>                 d[i][j] = min(d[i][j], d[i][k] + d[k][j]);
> }
> ```
>
> 





<details>
<summary><strong>🔗 练习平台与题解</strong></summary>

-   **AcWing**: [854. Floyd求最短路](https://www.acwing.com/problem/content/856/)
-   **洛谷**：https://www.luogu.com.cn/problem/B3647

![image-20240205145217224](assets/image-20240205145217224.png)

> **解法思路**：
>
> 1.  **初始化**：用邻接矩阵 `d[N][N]` 存图。`d[i][i] = 0`，`d[i][j]` (i≠j) 为边权重或无穷大。
> 2.  **三重循环**：**最外层必须是中转点 `k`**，内两层是起点 `i` 和终点 `j`。
>     `for k = 1 to n`
>     `  for i = 1 to n`
>     `    for j = 1 to n`
>     `      d[i][j] = min(d[i][j], d[i][k] + d[k][j]);`
> 3.  **结果**：算法结束后，`d[a][b]` 即为 `a` 到 `b` 的最短距离。

> **AcWing 题解代码**

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

const int N = 210, INF = 1e9;
int n, m, q;
int d[N][N];

void floyd() {
    for (int k = 1; k <= n; k++) {
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                d[i][j] = min(d[i][j], d[i][k] + d[k][j]);
            }
        }
    }
}

int main() {
    cin >> n >> m >> q;
    // 初始化邻接矩阵
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            if (i == j) d[i][j] = 0;
            else d[i][j] = INF;
        }
    }
    
    while (m--) {
        int a, b, c;
        cin >> a >> b >> c;
        d[a][b] = min(d[a][b], c);
    }
    
    floyd();
    
    while (q--) {
        int a, b;
        cin >> a >> b;
        // 由于有负权边存在，可能d[a][b]被更新成一个很小的负数，所以判断不可达要用 > INF/2
        if (d[a][b] > INF / 2) puts("impossible");
        else cout << d[a][b] << endl;
    }
    
    return 0;
}
```

</details>



### 5.5 算法比较



> Dijkstra 朴素实现、Dijkstra 堆优化实现、Bellman-Ford 原始实现、SPFA (Bellman-Ford 队列优化)对比

| 特性/算法                |                Dijkstra 朴素实现                 | Dijkstra 堆优化实现                      | Bellman-Ford 原始实现                      | SPFA (Bellman-Ford 队列优化)                                 |
| ------------------------ | :----------------------------------------------: | ---------------------------------------- | ------------------------------------------ | ------------------------------------------------------------ |
| **主要用途**             |              单源最短路径，非负权重              | 单源最短路径，非负权重                   | 单源最短路径，可处理负权重（检测负环）     | 单源最短路径，期望处理稀疏图时比Bellman-Ford快               |
| **主要数据结构**         |                       数组                       | 优先队列（如二叉堆）                     | 数组                                       | 队列                                                         |
| **存储图的方式**         |                     邻接矩阵                     | 邻接表                                   | 使用数组直接存储每一条边的起点、终点、权重 | 邻接表                                                       |
| **时间复杂度**           |              $O(V^2)$，$V$是顶点数               | $O(Elog E)$，$E$是边数                   | $O(VE)$                                    | 平均情况下$O(kE)$，k为每个节点入队次数，但在最坏情况下仍为$O(VE)$ |
| **空间复杂度**           |                     $O(V^2)$                     | $O(V + E)$，额外需要优先队列             | $O(E)$                                     | $O(V + E)$，额外需要队列                                     |
| **适用图类型**           |                    无负权重边                    | 无负权重边                               | 可以有负权重边，但不能有负权重环           | 理论上可以有负权重边，但更适合稀疏图                         |
| **能否处理负权重**       |                      不可以                      | 不可以                                   | 可以，且能检测负权重环                     | 实践中不推荐用于负权重边                                     |
| **选择下一个节点的方式** | 遍历所有未确定最短路径的节点，寻找距离最小的节点 | 使用优先队列获取当前距离最小的节点       | 松弛操作，遍历所有边V-1次                  | 松弛操作，仅对受影响的节点进行松弛                           |
| **优势**                 |    - 易于理解和实现<br>- 对小规模或稠密图有效    | - 对大规模或稀疏图更高效                 | - 可处理负权重边<br>- 能检测负权重环       | - 在稀疏图上通常比标准Bellman-Ford更快                       |
| **劣势**                 |                  对稀疏图效率低                  | 实现较为复杂                             | 效率较低，尤其是对于稀疏图                 | 最坏情况下的性能与Bellman-Ford相同                           |
| **应用场景**             |   小规模或非常稠密的图（边数接近顶点数的平方）   | 大规模或稀疏图（边数远小于顶点数的平方） | 包含负权重边的图，特别是需要检测负环的情况 | 实际应用中常用于稀疏图，尽管理论上不稳定                     |





> BFS (广度优先搜索)、Dijkstra (迪杰斯特拉算法)、Bellman-Ford (贝尔曼-福特算法)、Floyd-Warshall (弗洛伊德-沃舍尔算法) 算法对比

| 特性/算法          | BFS (广度优先搜索)                   | Dijkstra (迪杰斯特拉算法)            | Bellman-Ford (贝尔曼-福特算法)                               | Floyd-Warshall (弗洛伊德-沃舍尔算法)                       |
| ------------------ | ------------------------------------ | ------------------------------------ | ------------------------------------------------------------ | ---------------------------------------------------------- |
| **主要用途**       | 寻找无权图中的最短路径               | 寻找带非负权重图中的单源最短路径     | 寻找可能包含负权重边的单源最短路径                           | 寻找所有节点对之间的最短路径                               |
| **处理的图类型**   | 无权图或边权重相同的图               | 带非负权重的有向图或无向图           | 可以处理负权重边（但不能有负权重环）的有向图                 | 带权重的有向图或无向图（可以包含负权重边但不能有负权重环） |
| **实现方式**       | 队列（Queue）                        | 优先队列（Priority Queue）           | 松弛操作（Relaxation），V-1次迭代                            | 动态规划                                                   |
| **时间复杂度**     | O(V + E)，V为顶点数，E为边数         | $O(V + E)$，额外需要优先队列         | 平均情况下$O(kE)$，k为每个节点入队次数，但在最坏情况下仍为$O(VE)$ | $O(V^3)$，$V$为顶点数                                      |
| **空间复杂度**     | $O(V)$，主要用于存储访问标记和队列   | $O(V)$，用于存储距离和前驱信息       | $O(V)$，用于存储距离和前驱信息                               | $O(V^2)$，用于存储所有节点间的最短路径矩阵                 |
| **能否处理负权重** | 不适用（因为是无权图）               | 不能处理负权重的边                   | 能够处理负权重边，但不能有负权重环                           | 能够处理负权重的边，但不能有负权重环                       |
| **应用场景**       | 最短路径问题（限于无权图），层次遍历 | 单源最短路径问题                     | 单源最短路径问题，检测负权重环                               | 所有点对间最短路径问题，检测负权重环                       |
| **优缺点**         | 简单高效，适用于无权图               | 对有权图效率高，但不支持负权重       | 可以处理负权重边，适用于稠密图；能检测负环                   | 实现简单，适合稠密图；但效率较低，不适合大规模稀疏图       |
| **特殊考虑**       | -                                    | 对于稀疏图，使用优先队列可以提高效率 | 通过额外的一轮松弛操作来检查是否存在负权重环                 | 可以用来检查图中是否存在负权重环                           |





---

## 🌳 6. 最小生成树 (MST)

> **核心思想**：对于一个连通的无向加权图，最小生成树（MST）是包含图中所有顶点的一棵树，且其所有边的权重之和最小。构造 MST 的常用算法都是基于贪心策略：
>
> -   **Prim**: 从一个点开始，不断将离当前生成树最近的顶点和边加入，直到所有点都加入。
> -   **Kruskal**: 将所有边按权重从小到大排序，依次加入边，只要不形成环就保留，直到有 `n-1` 条边。

### 6.1 Prim 算法

> **核心思想**：Prim 算法类似于 Dijkstra。它维护一个顶点集合 `S`，初始时只有一个顶点。算法每一步都选择一条连接 `S` 中顶点与 `S` 外顶点的权重最小的边，并将该边和对应的 `S` 外顶点加入 `S`。这个过程重复 `n-1` 次。



> [!important]
>
> ==朴素版Prim算法模板==
>
> ```c++
> //朴素版prim算法，时间复杂度是O(n*n+m)，n表示点数，m表示边数
> 
> int n;      // n表示点数
> int g[N][N];        // 邻接矩阵，存储所有边
> int dist[N];        // 存储其他点到当前最小生成树的距离
> bool st[N];     // 存储每个点是否已经在生成树中
> 
> 
> // 如果图不连通，则返回INF(值是0x3f3f3f3f), 否则返回最小生成树的树边权重之和
> int prim()
> {
>     memset(dist, 0x3f, sizeof dist);
> 
>     int res = 0;
>     for (int i = 0; i < n; i ++ )
>     {
>         int t = -1;
>         for (int j = 1; j <= n; j ++ )
>             if (!st[j] && (t == -1 || dist[t] > dist[j]))
>                 t = j;
> 
>         if (i && dist[t] == INF) return INF;
> 
>         if (i) res += dist[t];
>         st[t] = true;
> 
>         for (int j = 1; j <= n; j ++ ) dist[j] = min(dist[j], g[t][j]);
>     }
> 
>     return res;
> }
> 
> ```
>
> 





<details>
<summary><strong>🔗 练习平台与题解 (朴素版, O(n²))</strong></summary>

-   **AcWing**: [858. Prim算法求最小生成树](https://www.acwing.com/problem/content/860/)
-   **洛谷**：https://www.luogu.com.cn/problem/U562562

![image-20240205160356226](assets/image-20240205160356226.png)

> **解法思路**：
>
> 适用于稠密图。
>
> 1.  初始化 `dist` 数组为无穷大，`dist[i]` 表示点 `i` 到当前生成树集合的最短距离。
> 2.  进行 `n` 次迭代：
>     -   在所有未加入集合的点中，找到 `dist` 值最小的点 `t`。
>     -   如果找到的 `dist[t]` 为无穷大，说明图不连通。
>     -   将 `dist[t]` 加入总权重，并将 `t` 加入集合。
>     -   用 `t` 来更新其他点到集合的距离：`dist[j] = min(dist[j], g[t][j])`。

> **AcWing 题解代码**

```cpp
#include <cstring>
#include <iostream>
#include <algorithm>
using namespace std;

const int N = 510, INF = 0x3f3f3f3f;

int n, m;
int g[N][N];
int d[N];    // 某点离集合的距离
bool st[N];  // 标记是否已加入集合

int prim()
{
    memset(d, 0x3f, sizeof d);
    
    int res = 0; // 最小生成树的边权之和
    for (int i = 0; i < n; i ++ )
    {
        int t = -1;
        // 找到目前离集合最近的点
        for (int j = 1; j <= n; j ++ )
            if (!st[j] && (t == -1 || d[t] > d[j]))
                t = j;
        
        // 如果不是第一个点且距离为INF，说明图不连通
        if (i && d[t] == INF) return INF;
        
        if (i) res += d[t]; // 将边权加入总和
        st[t] = true;       // 将点t加入集合
		
        // 用t更新其他点到集合的距离
        for (int j = 1; j <= n; j ++ ) d[j] = min(d[j], g[t][j]);
    }

    return res;
}


int main()
{
    cin >> n >> m;
    memset(g, 0x3f, sizeof g);

    while (m -- )
    {
        int a, b, c;
        cin >> a >> b >> c; 
        g[a][b] = g[b][a] = min(g[a][b], c); // 无向图
    }

    int t = prim();

    if (t == INF) puts("impossible");
    else cout << t << endl;

    return 0;
}
```

</details>

### 6.2 Kruskal 算法

> **核心思想**：Kruskal 算法是一种以边为中心的贪心算法。它将所有边按权重从小到大排序，然后依次考察每条边。如果一条边连接的两个顶点尚不属于同一个连通分量（用**并查集**判断），则将这条边加入最小生成树，并合并这两个连通分量。



> [!note]
>
> ==Kruskal算法模板==
>
> ```c++
> //时间复杂度是O(mlogm)，m表示边数
> 
> int n, m;
> int p[N];
> int res;//集合中的总边权 
> int cnt;//集合中的边数 
> 
> struct Edge
> {
>     int a, b, w;
>   
>     bool operator < (const Edge &e) const
>     {
>         return w < e.w;
>     }
> }edges[M];
>  
> int find(int x)
> {
>     if (p[x] != x) p[x] = find(p[x]);
>     return p[x];
> }
> 
> void kruskal()
> {
> 	//按升序给每条边排序 
>     sort(edges, edges + m);
> 	
> 	// 初始化并查集
>     for (int i = 1; i <= n; i ++ ) p[i] = i;    
>     
>     //遍历所有边
>     for (int i = 0; i < m; i ++ )
>     {
>         int a = edges[i].a, b = edges[i].b, w = edges[i].w;
> 		 
>         int pa = find(a), pb = find(b);
>         if (pa != pb)
>         {
>             p[pa] = pb;
>             res += w;
>             cnt ++ ;
>         }
>     }
> 
> }
> ```
>
> 



<details>
<summary><strong>🔗 练习平台与题解 (O(m log m))</strong></summary>

-   **洛谷**: [P3366 【模板】最小生成树](https://www.luogu.com.cn/problem/P3366)
-   **AcWing**: [859. Kruskal算法求最小生成树](https://www.acwing.com/problem/content/861/)

![image-20240206173236341](assets/image-20240206173236341.png)

> **解法思路**：
>
> 适用于稀疏图。
>
> 1.  用结构体存储所有边，并按权重升序排序。
> 2.  初始化并查集，每个点自成一个集合。
> 3.  遍历排序后的边 `(a, b, w)`：
>     -   用 `find` 函数查找 `a` 和 `b` 的根节点。
>     -   如果根节点不同，说明 `a` 和 `b` 不在同一连通块，加入这条边不会形成环。
>     -   将该边权重 `w` 加入总和，合并 `a` 和 `b` 所在的集合，已加入的边数 `cnt` 加一。
> 4.  当 `cnt` 达到 `n-1` 时，MST 构建完成。如果遍历完所有边后 `cnt < n-1`，则图不连通。

> **AcWing 题解代码**

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

const int N = 100010, M = 200010;

int n, m;
int p[N]; // 并查集

struct Edge {
    int a, b, w;
    // 重载 "<" 运算符，用于 sort 排序
    bool operator< (const Edge &other) const {
        return w < other.w;
    }
} edges[M];

// 查找根节点 + 路径压缩
int find(int x) {
    if (p[x] != x) p[x] = find(p[x]);
    return p[x];
}

int main() {
    cin >> n >> m;
    for (int i = 0; i < m; i++) {
        cin >> edges[i].a >> edges[i].b >> edges[i].w;
    }

    sort(edges, edges + m); // 按边权排序

    for (int i = 1; i <= n; i++) p[i] = i; // 初始化并查集

    int res = 0, cnt = 0; // res: 总权重, cnt: 已加入的边数
    for (int i = 0; i < m; i++) {
        int a = edges[i].a, b = edges[i].b, w = edges[i].w;
        int pa = find(a), pb = find(b);
        if (pa != pb) { // 如果不在同一个集合
            p[pa] = pb; // 合并
            res += w;
            cnt++;
        }
    }

    if (cnt < n - 1) puts("impossible"); // 边数不够，图不连通
    else cout << res << endl;

    return 0;
}
```



</details>



### 6.3  算法比较

| 最小生成树算法 | Prim算法                        | Kruskal算法                           |
| -------------- | ------------------------------- | ------------------------------------- |
| 时间复杂度     | $O(|V|^2)$，与边的数量$|E|$无关 | $O(|E|log_2|E|)$，与点的数量$|V|$无关 |
| 空间复杂度     | $O(|V|^2)$（邻接矩阵存储）      | $O(|E| + |V|)$（存储边和并查集）      |
| 使用场景       | 适合**边稠密**的图              | 适用于**边稀疏而顶点较多图**          |

>  **prim 算法是维护节点的集合，而 Kruskal 是维护边的集合**。



---

## 🎨 7. 二分图

> **核心思想**：二分图是一种特殊的图，其所有顶点可以被分为两个互不相交的集合 `U` 和 `V`，使得图中每条边的两个端点都分别属于这两个集合。一个重要的判定性质是：**一个图是二分图，当且仅当它不包含奇数长度的环**。

### 7.1 染色法判定二分图

> **核心思想**：我们可以使用图遍历（DFS 或 BFS）来尝试对图进行二染色。从任一未染色的顶点开始，将其染成颜色 1。然后遍历其所有邻居，将它们染成颜色 2。再从这些邻居出发，将其邻居染成颜色 1，以此类推。如果在染色过程中，发现一个顶点的邻居已经被染成了和它自己相同的颜色，那么就说明存在冲突（即存在奇数环），该图不是二分图。

<details>
<summary><strong>🔗 练习平台与题解 (O(n+m))</strong></summary>

- **AcWing**: [860. 染色法判定二分图](https://www.acwing.com/problem/content/862/)

- **洛谷**：

  https://www.luogu.com.cn/problem/U169194

  https://www.luogu.com.cn/problem/U248878

![image-20240207145306447](assets/image-20240207145306447.png)

> **AcWing 题解代码**

```cpp
#include <cstring>
#include <iostream>
#include <algorithm>
using namespace std;

const int N = 100010, M = 200010;

int n, m;
int h[N], e[M], ne[M], idx;
int color[N]; // 0:未染色, 1:颜色1, 2:颜色2

void add(int a, int b) {
    e[idx] = b, ne[idx] = h[a], h[a] = idx++;
}

// 给 u 点及其连通子块染色，c 是要染的颜色
bool dfs(int u, int c) {
    color[u] = c;

    for (int i = h[u]; i != -1; i = ne[i]) {
        int j = e[i];
        if (!color[j]) { // 如果邻居未染色
            if (!dfs(j, 3 - c)) return false; // 染成相反颜色
        }
        else if (color[j] == c) { // 如果邻居已染色且和自己颜色相同
            return false;
        }
    }

    return true;
}

int main() {
    cin >> n >> m;
    memset(h, -1, sizeof h);
    while (m--) {
        int a, b;
        cin >> a >> b;
        add(a, b), add(b, a);
    }

    bool flag = true;
    // 遍历所有点，防止图不连通
    for (int i = 1; i <= n; i++) {
        if (!color[i]) {
            if (!dfs(i, 1)) {
                flag = false;
                break;
            }
        }
    }

    if (flag) puts("Yes");
    else puts("No");

    return 0;
}
```

</details>

### 7.2 匈牙利算法 (最大匹配)

> **核心思想**：匈牙利算法用于解决二分图的最大匹配问题。它是一种增广路算法。核心思想是：遍历左部集合的每个点 `u`，尝试为它在右部集合中寻找一个匹配点 `v`。
> -   如果 `v` 尚未匹配，则直接将 `(u, v)` 组成一对。
> -   如果 `v` 已被另一个左部点 `u'` 匹配，则尝试为 `u'` 寻找一个新的匹配点（递归调用 `find(u')`）。如果成功为 `u'` 找到了“下家”，那么 `v` 就可以让给 `u` 来匹配。
> 这条“让来让去”的路径就是增广路。每找到一条增广路，最大匹配数就加一。

<details>
<summary><strong>🔗 练习平台与题解 (O(nm))</strong></summary>

-   **洛谷**: [P3386 【模板】二分图最大匹配](https://www.luogu.com.cn/problem/P3386)
-   **AcWing**: [861. 二分图的最大匹配](https://www.acwing.com/problem/content/863/)

![image-20240207164717001](assets/image-20240207164717001.png)

> **写法 1: AcWing 题解 (邻接矩阵)**

```cpp
#include <cstring>
#include <iostream>
#include <algorithm>
using namespace std;

const int N = 510, M = 100010;

int n1, n2, m;
int g[N][N];   // 稠密图采用邻接矩阵
int match[N];  // match[j]表示右部点j目前匹配的左部点
bool st[N];    // st[j]表示在一轮find中，右部点j是否已经访问过

// 尝试为左部点 x 寻找匹配
bool find(int x) {
    // 遍历 x 连接的所有右部点 j
    for (int j = 1; j <= n2; j++) {
        if (g[x][j] && !st[j]) { // 如果有边且 j 未被访问
            st[j] = true;
            // 如果 j 没有匹配，或者 j 的原配可以找到下家
            if (match[j] == 0 || find(match[j])) {
                match[j] = x;
                return true;
            }
        }
    }
    return false;
}

int main() {
    cin >> n1 >> n2 >> m;
    while (m--) {
        int a, b;
        cin >> a >> b;
        g[a][b] = 1;
    }

    int res = 0;
    // 遍历所有左部点
    for (int i = 1; i <= n1; i++) {
        memset(st, false, sizeof st); // 新一轮匹配，重置st
        if (find(i)) res++;
    }

    cout << res << endl;
    return 0;
}
```

> **写法 2: 邻接表写法 (适用于稀疏图)**

```cpp
#include <iostream>
#include <cstring>
using namespace std;
const int N = 505;
const int M = 5e4 + 5;
int h[N], e[M], ne[M], idx;
int match[N];
bool st[N]; // 表示当前节点是否已经匹配
int n, m, k;
void init()
{
    memset(h, -1, sizeof(h));
    idx = 0;
}
void add(int u, int v)
{
    e[idx] = v, ne[idx] = h[u], h[u] = idx++;
}
bool find(int x)
{
    for (int i = h[x]; i != -1; i = ne[i])
    {
        int j = e[i];
        if (!st[j])
        {
            st[j] = true;
            if (match[j] == 0 || find(match[j]))
            {
                match[j] = x;
                return true;
            }
        }
    }
    return false;
}
int main()
{
    init();
    scanf("%d %d %d", &n, &m, &k);
    int u, v;
    while (k--)
    {
        scanf("%d %d", &u, &v);
        add(u, v);
    }
    int result = 0;
    for (int i = 1; i <= n; i++)
    {
        fill(st, st + N, false);
        if (find(i))
        {
            result++;
        }
    }
    printf("%d", result);
    return 0;
}
```

</details>

---

## 🧠 8 思维导图总结

![最短路](assets/最短路.png)

![搜索与图论](assets/搜索与图论.png)

![image-20240208134725963](assets/image-20240208134725963.png)
