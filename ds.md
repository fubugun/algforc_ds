
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


我把这两道算法题的**题目、正确代码、解析**整理如下：


## 第1题：求二叉树的节点总数
#### 题目
编写算法，求一棵二叉树的节点总数。


#### 正确代码（递归实现，先序遍历）
```c
// 二叉树节点结构定义
typedef struct BiTNode {
    int data;
    struct BiTNode *lchild, *rchild;
} BiTNode, *BiTree;

// 统计节点总数（n为计数器，需传入指针）
void count_preorder(BiTree t, int *n) {
    if (t != NULL) {
        (*n)++;  // 访问当前节点，计数器+1
        count_preorder(t->lchild, n);  // 递归遍历左子树
        count_preorder(t->rchild, n);  // 递归遍历右子树
    }
}
```


#### 解析
- 思路：通过**先序遍历**（根→左→右）遍历二叉树，每遇到一个非空节点，就将计数器加1；
- 注意：计数器`n`需用**指针传递**（否则递归中无法修改外部变量）；
- 复杂度：时间复杂度O(n)（n为节点数，需遍历所有节点），空间复杂度O(h)（h为树的深度，递归栈开销）。


---


## 第2题：有序顺序表的插入（保持有序性）
#### 题目
设顺序表`Va`中的数据元素有序递增，试写算法，将`x`插入到顺序表的适当位置，保持该表的有序性。


#### 正确代码
```c
// 顺序表结构（假设ElemType为数据类型，如int）
typedef struct {
    ElemType *data;  // 数据数组
    int length;      // 当前长度
    int maxSize;     // 最大容量
} SqList;

// 将x插入有序顺序表Va，保持递增有序
void insert_sq(SqList *Va, ElemType x) {
    int i, j, n;
    n = Va->length;  // 当前表长

    // 情况1：x比最后一个元素大，直接插在末尾
    if (x >= Va->data[n-1]) {
        Va->data[n] = x;
    } 
    // 情况2：x插在中间位置，需后移元素
    else {
        // 找到插入位置i（第一个比x大的元素的下标）
        i = 0;
        while (x > Va->data[i]) {
            i++;
        }
        // 将i及之后的元素向后移1位
        for (j = n-1; j >= i; j--) {
            Va->data[j+1] = Va->data[j];
        }
        // 插入x
        Va->data[i] = x;
    }
    // 表长+1
    Va->length++;
}
```


#### 解析
- 步骤：
  1. **先判断`x`是否比表尾元素大：若是，直接插入表尾；**
  2. 若不是，找到**第一个比x大的元素的下标i**（插入位置）；
  3. 将`i`及之后的元素向后移动1位，腾出位置插入`x`；
  4. 表长加1；
- 复杂度：时间复杂度O(n)（最坏情况需移动n个元素），空间复杂度O(1)（原地操作）。

---
## 题目：从邻接表转换成邻接矩阵的算法

---

#### 思路

要把一个图的 **邻接表（AdjList）** 转换成 ​**邻接矩阵（AdjMatrix）**​，需要做以下步骤：

* 邻接表用链表的方式存储顶点的邻居
* 邻接矩阵是 n×n 数组，matrix[i][j] = 1 表示存在边

---

#### 完整的 C 语言代码（含详细注释）

```c
#include <stdio.h>
#include <stdlib.h>

#define MAXV 100   // 最大顶点数
#define INF 0      // 无边时的值（可以设为 0 或 INF）

/*-----------------------------*
 *   邻接表的数据结构定义
 *-----------------------------*/

// 边结点结构
typedef struct ArcNode {
    int adjvex;              // 该边指向的顶点下标
    struct ArcNode *next;    // 下一条边
} ArcNode;

// 顶点表头结点
typedef struct VNode {
    ArcNode *first;          // 指向第一条边
} VNode;

// 图的邻接表结构
typedef struct {
    VNode vertices[MAXV];    // 顶点数组
    int vexnum;              // 顶点数
    int arcnum;              // 边数
} AdjListGraph;

/*-----------------------------*
 *   邻接表 → 邻接矩阵转换函数
 *-----------------------------*/

void AdjListToMatrix(AdjListGraph G, int matrix[MAXV][MAXV]) {
    // 1. 初始化邻接矩阵为 0
    for (int i = 0; i < G.vexnum; i++) {
        for (int j = 0; j < G.vexnum; j++) {
            matrix[i][j] = 0;   // 或设为 INF
        }
    }

    // 2. 遍历每个顶点 i 的边链表
    for (int i = 0; i < G.vexnum; i++) {
        ArcNode *p = G.vertices[i].first;   // 指向第一个边
        while (p != NULL) {
            int j = p->adjvex;              // 得到 i → j
            matrix[i][j] = 1;               // 置为 1（或赋权值）
            p = p->next;                    // 继续遍历下一条边
        }
    }
}

```


