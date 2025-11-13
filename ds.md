
------------------------------------------------------------------------

## 题目一：有向图根节点判定

### 🧠 思路

若结点 r 能到达所有结点，则它是根节点。\
因此：\
1. 对每个结点 r，从 r 出发做一次 DFS/BFS；\
2. 若访问到的结点数等于 n，则 r 为根节点；\
3. 输出所有这样的结点。

### 💻 代码实现

``` c
#include <stdio.h>
#include <stdlib.h>
#define MAXV 100
int n;                        // 顶点数
int G[MAXV][MAXV];            // 邻接矩阵
int visited[MAXV];            // 访问标记

void DFS(int v) {
    visited[v] = 1;
    for (int i = 0; i < n; i++) {
        if (G[v][i] && !visited[i])
            DFS(i);
    }
}

// 判断一个顶点是否为根
int isRoot(int v) {
    for (int i = 0; i < n; i++)
        visited[i] = 0;       // 重置访问数组
    DFS(v);
    for (int i = 0; i < n; i++)
        if (!visited[i])
            return 0;         // 有结点不可达，不是根
    return 1;                 // 所有结点可达，是根
}

int main() {
    int e, u, v;
    printf("输入顶点数 n 和 边数 e: ");
    scanf("%d %d", &n, &e);
    // 初始化邻接矩阵
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            G[i][j] = 0;

    printf("输入每条有向边 (u v):\n");
    for (int i = 0; i < e; i++) {
        scanf("%d %d", &u, &v);
        G[u][v] = 1;
    }

    int hasRoot = 0;
    printf("根节点为: ");
    for (int i = 0; i < n; i++) {
        if (isRoot(i)) {
            printf("%d ", i);
            hasRoot = 1;
        }
    }
    if (!hasRoot)
        printf("无根节点");
    printf("\n");
    return 0;
}
```

------------------------------------------------------------------------

## 题目二：不带头结点的链表按值从小到大排序

### 🧠 思路

使用直接插入排序：

1.  维护两个区域：
    -   已排序区：初始时只有第一个结点；\
    -   未排序区：其余结点；\
2.  逐个从未排序区取出结点 p；\
3.  从已排序区头开始，找到 p-\>data 应插入的位置；\
4.  将 p 插入；\
5.  重复直到未排序区为空。

⚠️ 要求：不能使用额外节点空间（即不新建节点，只改指针）。

### 💻 代码实现

``` c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int data;
    struct Node *link;
} Node;

// 创建链表
Node* createList(int n) {
    Node *head = NULL, *p = NULL;
    for (int i = 0; i < n; i++) {
        Node *node = (Node*)malloc(sizeof(Node));
        scanf("%d", &node->data);
        node->link = NULL;
        if (head == NULL)
            head = node;
        else
            p->link = node;
        p = node;
    }
    return head;
}

// 插入排序法重排链表（不创建新节点）
Node* insertionSortList(Node *head) {
    if (head == NULL || head->link == NULL)
        return head;  // 空表或单节点，直接返回

    Node *sorted = head;         // 已排序部分的头
    Node *unsorted = head->link; // 未排序部分的第一个
    sorted->link = NULL;         // 截断

    while (unsorted != NULL) {
        Node *p = unsorted;          // 当前要插入的节点
        unsorted = unsorted->link;   // 记录下一个未排序节点

        // 情况1：插入到排序链表的头部
        if (p->data < sorted->data) {
            p->link = sorted;
            sorted = p;
        } else {
            // 情况2：在已排序链表中找到插入位置
            Node *q = sorted;
            while (q->link != NULL && q->link->data < p->data)
                q = q->link;
            // 插入到 q 之后
            p->link = q->link;
            q->link = p;
        }
    }
    return sorted;
}

// 打印链表
void printList(Node *head) {
    Node *p = head;
    while (p != NULL) {
        printf("%d ", p->data);
        p = p->link;
    }
    printf("\n");
}

int main() {
    int n;
    printf("输入链表长度: ");
    scanf("%d", &n);
    printf("输入链表元素: ");
    Node *list = createList(n);
    list = insertionSortList(list);
    printf("排序后链表: ");
    printList(list);
    return 0;
}
```

