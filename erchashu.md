# 二叉树核心算法题完整整理（含递归遍历等所有题目）

## 一、二叉树递归遍历（前序、中序、后序）

### 题目描述
给定二叉树，分别通过前序（根→左→右）、中序（左→根→右）、后序（左→右→根）递归遍历所有节点，输出节点数据。

### 整体思路
1. 递归遍历核心：基于“分治思想”，将二叉树拆解为根节点、左子树、右子树三部分；
2. 终止条件：当前节点为NULL时直接返回；
3. 遍历顺序差异：仅调整“访问根节点”的时机，左子树遍历始终在右子树之前。

### 核心代码（C++）
```cpp
// 二叉树节点定义（统一复用）
typedef struct BiTnode {
    int data;                  // 数据域
    struct BiTnode *lchild;    // 左孩子指针
    struct BiTnode *rchild;    // 右孩子指针
} BiTnode, *BiTree;

// 1. 前序遍历（根→左→右）
void preOrder(BiTree T) {
    if (T == NULL) {
        return;  // 节点为空，递归终止
    }
    printf("%d ", T->data);    // 先访问根节点
    preOrder(T->lchild);       // 递归遍历左子树
    preOrder(T->rchild);       // 递归遍历右子树
}

// 2. 中序遍历（左→根→右）
void inOrder(BiTree T) {
    if (T == NULL) {
        return;  // 节点为空，递归终止
    }
    inOrder(T->lchild);        // 先递归遍历左子树
    printf("%d ", T->data);    // 再访问根节点
    inOrder(T->rchild);        // 最后递归遍历右子树
}

// 3. 后序遍历（左→右→根）
void postOrder(BiTree T) {
    if (T == NULL) {
        return;  // 节点为空，递归终止
    }
    postOrder(T->lchild);      // 先递归遍历左子树
    postOrder(T->rchild);      // 再递归遍历右子树
    printf("%d ", T->data);    // 最后访问根节点
}
```

---

## 二、二叉树非递归遍历（前序、中序）

### 题目描述
给定二叉树，分别通过栈实现前序、中序非递归遍历，输出节点数据（避免递归栈溢出问题）。

### 整体思路
1. 利用栈模拟递归调用过程，通过“入栈/出栈”控制节点访问顺序；
2. 前序非递归：根节点先入栈，出栈时访问，再将右子树入栈（后访问）、左子树入栈（先访问）；
3. 中序非递归：左子树全部入栈后，出栈时访问节点，再处理右子树。

### 核心代码（C++）
```cpp
#include <stack>  // 引入栈头文件
using namespace std;

// 1. 前序遍历（非递归，栈实现）
void preOrderIterative(BiTree T) {
    if (T == NULL) {
        return;  // 树空，直接返回
    }
    stack<BiTnode*> st;  // 栈存储二叉树节点指针
    st.push(T);          // 根节点入栈

    while (!st.empty()) {
        BiTnode *node = st.top();  // 取出栈顶节点
        st.pop();                  // 栈顶节点出栈（避免重复处理）
        printf("%d ", node->data); // 访问当前节点

        // 右子树先入栈（栈先进后出，保证左子树先访问）
        if (node->rchild != NULL) {
            st.push(node->rchild);
        }
        // 左子树后入栈，出栈时在右子树之前
        if (node->lchild != NULL) {
            st.push(node->lchild);
        }
    }
}

// 2. 中序遍历（非递归，栈实现）
void inOrderIterative(BiTree T) {
    if (T == NULL) {
        return;  // 树空，直接返回
    }
    BiTnode *stack[100];  // 数组模拟栈（存储节点指针）
    int top = -1;         // 栈顶指针，初始为-1（栈空）
    BiTree p = T;         // 遍历指针，从根节点开始

    while (p != NULL || top != -1) {
        // 左子树全部入栈（直到无左子节点）
        while (p != NULL) {
            stack[++top] = p;  // 栈顶指针上移，节点入栈
            p = p->lchild;     // 指针指向左子节点
        }
        // 栈不为空时，出栈并访问节点
        if (top != -1) {
            p = stack[top--];  // 栈顶节点出栈，指针指向该节点
            printf("%d ", p->data);  // 访问当前节点
            p = p->rchild;     // 指针转向右子树，重复上述过程
        }
    }
}
```