## 将顺序表存储的完全二叉树转换为二叉链表结构

顺序表 T 定义如下：

```c
struct tree{
    int no;
    ELEMTYPE data;
} T[N];
```

---

### 1. 前提概念

**完全二叉树的顺序存储规则（从 1 开始记下标）：**

* 若节点下标为 `i`

  * 左孩子下标：`2i`
  * 右孩子下标：`2i + 1`
* 若 `2i > N`，无左孩子
* 若 `2i + 1 > N`，无右孩子

**二叉链表节点定义：**

```c
typedef struct BiTNode {
    int no;
    ELEMTYPE data;
    struct BiTNode *lchild, *rchild;
} BiTNode, *BiTree;
```

---

### 2. 算法设计（递 归实现）

利用完全二叉树的下标规律构建每个节点：

```c
// 参数：顺序表 T、当前节点的下标 i（从 1 开始）
BiTree CreateBiTree(struct tree T[], int i, int len) {
    // 递归终止条件
    if (i > len) {
        return NULL;
    }

    // 创建当前节点
    BiTree node = (BiTree)malloc(sizeof(BiTNode));
    node->no = T[i-1].no;        // 顺序表从 0 开始 → 对应 i-1
    node->data = T[i-1].data;

    // 构建左子树
    node->lchild = CreateBiTree(T, 2*i, len);

    // 构建右子树
    node->rchild = CreateBiTree(T, 2*i+1, len);

    return node;
}
```

---

### 3. 调用方式

```c
BiTree root = CreateBiTree(T, 1, N);
```

---

如需我把两部分合并成一个统一风格的文档，也可以告诉我~

-----
## 题目1：有两个循环单链表，链表头指针分别为h1和h2，编写一个函数将链表h2链接到链表h1之后，要求链接后的链表仍保持循环链表形式。


### 思路
1. **循环单链表特性**：循环单链表的尾节点指针指向头节点（即尾节点的`next`等于头指针）。
2. **核心操作**：
   - 若h1为空，直接返回h2（h2本身已是循环链表）；若h2为空，直接返回h1。
   - 找到h1的尾节点（满足`p1->next == h1`的节点p1）。
   - 找到h2的尾节点（满足`p2->next == h2`的节点p2）。
   - 链接操作：h1的尾节点指向h2的头节点（`p1->next = h2`），h2的尾节点指向h1的头节点（`p2->next = h1`），形成新的循环链表。
3. **返回结果**：链接后的循环链表头指针仍为h1。