------------------------------------------------------------------------

## 题目三：按行均值对矩阵排序

### 🧠 思路

1.  计算每行平均值：遍历矩阵每一行，求和后除以 n。\
2.  记录行下标：将平均值与对应行号存入结构体数组。\
3.  对平均值数组排序：按平均值升序排序。\
4.  重新排列矩阵行：按排序结果输出新矩阵。

### 💻 代码实现

``` c
#include <stdio.h>
#include <stdlib.h>
#define MAX 100

typedef struct {
    double avg;   // 行平均值
    int index;    // 原行下标
} RowInfo;

// 比较函数，用于 qsort 按 avg 升序排序
int cmp(const void *a, const void *b) {
    RowInfo *ra = (RowInfo *)a;
    RowInfo *rb = (RowInfo *)b;
    if (ra->avg > rb->avg) return 1;
    else if (ra->avg < rb->avg) return -1;
    else return 0;
}

int main() {
    int n;
    double matrix[MAX][MAX];

    // 输入矩阵大小
    printf("请输入矩阵大小 n: ");
    scanf("%d", &n);

    // 输入矩阵元素
    printf("请输入 %d x %d 矩阵元素:\n", n, n);
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            scanf("%lf", &matrix[i][j]);

    // 计算每行平均值
    RowInfo rows[MAX];
    for (int i = 0; i < n; i++) {
        double sum = 0;
        for (int j = 0; j < n; j++)
            sum += matrix[i][j];
        rows[i].avg = sum / n;
        rows[i].index = i;
    }

    // 按行平均值升序排序
    qsort(rows, n, sizeof(RowInfo), cmp);

    // 输出新矩阵
    printf("按行平均值升序排列后的矩阵:\n");
    for (int i = 0; i < n; i++) {
        int rowIndex = rows[i].index;
        for (int j = 0; j < n; j++)
            printf("%.2lf ", matrix[rowIndex][j]);
        printf("\n");
    }

    return 0;
}
```
---
## 题目四  由inorder postorder构造二叉树
已知一棵二叉树的中序序列和后序序列，写一个建立该二叉树的二叉链表存 储结
构的算法。

###  一、题目分析

已知：

* **中序序列**（Inorder）：`Left → Root → Right`
* **后序序列**（Postorder）：`Left → Right → Root`

目标：

> 根据这两种遍历序列 **唯一确定一棵二叉树**，并用**二叉链表存储结构**建立这棵树。

---

### 🧩 二、思路讲解

1. **后序序列的最后一个结点**一定是**根节点**。
2. 在**中序序列**中找到该根节点的位置：

   * 左边部分是 **左子树的中序序列**
   * 右边部分是 **右子树的中序序列**
3. 根据左右子树的结点数，在**后序序列**中划分出左右子树对应的序列部分。
4. 递归构造左右子树。

---

### 🪴 三、二叉链表结构定义

```c
typedef struct BiTNode {
    char data;                  // 数据域
    struct BiTNode *lchild;     // 左孩子
    struct BiTNode *rchild;     // 右孩子
} BiTNode, *BiTree;
```

---

### 🔁 四、递归建树算法

### 思路伪代码

```
BuildTree(in[], post[], n):
    若 n == 0:
        返回 NULL
    root_data = post[n-1]  // 后序的最后一个结点是根
    创建 root 结点
    在中序中找到 root_data 的位置 k
    左子树结点数 = k
    root->lchild = BuildTree(in[0..k-1], post[0..k-1], k)
    root->rchild = BuildTree(in[k+1..n-1], post[k..n-2], n-k-1)
    返回 root
```

---

### 💻 五、完整 C 语言实现（含详细注释）

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct BiTNode {
    char data;
    struct BiTNode *lchild, *rchild;
} BiTNode, *BiTree;