---

## 三、计算二叉树的高度（深度）

### 题目描述
给定二叉树，计算其高度（深度）：从根节点到最远叶子节点的最长路径上的节点数。

### 整体思路
1. 递归思想：二叉树高度 = 左右子树最大高度 + 1（+1为当前根节点）；
2. 终止条件：空节点高度为0（无实际节点贡献）。

### 核心代码（C++）
```cpp
int treeHeight(BiTree T) {
    if (T == NULL) {
        return 0;  // 空节点高度为0，递归终止
    }
    // 递归计算左子树高度
    int leftHeight = treeHeight(T->lchild);
    // 递归计算右子树高度
    int rightHeight = treeHeight(T->rchild);
    // 当前节点为根的树高 = 左右子树最大高度 + 1
    return (leftHeight > rightHeight) ? (leftHeight + 1) : (rightHeight + 1);
}
```

---

## 四、计算二叉树的节点总数

### 题目描述
给定二叉树，统计树中所有节点的总个数（包括根节点、中间节点、叶子节点）。

### 整体思路
1. 递归思想：节点总数 = 1（当前节点） + 左子树节点数 + 右子树节点数；
2. 终止条件：空节点贡献0个节点，直接返回。

### 核心代码（C++）
```cpp
int countNodes(BiTree T) {
    if (T == NULL) {
        return 0;  // 空节点无贡献，递归终止
    }
    // 递归计算左子树节点数
    int leftCount = countNodes(T->lchild);
    // 递归计算右子树节点数
    int rightCount = countNodes(T->rchild);
    // 当前树总节点数 = 当前节点（1） + 左右子树节点数
    return 1 + leftCount + rightCount;
}
```

---

## 五、计算二叉树的叶子节点数

### 题目描述
给定二叉树，统计叶子节点的个数（叶子节点定义：左右子树均为空的节点）。

### 整体思路
1. 递归思想：叶子节点数 = 左子树叶子节点数 + 右子树叶子节点数；
2. 终止条件：空节点贡献0个；当前节点为叶子节点（左右子树均空）贡献1个。

### 核心代码（C++）
```cpp
int countLeafNodes(BiTree T) {
    if (T == NULL) {
        return 0;  // 空节点无叶子节点，递归终止
    }
    // 当前节点为叶子节点（左右子树均空），返回1
    if (T->lchild == NULL && T->rchild == NULL) {
        return 1;
    }
    // 递归统计左、右子树的叶子节点数，求和返回
    return countLeafNodes(T->lchild) + countLeafNodes(T->rchild);
}
```

---

## 六、层序遍历

### 题目描述
给定二叉树，按层从上到下、从左到右遍历所有节点，并输出每个结点的数据。

### 整体思路
1. 利用队列“先进先出”特性辅助层序遍历；
2. 根节点入队，循环取出队首节点访问，再将其左右子节点依次入队，直至队列为空。

### 核心代码（C++）
```cpp
#include <queue>  // 引入队头文件
using namespace std;

void levelOrder(BiTree T) {
    if (T == NULL) {
        return;  // 树空，直接返回
    }
    queue<BiTnode*> q;  // 队列存储节点指针
    q.push(T);          // 根节点入队

    while (!q.empty()) {
        BiTnode *node = q.front();  // 取出队首节点
        q.pop();                    // 出队避免重复处理
        printf("%d ", node->data);  // 访问当前节点

        // 左子节点存在则入队
        if (node->lchild != NULL) {
            q.push(node->lchild);
        }
        // 右子节点存在则入队
        if (node->rchild != NULL) {
            q.push(node->rchild);
        }
    }
}
```

---

## 七、计算二叉树的最大宽度

### 题目描述
给定二叉树，计算其最大宽度（宽度定义：各层节点数的最大值）。

### 整体思路
1. 基于层序遍历，每次处理一层前记录队列大小（即当前层节点数）；
2. 用变量记录每层节点数的最大值，遍历完成后返回该最大值。