### 代码
```c
#include <stdio.h>
#include <stdlib.h>

// 定义循环单链表节点结构
typedef struct CNode {
    int data;
    struct CNode* next;
} CNode;

// 创建循环单链表（示例：从数组创建）
CNode* createCircularList(int* arr, int n) {
    if (n == 0) return NULL;
    CNode* head = (CNode*)malloc(sizeof(CNode));
    head->data = arr[0];
    CNode* tail = head;
    for (int i = 1; i < n; i++) {
        CNode* newNode = (CNode*)malloc(sizeof(CNode));
        newNode->data = arr[i];
        tail->next = newNode;
        tail = newNode;
    }
    tail->next = head; // 尾节点指向头节点，形成循环
    return head;
}

// 打印循环单链表（打印前n个节点，避免无限循环）
void printCircularList(CNode* head, int n) {
    if (head == NULL) {
        printf("链表为空\n");
        return;
    }
    CNode* p = head;
    for (int i = 0; i < n; i++) {
        printf("%d ", p->data);
        p = p->next;
    }
    printf("\n");
}

// 将h2链接到h1之后，返回新的循环链表头指针（h1）
CNode* linkTwoCircularLists(CNode* h1, CNode* h2) {
    // 边界情况：若h1为空，返回h2；若h2为空，返回h1
    if (h1 == NULL) return h2;
    if (h2 == NULL) return h1;

    // 找到h1的尾节点（p1->next == h1）
    CNode* p1 = h1;
    while (p1->next != h1) {
        p1 = p1->next;
    }

    // 找到h2的尾节点（p2->next == h2）
    CNode* p2 = h2;
    while (p2->next != h2) {
        p2 = p2->next;
    }

    // 链接两个循环链表
    p1->next = h2;   // h1的尾节点指向h2的头节点
    p2->next = h1;   // h2的尾节点指向h1的头节点，形成新循环

    return h1;
}

// 释放循环单链表内存
void freeCircularList(CNode* head) {
    if (head == NULL) return;
    CNode* p = head->next;
    while (p != head) {
        CNode* temp = p;
        p = p->next;
        free(temp);
    }
    free(head);
}

int main() {
    // 测试数据
    int arr1[] = {1, 2, 3};
    int arr2[] = {4, 5, 6};
    CNode* h1 = createCircularList(arr1, 3);
    CNode* h2 = createCircularList(arr2, 3);

    printf("链接前h1（前3个节点）：");
    printCircularList(h1, 3); // 1 2 3
    printf("链接前h2（前3个节点）：");
    printCircularList(h2, 3); // 4 5 6

    CNode* newHead = linkTwoCircularLists(h1, h2);

    printf("链接后（前6个节点）：");
    printCircularList(newHead, 6); // 1 2 3 4 5 6

    freeCircularList(newHead);
    return 0;
}
```


### 说明
- **循环链表识别**：通过尾节点的`next`指向头节点的特性，准确定位h1和h2的尾节点。
- **边界处理**：兼容h1或h2为空的情况，保证函数健壮性。
- **链接逻辑**：通过两次指针调整（h1尾接h2头，h2尾接h1头），确保新链表仍是循环结构，头指针保持为h1。

----
## 题目2：在二叉树中查找给定值为x的结点，试编写算法找出值为x的结点并输出值为x的结点的所有祖先，假设值为x的结点仅有一个或不存在。


### 思路
1. **二叉树结构**：节点包含数据域、左孩子指针、右孩子指针。
2. **核心逻辑**：
   - 采用递归遍历（先序遍历思路），从根节点开始查找x。
   - 若当前节点为x，返回“找到”的标志（通知父节点）。
   - 若左子树中找到x，当前节点是祖先，输出当前节点数据。
   - 若右子树中找到x，当前节点是祖先，输出当前节点数据。
   - 若遍历结束未找到x，输出提示信息。
3. **特殊情况**：若x是根节点，无祖先，直接提示；若树为空，直接提示。


### 代码
```c
#include <stdio.h>
#include <stdlib.h>

// 定义二叉树节点结构
typedef struct BiNode {
    int data;
    struct BiNode* left;
    struct BiNode* right;
} BiNode;

// 创建二叉树节点
BiNode* createBiNode(int data) {
    BiNode* node = (BiNode*)malloc(sizeof(BiNode));
    node->data = data;
    node->left = NULL;
    node->right = NULL;
    return node;
}

// 递归查找x并输出其祖先（辅助函数）
// 返回值：1表示找到x，0表示未找到
int findXAndAncestors(BiNode* node, int x) {
    if (node == NULL) return 0; // 空节点，未找到

    // 若当前节点是x，返回找到标志
    if (node->data == x) return 1;

    // 查找左子树，若找到x，当前节点是祖先
    if (findXAndAncestors(node->left, x)) {
        printf("%d ", node->data);
        return 1;
    }

    // 查找右子树，若找到x，当前节点是祖先
    if (findXAndAncestors(node->right, x)) {
        printf("%d ", node->data);
        return 1;
    }

    return 0; // 左右子树均未找到
}

// 主函数：查找x并输出其所有祖先
void findAncestors(BiNode* root, int x) {
    if (root == NULL) {
        printf("二叉树为空，无法查找\n");
        return;
    }

    // 若x是根节点，无祖先
    if (root->data == x) {
        printf("值为%d的节点是根节点，没有祖先\n", x);
        return;
    }

    // 调用辅助函数查找
    int found = findXAndAncestors(root, x);
    if (!found) {
        printf("未找到值为%d的节点\n", x);
    } else {
        printf("是值为%d的节点的所有祖先\n", x);
    }
}

// 释放二叉树内存
void freeBiTree(BiNode* root) {
    if (root == NULL) return;
    freeBiTree(root->left);
    freeBiTree(root->right);
    free(root);
}

int main() {
    // 构建示例二叉树：
    //       1
    //      / \
    //     2   3
    //    / \
    //   4   5
    BiNode* root = createBiNode(1);
    root->left = createBiNode(2);
    root->right = createBiNode(3);
    root->left->left = createBiNode(4);
    root->left->right = createBiNode(5);

    // 测试查找
    findAncestors(root, 5); // 输出：2 1 是值为5的节点的所有祖先
    findAncestors(root, 1); // 输出：值为1的节点是根节点，没有祖先
    findAncestors(root, 6); // 输出：未找到值为6的节点

    freeBiTree(root);
    return 0;
}
```


