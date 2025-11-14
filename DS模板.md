用户：
这事数据接轨算法题 dfs bfs 快排 前中厚序遍历 非递归前序中序 层序的模板代码  你整理一下
豆包：
我把这些数据结构算法的模板代码按类别整理好了，清晰区分功能~


### 一、图的遍历（邻接矩阵实现）
#### 1. 深度优先遍历（DFS）
```c
// 辅助标记数组（全局/外部定义）
int visited[100]; 

// 递归实现DFS
void DFS_M(ALGraph G, int v) {
    visited[v] = 1;                  // 标记已访问
    printf("%d ", G.vertices[v]);    // 访问顶点
    for (int u = 0; u < G.vexnums; u++) {
        // 存在边且顶点u未访问
        if (G.arcs[v][u] == 1 && visited[u] == 0) {
            DFS_M(G, u);
        }
    }
}

// DFS遍历（处理非连通图）
void DFSTraverse_M(MGraph G) {
    // 初始化标记数组
    for (int i = 0; i < G.vexnums; i++) {
        visited[i] = 0;
    }
    // 遍历所有顶点，处理非连通分量
    for (int i = 0; i < G.vexnums; i++) {
        if (!visited[i]) {
            DFS_M(G, i);
        }
    }
}
```


#### 2. 广度优先遍历（BFS）
```c
// 辅助标记数组（全局/外部定义）
int visited[100];

// 队列实现BFS
void BFS(MGraph G, int v) {
    queue<int> q;
    visited[v] = 1;                  // 标记起始顶点
    printf("%d ", G.vexs[v]);        // 访问顶点
    q.push(v);                       // 入队
    while (!q.empty()) {
        int u = q.front();           // 取队首
        q.pop();                     // 出队
        // 遍历所有邻接顶点
        for (int i = 0; i < G.vexnums; i++) {
            if (G.arcs[u][i] == 1 && !visited[i]) {
                visited[i] = 1;
                printf("%d ", G.vexs[i]);
                q.push(i);           // 邻接顶点入队
            }
        }
    }
}

// BFS遍历（处理非连通图）
void BFSTraverse(MGraph G) {
    // 初始化标记数组
    for (int i = 0; i < G.vexnums; i++) {
        visited[i] = 0;
    }
    // 遍历所有顶点，处理非连通分量
    for (int i = 0; i < G.vexnums; i++) {
        if (!visited[i]) {
            BFS(G, i);
        }
    }
}
```


### 二、快速排序
```c
// 交换函数（辅助）
void swap(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

// 分区函数（找基准位置）
int Partition(int arr[], int left, int right) {
    int i = left;
    int j = right;
    int key = arr[left];  // 选左端点为基准
    while (i != j) {
        // 右指针左移，找小于基准的值
        while (i < j && arr[j] >= key) j--;
        // 左指针右移，找大于基准的值
        while (i < j && arr[i] <= key) i++;
        swap(&arr[i], &arr[j]);  // 交换左右指针的值
    }
    swap(&arr[left], &arr[i]);   // 基准放到最终位置
    return i;  // 返回基准位置
}

// 快速排序递归实现
void quickSort(int arr[], int low, int high) {
    if (low < high) {
        int pi = Partition(arr, low, high);  // 分区
        quickSort(arr, low, pi - 1);         // 排序左半部分
        quickSort(arr, pi + 1, high);        // 排序右半部分
    }
}
```


### 三、二叉树遍历
#### （1）递归遍历
```c
// 二叉树节点结构（需提前定义）
typedef struct BiTNode {
    int data;
    struct BiTNode *lchild, *rchild;
} BiTNode, *BiTree;
```

- **前序遍历（根→左→右）**
```c
void preOrder(BiTree T) {
    if (T == NULL) return;
    printf("%d ", T->data);  // 访问根节点
    preOrder(T->lchild);     // 递归左子树
    preOrder(T->rchild);     // 递归右子树
}
```

- **中序遍历（左→根→右）**
```c
void inOrder(BiTree T) {
    if (T == NULL) return;
    inOrder(T->lchild);      // 递归左子树
    printf("%d ", T->data);  // 访问根节点
    inOrder(T->rchild);      // 递归右子树
}
```

- **后序遍历（左→右→根）**
```c
void postOrder(BiTree T) {
    if (T == NULL) return;
    postOrder(T->lchild);    // 递归左子树
    postOrder(T->rchild);    // 递归右子树
    printf("%d ", T->data);  // 访问根节点
}
```


#### （2）非递归遍历（栈实现）
- **非递归前序遍历**
```c
void preOrderIterative(BiTree T) {
    if (T == NULL) return;  // 树空的话直接返回
    stack<BiNode*> st;      // 定义一个“存二叉树节点指针”的栈
    st.push(T);             // 第一步：把根节点(1)压入栈

    while (!st.empty()) {   // 栈不空就一直循环
        BiNode* node = st.top();  // 取出栈顶节点（此时是根节点1）
        st.pop();                 // 把栈顶节点弹出栈

        printf("%d ", node->data);  // 访问节点（输出1）

        // 关键：栈是“先进后出”，所以要先压右子树、再压左子树
        if (node->rchild != NULL) st.push(node->rchild);  // 压右子树(3)
        if (node->lchild != NULL) st.push(node->lchild);  // 压左子树(2)
    }
}

```

- **非递归中序遍历**
```c
void inOrderIterative(BiTree T) {
    if (T == NULL) return;
    BiTNode* stack[100];
    int top = -1;
    BiTree p = T;
    while (p != NULL || top != -1) {
        // 左子树全部入栈
        while (p != NULL) {
            stack[++top] = p;
            p = p->lchild;
        }
        p = stack[top--];  // 弹出节点
        printf("%d ", p->data);  // 访问节点
        p = p->rchild;     // 转向右子树
    }
}
```


#### （3）层序遍历（队列实现）
```c
void levelOrder(BiTree T) {
    if (T == NULL) return;
    queue<BiTNode*> q;
    q.push(T);  // 根节点入队
    while (!q.empty()) {
        BiTNode* node = q.front();  // 取队首
        q.pop();
        printf("%d ", node->data);  // 访问节点
        // 左右子节点入队
        if (node->lchild != NULL) q.push(node->lchild);
        if (node->rchild != NULL) q.push(node->rchild);
    }
}
```


要不要我帮你补充这些模板对应的**调用示例代码**，方便你直接测试？

内容由 AI 生成