### 核心代码（C++）
```cpp
#include <queue>
using namespace std;

int maxWidth(BiTree root) {
    if (root == NULL) {
        return 0;  // 空树宽度为0
    }
    queue<BiTnode*> q;
    q.push(root);       // 根节点入队
    int maxWidth = 0;   // 记录最大宽度

    while (!q.empty()) {
        int levelSize = q.size();  // 当前层节点数 = 队列大小
        // 更新最大宽度
        if (levelSize > maxWidth) {
            maxWidth = levelSize;
        }
        // 处理当前层所有节点，将下一层节点入队
        for (int i = 0; i < levelSize; i++) {
            BiTnode *node = q.front();
            q.pop();

            if (node->lchild != NULL) {
                q.push(node->lchild);
            }
            if (node->rchild != NULL) {
                q.push(node->rchild);
            }
        }
    }
    return maxWidth;
}
```

---

## 八、计算指定层次的节点数

### 题目描述
给定二叉树和一个层次level（层次从1开始），计算该层次的节点总数。

### 整体思路（递归法）
1. 终止条件：树空或层次无效（<1）返回0；到达目标层次（level=1）返回1；
2. 递归分解：目标层次节点数 = 左子树level-1层节点数 + 右子树level-1层节点数。

### 核心代码（递归法，C++）
```cpp
int countNodesAtLevel(BiTree root, int level) {
    // 异常情况：树空或层次无效，返回0
    if (root == NULL || level < 1) {
        return 0;
    }
    // 到达目标层次，返回1（当前节点）
    if (level == 1) {
        return 1;
    }
    // 递归统计左右子树的level-1层节点数
    return countNodesAtLevel(root->lchild, level - 1) + 
           countNodesAtLevel(root->rchild, level - 1);
}
```

### 整体思路（层序遍历法）
1. 层序遍历过程中记录当前层次；
2. 遍历到目标层次时，返回当前层节点数（队列大小）；遍历结束未达目标层返回0。

### 核心代码（层序遍历法，C++）
```cpp
#include <queue>
using namespace std;

int countNodesAtLevel(BiTree root, int level) {
    if (root == NULL || level < 1) {
        return 0;  // 异常情况返回0
    }
    queue<BiTnode*> q;
    q.push(root);          // 根节点入队（第1层）
    int currentLevel = 1;  // 当前遍历层次

    while (!q.empty()) {
        int size = q.size();  // 当前层节点数
        if (currentLevel == level) {
            return size;  // 到达目标层，返回节点数
        }
        // 处理当前层，将下一层节点入队
        for (int i = 0; i < size; i++) {
            BiTnode *node = q.front();
            q.pop();

            if (node->lchild != NULL) {
                q.push(node->lchild);
            }
            if (node->rchild != NULL) {
                q.push(node->rchild);
            }
        }
        currentLevel++;  // 层次递增
    }
    return 0;  // 目标层次超过树高
}
```

---

## 九、判断二叉树是否为完全二叉树

### 题目描述
判断给定二叉树是否为完全二叉树（定义：除最后一层外，每一层都完全填充，最后一层节点靠左排列）。

### 整体思路
1. 层序遍历，队列存储所有节点（包括空节点）；
2. 用flag标记是否遇到空节点，遇到第一个空节点后，后续若出现非空节点则不是完全二叉树。

### 核心代码（C++）
```cpp
#include <queue>
using namespace std;

bool isCompleteTree(BiTree root) {
    if (root == NULL) {
        return true;  // 空树是完全二叉树
    }
    queue<BiTnode*> q;
    q.push(root);
    bool flag = false;  // 标记是否遇到空节点

    while (!q.empty()) {
        BiTnode *node = q.front();
        q.pop();

        if (node == NULL) {
            flag = true;  // 遇到空节点，标记为true
        } else {
            if (flag) {
                return false;  // 空节点后出现非空节点，不符合
            }
            // 左右子节点无论是否为空都入队
            q.push(node->lchild);
            q.push(node->rchild);
        }
    }
    return true;
}
```

---

## 十、查找值为x的节点

### 题目描述
给定二叉树和值x，查找第一个值为x的节点并返回指针，不存在则返回NULL。

### 整体思路（递归法）
1. 前序遍历逻辑：先检查当前节点，再递归左、右子树；
2. 找到目标节点立即返回，避免冗余遍历。

### 核心代码（递归法，C++）
```cpp
BiTree findNode(BiTree root, int x) {
    // 终止条件：树空或找到目标节点
    if (root == NULL || root->data == x) {
        return root;
    }
    // 先递归查找左子树
    BiTree leftResult = findNode(root->lchild, x);
    if (leftResult != NULL) {
        return leftResult;  // 左子树找到，返回结果
    }
    // 左子树未找到，查找右子树
    return findNode(root->rchild, x);
}
```