// 根据中序和后序序列构造二叉树
BiTree CreateTree(char *inorder, char *postorder, int n) {
    if (n <= 0) return NULL;  // 空树

    // 后序序列的最后一个元素是根节点
    char rootData = postorder[n - 1];
    BiTree root = (BiTree)malloc(sizeof(BiTNode));
    root->data = rootData;
    root->lchild = root->rchild = NULL;

    // 在中序序列中找到根节点的位置
    int k;
    for (k = 0; k < n; k++) {
        if (inorder[k] == rootData)
            break;
    }

    // 递归构造左子树和右子树
    root->lchild = CreateTree(inorder, postorder, k);  // 左子树
    root->rchild = CreateTree(inorder + k + 1, postorder + k, n - k - 1);  // 右子树

    return root;
}

// 中序遍历验证结果
void InOrder(BiTree T) {
    if (T) {
        InOrder(T->lchild);
        printf("%c ", T->data);
        InOrder(T->rchild);
    }
}

// 后序遍历验证结果
void PostOrder(BiTree T) {
    if (T) {
        PostOrder(T->lchild);
        PostOrder(T->rchild);
        printf("%c ", T->data);
    }
}

int main() {
    char inorder[]   = {'D','B','E','A','F','C'};
    char postorder[] = {'D','E','B','F','C','A'};
    int n = 6;

    BiTree T = CreateTree(inorder, postorder, n);

    printf("中序遍历结果: ");
    InOrder(T);
    printf("\n后序遍历结果: ");
    PostOrder(T);
    printf("\n");

    return 0;
}
```

---

### 🧾 六、输出结果验证

```
中序遍历结果: D B E A F C 
后序遍历结果: D E B F C A 
```

✅ 与输入一致，说明构造正确。

---
## 题目五  在数组中排好kn的位置
设有一个数组中存放了一个无序的关键序列 \( K_1, K_2, \ldots, K_n \)。现要求将 \( K_n \) 放在其排序后的正确位置上，试编写实现该功能的算法，要求比较关键字的次数不超过 \( n \)。  


### 思路  
利用快速排序的核心步骤——**划分（Partition）** 实现：  
1. 选择数组最后一个元素 \( K_n \) 作为基准（pivot）；  
2. 遍历数组前 \( n-1 \) 个元素，将所有小于等于基准的元素移到左侧，大于基准的元素自然留在右侧；  
3. 遍历结束后，基准元素的正确位置即为左侧元素的边界，将其放置在此位置即可。  

该过程仅需与前 \( n-1 \) 个元素各比较一次，总比较次数为 \( n-1 \leq n \)，满足要求。  


### C代码实现（基于快排划分思想）  
```c
#include <stdio.h>

// 交换两个元素
void swap(int* a, int* b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

// 快排划分函数：将最后一个元素放到正确位置
int partition(int arr[], int low, int high) {
    int pivot = arr[high]; // 以最后一个元素为基准（K_n）
    int i = low - 1;       // 标记左侧区域（≤pivot）的右边界

    // 遍历前n-1个元素（[low, high-1]）
    for (int j = low; j <= high - 1; j++) {
        if (arr[j] <= pivot) { // 当前元素≤基准时，放入左侧区域
            i++;
            swap(&arr[i], &arr[j]);
        }
    }

    // 将基准放到正确位置（i+1）
    swap(&arr[i + 1], &arr[high]);
    return i + 1; // 返回基准位置（可选）
}

// 封装函数：处理整个数组
void putLastInPlace(int arr[], int n) {
    if (n <= 1) return; // 数组长度≤1时无需操作
    partition(arr, 0, n - 1);
}

// 测试
int main() {
    int arr[] = {6, 2, 8, 1, 5, 4}; // K_n=4
    int n = sizeof(arr) / sizeof(arr[0]);
    
    putLastInPlace(arr, n);
    
    printf("处理后数组：");
    for (int i = 0; i < n; i++) {
        printf("%d ", arr[i]); // 输出：2 1 4 6 5 8（4的位置正确）
    }
    
    return 0;
}
```


### 说明  
- 算法核心是快排的划分过程，仅需一次遍历即可定位 \( K_n \) 的正确位置；  
- 比较次数为 \( n-1 \)，严格满足“不超过 \( n \)”的限制；  
- 定位后，\( K_n \) 左侧元素均≤它，右侧元素均>它，与排序后该元素的位置一致。