### 说明
- **递归逻辑**：通过递归遍历左、右子树，利用返回值传递“是否找到x”的信息，当子树找到x时，当前节点即为祖先并输出。
- **祖先顺序**：输出顺序为“直接父节点→...→根节点”，符合祖先层级关系（根节点是最顶层祖先）。
- **特殊处理**：单独判断x为根节点或树为空的情况，确保输出准确。
-----


下面按你要求的格式 **“题目 → 思路 → 代码”** 给出两道算法题的标准答案，清晰明了，非常适合写到试卷或作业中。

---

## **算法题 1：判断链表是否有环并返回入口（或 NULL）**

---

### **题目**

给定一个单链表，判断该链表是否存在环。如果存在环，返回环开始的入口节点；如果不存在环，返回 `NULL`。

---

## **思路**

使用 **Floyd 快慢指针算法（龟兔赛跑算法）**：

1. 定义两个指针：`slow` 每次走一步，`fast` 每次走两步。
2. 若 `fast` 或 `fast->next` 为空，则链表无环。
3. 若 `slow == fast` 相遇，说明链表有环。
4. 为找到环入口：

   * 让 `slow` 停在相遇点
   * 让 `fast` 回到链表头
   * 两个指针同时每次各走一步
   * 再次相遇时，该点即为 **环入口**


---

### **代码**

```c
typedef struct Node {
    int val;
    struct Node *next;
} Node;

// 返回环入口节点；无环则返回 NULL
Node* detectCycle(Node *head) {
    Node *slow = head, *fast = head;

    // 第一阶段：判断是否有环
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;

        if (slow == fast) {   // 相遇，说明有环
            fast = head;      // fast 回到头
            // 第二阶段：寻找入口
            while (fast != slow) {
                fast = fast->next;
                slow = slow->next;
            }
            return fast;      // fast/slow 所在位置即入口
        }
    }
    return NULL;              // 无环
}
```

---

##  **算法题 2：二叉树的相似性判断**

---

### **题目**

给定两棵二叉树，判断它们是否“相似”。

**相似的定义：**

* 两个结点同时为空 ⇒ 相似
* 两个结点不同时空 ⇒ 不相似
* 两个结点都不为空 ⇒ 它们的左子树相似并且右子树相似

注意：“相似”只比较两棵树的 **形状结构**，不比较节点值。

---

### **思路**

使用递归判断：

1. 若两个节点都为空，返回真。
2. 若只有一个为空，返回假。
3. 若两个都不为空：

   * 递归比较左子树是否相似
   * 递归比较右子树是否相似
   * 两者都相似 ⇒ 这两个节点也相似

时间复杂度 O(n)，空间复杂度 O(h)，其中 h 为树高。

---

### **代码**

```c
typedef struct TreeNode {
    int val;
    struct TreeNode *left, *right;
} TreeNode;

// 判断两棵树是否相似
int isSimilar(TreeNode *a, TreeNode *b) {
    if (!a && !b) return 1;      // 都为空 ⇒ 相似
    if (!a || !b) return 0;      // 一个空一个不空 ⇒ 不相似
    return isSimilar(a->left, b->left) &&
           isSimilar(a->right, b->right);
}
```

---