### 整体思路（层序遍历法）
1. 层序遍历逐个检查节点，找到目标值立即返回；
2. 适合目标节点靠近根的场景，效率更高。

### 核心代码（层序遍历法，C++）
```cpp
#include <queue>
using namespace std;

BiTree findNode(BiTree root, int x) {
    if (root == NULL) {
        return NULL;  // 树空返回NULL
    }
    queue<BiTnode*> q;
    q.push(root);

    while (!q.empty()) {
        BiTnode *node = q.front();
        q.pop();

        if (node->data == x) {
            return node;  // 找到目标节点，返回指针
        }
        // 左右子节点入队继续查找
        if (node->lchild != NULL) {
            q.push(node->lchild);
        }
        if (node->rchild != NULL) {
            q.push(node->rchild);
        }
    }
    return NULL;  // 未找到
}
```

---

## 十一、判断两棵树是否结构相同

### 题目描述
给定两棵二叉树，判断它们的结构是否完全相同（节点存在性相同，数据域可不同）。

### 整体思路
1. 递归比较：两树均空→相同；仅一树空→不同；
2. 两树均非空时，递归比较左、右子树结构，需均相同才判定结构一致。

### 核心代码（C++）
```cpp
bool isSameStructure(BiTree T1, BiTree T2) {
    // 两树均空，结构相同
    if (T1 == NULL && T2 == NULL) {
        return true;
    }
    // 仅一树空，结构不同
    if (T1 == NULL || T2 == NULL) {
        return false;
    }
    // 递归比较左右子树结构
    return isSameStructure(T1->lchild, T2->lchild) && 
           isSameStructure(T1->rchild, T2->rchild);
}
```

---

## 十二、判断二叉树是否为平衡二叉树（AVL树）

### 题目描述
判断给定二叉树是否为平衡二叉树（定义：每个节点的平衡因子绝对值≤1，平衡因子=左子树高度-右子树高度）。

### 整体思路
1. 递归计算节点高度的同时检查平衡状态；
2. 若子树不平衡返回-1标记，当前节点平衡因子超标也返回-1，最终通过返回值判断是否平衡。

### 核心代码（C++）
```cpp
// 辅助函数：计算高度并检查平衡，不平衡返回-1
int isBalancedHelper(BiTree root) {
    if (root == NULL) {
        return 0;  // 空树高度为0，平衡
    }
    // 计算左子树高度，若不平衡直接返回-1
    int leftHeight = isBalancedHelper(root->lchild);
    if (leftHeight == -1) {
        return -1;
    }
    // 计算右子树高度，若不平衡直接返回-1
    int rightHeight = isBalancedHelper(root->rchild);
    if (rightHeight == -1) {
        return -1;
    }
    // 平衡因子超标，返回-1
    if (abs(leftHeight - rightHeight) > 1) {
        return -1;
    }
    // 返回当前节点高度（左右子树最大高度+1）
    return max(leftHeight, rightHeight) + 1;
}

// 主函数：判断是否为平衡二叉树
bool isAVLTree(BiTree root) {
    return isBalancedHelper(root) != -1;
}
```

---

## 十三、统计二叉树中度为1的节点

### 题目描述
给定二叉树，统计度为1的节点数（度为1：只有左子节点或只有右子节点）。

### 整体思路
1. 递归遍历所有节点，判断节点的子节点存在情况；
2. 度为1则计数+1，累计左右子树的统计结果。

### 核心代码（C++）
```cpp
int countDegreeOneNodes(BiTree root) {
    if (root == NULL) {
        return 0;  // 空树无节点
    }
    // 标记左右子节点是否存在
    bool hasLeft = (root->lchild != NULL);
    bool hasRight = (root->rchild != NULL);
    // 度为1的条件：只有左或只有右子节点（异或）
    if (hasLeft != hasRight) {
        // 当前节点度为1，累加左右子树结果
        return 1 + countDegreeOneNodes(root->lchild) + countDegreeOneNodes(root->rchild);
    } else {
        // 度不为1，直接返回左右子树结果
        return countDegreeOneNodes(root->lchild) + countDegreeOneNodes(root->rchild);
    }
}
```

---

## 十四、求根节点到叶节点的最长路径长度

### 题目描述
给定二叉树，返回根节点到叶节点的最长路径长度（路径长度=路径上的节点数）。

### 整体思路（递归法）
1. 递归计算左右子树最长路径长度；
2. 当前节点最长路径=左右子树最大长度+1（包含当前节点）。

### 核心代码（递归法，C++）
```cpp
int treeHeight(BiTree root) {
    if (root == NULL) {
        return 0;  // 空节点路径长度为0
    }
    // 递归计算左右子树最长路径
    int leftHeight = treeHeight(root->lchild);
    int rightHeight = treeHeight(root->rchild);
    // 当前节点最长路径=最大子树长度+1
    return max(leftHeight, rightHeight) + 1;
}
```

### 整体思路（层序遍历法）
1. 层序遍历，每遍历完一层路径长度+1；
2. 遍历结束时的层数即为最长路径长度。

### 核心代码（层序遍历法，C++）
```cpp
#include <queue>
using namespace std;

int treeHeight(BiTree root) {
    if (root == NULL) {
        return 0;  // 空树路径长度为0
    }
    queue<BiTnode*> q;
    q.push(root);
    int height = 0;  // 记录路径长度

    while (!q.empty()) {
        int levelSize = q.size();  // 当前层节点数
        height++;  // 遍历完一层，长度+1
        // 处理当前层，将下一层节点入队
        for (int i = 0; i < levelSize; i++) {
            BiTnode *node = q.front();
            q.pop();

            if (node->lchild != NULL) {
                q.push(node->lchild);
            }
            if (node->rchild != NULL) {
                q.push(node->rchild);
            }
        }
    }
    return height;
}
```

---

## 十五、计算二叉树的带权路径长度（WPL）（2014年408统考真题）

### 题目描述
二叉树的WPL是所有叶节点的带权路径长度之和（带权路径长度=路径长度×节点权值，路径长度=根到叶节点的边数）。给定叶节点含权值的二叉树，求其WPL。

### 整体思路
1. 前序遍历查找叶节点，递归过程中记录当前路径长度；
2. 叶节点计算权值×路径长度，非叶节点递归左右子树，累计结果。

### 核心代码（C++）
```cpp
// 含权值的二叉树节点定义
typedef struct BiTnode {
    int weight;             // 权值域（叶节点有效）
    struct BiTnode *lchild; // 左孩子指针
    struct BiTnode *rchild; // 右孩子指针
} BiTnode, *BiTree;

// 辅助函数：len为当前节点到根的路径长度
int calculateWPL(BiTree T, int len) {
    if (T == NULL) {
        return 0;  // 空节点无贡献
    }
    // 叶节点：返回权值×路径长度
    if (T->lchild == NULL && T->rchild == NULL) {
        return T->weight * len;
    }
    // 非叶节点：递归计算左右子树WPL，路径长度+1
    return calculateWPL(T->lchild, len + 1) + calculateWPL(T->rchild, len + 1);
}

// 主函数：根节点路径长度初始为0
int getWPL(BiTree root) {
    return calculateWPL(root, 0);
}
```

---

## 十六、表达式树转换为中缀表达式（2017年408统考真题）

### 题目描述
将表达式树（二叉树）转换为等价的中缀表达式，用括号反映操作符计算次序（叶节点为操作数，非叶节点为操作符）。

### 整体思路
1. 中序遍历表达式树（左→根→右），契合中缀表达式逻辑；
2. 非根节点的操作符需添加括号，叶节点（操作数）无需括号，避免冗余。

### 核心代码（C++）
```cpp
// 表达式树节点定义
typedef struct node {
    char data[10];  // 存储操作数（如"a"）或操作符（如"+"）
    struct node *left;  // 左孩子指针
    struct node *right; // 右孩子指针
} BTree;

// 辅助函数：deep为节点深度（根节点深度=1）
void BtreeToExp(BTree *root, int deep) {
    if (root == NULL) {
        return;  // 空节点返回
    }
    // 叶节点（操作数）：直接输出
    if (root->left == NULL && root->right == NULL) {
        printf("%s", root->data);
        return;
    }
    // 非根节点的操作符：添加左括号
    if (deep > 1) {
        printf("(");
    }
    // 中序遍历：左子树→操作符→右子树
    BtreeToExp(root->left, deep + 1);
    printf("%s", root->data);
    BtreeToExp(root->right, deep + 1);
    // 非根节点的操作符：添加右括号
    if (deep > 1) {
        printf(")");
    }
}

// 主函数：调用辅助函数，根节点深度=1
void BtreeToInfix(BTree *root) {
    BtreeToExp(root, 1);
}
```

---

## 十七、判断顺序存储的二叉树是否为二叉搜索树（2022年408统考真题）

### 题目描述
二叉树采用顺序存储（数组SqBiTNode，不存在节点用-1表示），节点值为正整数。判断是否为二叉搜索树（BST：任意节点值>左子树所有节点，<右子树所有节点）。

### 整体思路（中序遍历法）
1. BST的中序遍历序列为升序，以此为判断依据；
2. 顺序存储节点索引规则：当前节点k的左孩子=2k+1，右孩子=2k+2；
3. 递归中序遍历，记录当前最大值，每个节点值需大于该最大值（保证升序）。

### 核心代码（C++）
```cpp
// 顺序存储二叉树结构定义
#define MAX_SIZE 100  // 假设最大容量为100
typedef struct {
    int SqBiTNode[MAX_SIZE];  // 存储节点值，-1表示节点不存在
    int ElemNum;              // 实际节点个数
} SqBiTree;

// 递归中序遍历判断BST：max0为当前遍历的最大值（指针传递）
bool isBST(SqBiTree bt, int k, int *max0) {
    int n = bt.ElemNum;
    // 当前节点存在（索引有效且值≠-1）
    if (k < n && bt.SqBiTNode[k] != -1) {
        // 递归遍历左子树
        if (!isBST(bt, k * 2 + 1, max0)) {
            return false;
        }
        // 当前节点值≤max0，不满足升序，不是BST
        if (bt.SqBiTNode[k] <= (*max0)) {
            return false;
        }
        *max0 = bt.SqBiTNode[k];  // 更新最大值
        // 递归遍历右子树
        if (!isBST(bt, k * 2 + 2, max0)) {
            return false;
        }
    }
    return true;
}

// 调用示例：
// SqBiTree bt;  // 已初始化的顺序存储二叉树
// int max0 = -1;  // 初始最大值设为-1（节点值均为正整数）
// bool result = isBST(bt, 0, &max0);
```

### 整体思路（逆向遍历法）
1. 从最后一个叶节点向前遍历，记录每个节点的左右子树最值；
2. 左孩子需满足双亲值>左子树最大值，右孩子需满足双亲值<右子树最小值。

### 核心代码（C++）
```cpp
bool isBST(SqBiTree bt) {
    if (bt.ElemNum == 0) {
        return true;  // 空树是BST
    }
    // 存储每个节点的左子树最小值、右子树最大值
    int *pmin = (int*)malloc(sizeof(int) * bt.ElemNum);
    int *pmax = (int*)malloc(sizeof(int) * bt.ElemNum);

    // 初始化：每个节点的最值为自身值
    for (int k = 0; k < bt.ElemNum; k++) {
        pmin[k] = pmax[k] = bt.SqBiTNode[k];
    }

    // 从最后一个叶节点向前遍历（跳过根节点，k从1开始）
    for (int k = bt.ElemNum - 1; k > 0; k--) {
        if (bt.SqBiTNode[k] == -1) {
            continue;  // 节点不存在，跳过
        }
        int m = (k - 1) / 2;  // 双亲节点索引
        if (k % 2 == 1) {  // 当前节点是左孩子（索引为奇数）
            // 双亲值需大于左子树最大值
            if (bt.SqBiTNode[m] <= pmax[k]) {
                free(pmin);
                free(pmax);
                return false;
            }
            // 更新双亲节点的最小值（左子树最小值）
            pmin[m] = pmin[k];
        } else {  // 当前节点是右孩子（索引为偶数）
            // 双亲值需小于右子树最小值
            if (bt.SqBiTNode[m] >= pmin[k]) {
                free(pmin);
                free(pmax);
                return false;
            }
            // 更新双亲节点的最大值（右子树最大值）
            pmax[m] = pmax[k];
        }
    }

    free(pmin);
    free(pmax);
    return true;
}
```

---
