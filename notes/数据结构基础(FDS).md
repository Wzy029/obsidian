# 程序分析 algorithm analysis

## 空间复杂度
$S(n)$：实际空间使用量
$O(f(n))$：最大空间使用量
$Ω(f(n))$：最少空间使用量
$Θ(f(n))$：如果$O(f(n)) = Ω(f(n))$（上界=下界），则空间就是$f(n)$

| 情形        | 空间复杂度    |
| --------- | -------- |
| 常量变量      | $O(1)$   |
| 长度为n的数组   | $O(n)$   |
| 二维数组      | $O(n^2)$ |
| 递归深度为n    | $O(n)$ 栈 |
| 递归+每层额外数组 | $more$   |
## 时间复杂度
$T(n)$：实际操作次数
$O(f(n))$：最大时间使用量
$Ω(f(n))$：最少时间使用量
$Θ(f(n))$：如果$O(f(n)) = Ω(f(n))$（上界=下界），则时间就是$f(n)$


---
# 线性表 linear list
## 顺序表 sequential list
	数组
## 链表 linked list
### 单向链表 singly linked list
#### Data structure
```c
typedef struct node* Node;
struct node{
	int data;
	Node next;
};
```
#### Creation
```c
Node create(int data){
	Node newnode = (Node)malloc(sizeof(struct node));
	newnode->data = data;
	newnode->next = NULL;
	return newnode;
}
```

#### Insertion
```c
Node insert(Node head, int data, int target){
	Node newnode = create(data);
	if(target == 0 || head == NULL){
		newnode->next = head;
		return newnode;
	}
	
	Node temp = head;
	int i = 0;
	while(i < target-1 && temp->next != NULL){
		temp = temp->next;
		i++;
	}

	newnode->next = temp->next;
	temp->next = newnode;

	return head;
}
```

#### Deletion
```c
Node Delete(Node head, int target){
	if(head == NULL){
		return head;
	}

	if(target == 0){
		Node temp = head->next;
		head->next = NULL;
		free(head);
		return temp;	
	}

	Node prev = head;
	int i = 0;
	while(i < target-1 && prev != NULL){
		prev = prev->next;
		i++;
	}

	if(prev == NULL || prev->next == NULL){
		return head;
	}

	Node deletenode = prev->next;
	prev->next = deletenode->next;
	deletenode->next = NULL;

	free(deletenode);
	return head;
}
```
### 双向链表 doubly linked list
#### Data structure
```c
typedef struct node* Node;
struct node{
	int data;
	Node next;
	Node prev;
};
```

#### Creation
```c
Node create(int data){
	Node newnode = (Node)malloc(sizeof(struct node));
	newnode->data = data;
	newnode->next = NULL;
	newnode->prev = NULL;
	return newnode;
}
```

#### Insertion
```c
Node insert(Node head, int data, int target){
	Node newnode = create(data);
	if(target == 0 || head == NULL){
		newnode->next = head;
		if(head != NULL)
			head->prev = newnode;
		return newnode;
	}
	
	Node temp = head;
	int i = 0;
	while(i < target-1 && temp->next != NULL){
		temp = temp->next;
		i++;
	}

	newnode->next = temp->next;
	if(temp->next != NULL)
		temp->next->prev = newnode;
	newnode->prev = temp;
	temp->next = newnode;

	return head;
}
```

#### Deletion
```c
Node Delete(Node head, int target){
	if(head == NULL){
		return head;
	}

	if(target == 0){
		Node temp = head->next;
		if(temp != NULL)
			temp->prev = NULL;
		head->next = NULL;
		free(head);
		return temp;	
	}

	Node prev = head;
	int i = 0;
	while(i < target-1 && prev != NULL){
		prev = prev->next;
		i++;
	}

	if(prev == NULL || prev->next == NULL){
		return head;
	}

	Node deletenode = prev->next;
	prev->next = deletenode->next;
	if(deletenode->next != NULL)
		deletenode->next->prev = prev;
	deletenode->next = NULL;
	deletenode->prev = NULL;

	free(deletenode);
	return head;
}
```

##### \*双向链表，哈希数组优化deletion
```c
Node hash[MAXnumber];
```

```c
void insertHash(Node newnode){
	if(newnode == NULL)
		return;

	hash[newnode->data] = newnode;
}
```

```c
void deleteHash(Node deletenode){
	if(deletenode == NULL)
		return ;

	hash[deletenode->data] = NULL;
}
```

```c
Node findHash(int data){
	return hash[data];
}
```

```c
Node Delete(Node head, int data){
	Node deletenode = findHash(data);
	if(deletenode == NULL)
		return head;

	if(deletenode->prev == NULL){
		head = deletenode->next;
		if(head != NULL)
			head->prev = NULL;
		free(deletenode);
		return head;
	}

	deletenode->prev->next = deletenode->next;
	if(deletenode->next != NULL)
		deletenode->next->prev = deletenode->prev;
	free(deletenode);
	return head;
}
```


> [!important] 
> 每次插入后都要调用insertHash，每次删除后都要调用deleteHash

### 循环链表 circular linked list
#### Data structure
```c
typedef struct node* Node;
struct node{
	int data;
	Node next;
};
```

#### Creation
```c
Node create(int data){
	Node newnode = (Node)malloc(sizeof(struct node));
	newnode->data = data;
	newnode->next = NULL;
	return newnode;
}
```

#### Insertion
```c
Node insert(Node head, int data, int target){
	Node newnode = create(data);
	if(head == NULL){
		newnode->next = newnode;
		return newnode;
	}

	if(target == 0){
		Node temp = head;
		while(temp->next != head){
			temp = temp->next;
		}
		temp->next = newnode;
		newnode->next = head;
		return newnode;
	}
	
	Node temp = head;
	int i = 0;
	while(i < target-1 && temp->next != head){
		temp = temp->next;
		i++;
	}

	newnode->next = temp->next;
	temp->next = newnode;

	return head;
}
```

#### Deletion
```c
Node Delete(Node head, int target){
	if(head == NULL){
		return head;
	}

	if(target == 0){
		if(head->next == head){
			free(head);
			return NULL;
		}
		
		Node temp = head;
		while(temp->next != head){
			temp = temp->next;
		}
		temp->next = head->next;
		head->next = NULL;
		free(head);
		return temp->next;
	}

	Node prev = head;
	int i = 0;
	while(i < target-1 && prev != head){
		prev = prev->next;
		i++;
	}

	if(prev->next == head){
		return head;
	}

	Node deletenode = prev->next;
	prev->next = deletenode->next;
	deletenode->next = NULL;

	free(deletenode);
	return head;
}
```

### 循环双向链表 circular doubly linked list
	Omitted
## 栈 stack
### 链表实现 linked list
#### Data structure
```c
typedef struct node* Node;
struct node{
	int data;
	Node next;
};
typedef struct{
	Node top;
}Stack;
```

#### Stackbuilding
```c
void stackbuilding(Stack *s){
	s->top = NULL; //初始化栈顶指针为0
}
```
#### isEmpty
```c
int isEmpty(Stack *s){
	return s->top == NULL;
}
```
#### push入栈
	插入到头部
```c
void push(Stack *s, int data){
	Node newnode = create(data);
	newnode->next = s->top;
	s->top = newnode;
}
```
#### pop出栈
```c
int pop(Stack *s){
	if(isEmpty(s)){
		return -1;
	}
	Node temp = s->top;
	int data = temp->data;
	s->top = s->top->next;
	
	free(temp);
	return data;
}
```

### 数组实现 sequential list
#### Data structure
```c
#define MAX 100

typedef struct{
	int data[MAX];
	int top;
}Stack;
```
#### Stackbuilding
```c
void stackbuilding(Stack *s){
	s->top = -1;
}
```
#### isEmpty
```c
int isEmpty(Stack *s){
	return s->top == -1;
}
```
#### isFull
```c
int isFull(Stack *s){
	return s->top == MAX-1;
}
```
#### push
```c
int push(Stack *s, int data){
	if(isFull(s)){
		return 0;
	}
	s->data[++(s->top)] = data;
	return 1;
}
```
#### pop
```c
int pop(Stack *s){
	if(isEmpty(s))
		return 0;
	int data =  s->data[s->top];
	s->top--;
	return data;
}
```

## 队列 queue
### 数组实现 sequential list
#### Data structure
```c
#define MAX 100

typedef struct{
	int data[MAX];
	int front; //队头索引
	int rear;  //队尾元素的下一个索引
}Queue;
```
#### queuebuilding
```c
void queuebuilding(Queue *q){
	q->front = 0;
	q->rear = 0;
}
```
#### isEmpty
```c
int isEmpty(Queue* q){
	return q->front == q->rear;
}
```
#### isFull
```c
int isFull(Queue* q){
	return (q->rear + 1) % MAX == q->front;
}
```
#### Enqueue入队
```c
int enqueue(Queue* q, int data){
	if(isFull(q)){
		return 0;
	}
	q->data[q->rear] = data;
	q->rear = (q->rear + 1) % MAX;//循环效果
	return 1;
}
```
#### Dequeue出队
```c
int dequeue(Queue* q){
	if(isEmpty(q)){
		return 0;
	}
	int data = q->data[q->front];
	q->front = (q->front + 1) % MAX;
	return data;
}
```

---
# 树 tree
## 二叉树

### Data structure
```c
typedef struct node* Node;
struct node{
	int data;
	Node Left;
	Node Right;
};
```
#### preorder 中左右
```c
void preorder(Node root){
	if(root == NULL){
		return;
	}

	printf("%d", root->data);
	preorder(root->Left);
	preorder(root->Right);
}
```
#### inorder 左中右
```c
void inorder(Node root){
	if(root == NULL){
		return;
	}
	inorder(root->Left);
	printf("%d", root->data);
	inorder(root->Right);
}
```
#### postorder 左右中
```c
void postorder(Node root){
	if(root == NULL){
		return;
	}
	postorder(root->Left);
	posrorder(root->Right);
	printf("%d", root->data);
}
```
#### levelorder 层序遍历
```c
void levelorder(Node root){
	if(root == NULL) return;

	Queue q;
	queuebuilding(&q);
	enqueue(&q, root);

	while(!isEmpty(q)){
		Node temp = dequeue(&q);
		printf("%d", temp->data);
		if(temp->Left)  enqueue(&q, temp->Left);
		if(temp->Right) enqueue(&q, temp->Right);
	}
	
}
```
#### \* Zizagging
```c
int store[MAX1][MAX2];
int size[MAX1];

void zizagging(Node root){
	if(root == NULL) return;

	int level = 0;
	int front = 0;
	int rear  = 0;
	Node queue[MAXN];

	queue[rear++] = root;
	while(rear > front){
		size[level] = rear - front;
		int i = 0;
		while(i < size[level]){
			Node temp = queue[front];
			store[level][i++] = queue[front++]->data;
			if(temp->Left){
				queue[rear++] = temp->Left;
			}
			if(temp->Right){
				queue[rear++] = temp->Right;
			}
		}
		level++;
	}
	for(int i = 0; i < level; i++){
		if(i % 2){
			printLtoR(store[i]);
		}else{
			printRtoL(store[i]);
		}
	}
}
```

### BST--Binary Search Tree
	左子节点<父节点，右子节点≥父节点
```c
typedef struct node* Node;
struct node{
	int data;
	Node left;
	Node right;
}

Node find(int data, Node root){
	if(root == NULL) return NULL;

	if(root->data == data){
		return root;
	}else if(root->data > data){
		return find(data, root->left);
	}else{
		return find(data, root->right);
	}
}
```

---
# 并查集 Disjoint set
	集合中的元素有部分存在联系

```c
int par[MAX] = {0};
int rank[MAX] = {0};
```

## Initialise
```c
void initial(int N){
	for(int i = 0; i < N; i++){
		par[i] = i; //初始化，自己的父节点是自己
		rank[i] = 1; // 一层
	}
}
```
## Find
```c
int find(x){
	if(par[x] == x)
		return x;
	else
		return find(par[x]);
}
```
## Union
```c
void union(int x, int y){
	int ix = find(x);
	int iy = find(y);
	if(ix == iy) return;
	if(rank[ix] > rank[iy]){
		par[iy] = par[ix];
	}else{
		par[ix] = par[iy];
		if(rank[ix] == rank[iy])  rank[iy]++;
	}
}
```
## Main
```c
int N;
initial(N);

for(int i = 0; i < N; i++)
	int x, y;
	scanf("%d%d", &x, &y);
	union(x, y);
}
```


---
# 堆 heap
## 最大堆/最小堆（以最大堆为例）
### Data structure
```c
typedef struct heap{
	int arr[MAX + 1];
	int capacity;
}Heap;
```
#### MaxHeapify
```c
void MaxHeapify(Heap* H, int i){
	int largest = i;
	int left = 2 * i;
	int right = 2 * i + 1;

	if(left <= H->capacity && H->arr[left] > H->arr[largest])
		largest = left;
	if(right <= H->capacity && H->arr[right] > H->arr[largest])
		largest = right;

	if(largest != i){
		swap(&(H->arr[i]), &(H->arr[largest]));
		MaxHeapify(H, largest); // 递归向下
	}
}
```
#### HeapPush
```c
void HeapPush(int data, Heap* H){
	int i = ++((*H)->capacity);
	(*H)->arr[i] = data;
	while(i > 1 && (*H)->arr[i] > (*H)->arr[i / 2]){ //data大于父节点，即要往上翻
		swap(...) // 父结点下移
		i /= 2;
	}
}
```
#### HeapPop
```c
void HeapPop(Heap H){
	if((*H)->capacity == 0) return;
	(*H)->arr[1] = (*H)->arr[(*H)->capacity];
	(*H)->capacity--;
	MaxHeapify(&H, 1);
}
```
#### HeapBuilding
```c
void HeapBuilding(int arr[], int N){
	for(int i = 1; i <= N; i++)
		H->arr[i] = arr[i];
		
	H->capacity = N;
	
	for(int i = N/2; i >= 1; i--)
		MaxHeapify(H, i);
}
```
其实一直push也可以

---

---
# 图 graph
## Data structure & Graph Building
### Adjacency Matrix
	（注意是0-based还是1-based）
	Omitted
### Adjacency List (适用于稀疏图)
```c
typedef struct AdjVNode *PtrToAdjVNode; 
struct AdjVNode{
    int AdjV;
    int weight;
    PtrToAdjVNode Next;
};  //单个节点

typedef struct Vnode{
    PtrToAdjVNode FirstEdge;
} AdjList[MaxVertexNum]; //存储每个节点的第一个指向的节点

typedef struct GNode *LGraph;
struct GNode{  
    int Nv; //节点数量
    int Ne; //边数量
    AdjList G; //邻接表数组
};

/*最终形成的效果：
G[0]->1->2
G[1]->3
G[2]->4
...
*/
```
#### addEdge
```c
void insertEdge(LGraph graph, int v1, int v2, int weight){
	PtrToAdjVNode newnode = (PtrToAdjVNode)malloc(sizeof(struct AdjVNode));
	newnode->AdjV = v2;
	newnode->weight = weight;
	newnode->Next = graph->G[v1].FirstEdge; // 头插
	graph->G[v1].FirstEdge = newnode;
}
```

#### Building
```c
// 0-based
int Nv, Ne;
scanf("%d%d", &Nv, &Ne);

// 空间分配，图的初始化
LGraph graph = (LGraph)malloc(sizeof(struct GNode));
for (int i = 0; i < Nv; i++) {
    graph->G[i].FirstEdge = NULL;
}
graph->Nv = Nv;
graph->Ne = Ne;

// 插入边
for(int i=0; i<Ne; i++){
	int v1, v2, weight;
	scanf("%d%d%d", &v1, &v2, &weight);
	insertEdge(graph, v1, v2, weight);
}
```

## BFS 广度优先搜索：最短路径（无权图）
### Data Structure
```c
int graph[MAX][MAX]; // 邻接矩阵
int dist[MAX] = {0}; // 最短距离

int queue[MAX];      // 队列
int front = 0;       // 队列指针
int rear = 0;

int prev[MAX];       // 记录最短路径
```
### Queue Functions
#### enqueue
```c
void enqueue(int x){
	queue[rear++] = x;
}
```
#### dequeue
```c
void dequeue(){
	return queue[front++];
}
```
#### isEmpty
```c
int isEmpty(){
	return front == rear;
}
```
### BFS
```c
void bfs(int n, int start){
	memset(dist, -1, sizeof(dist));
	memset(prev, -1, sizeof(dist)); // 初始化为-1
	front = rear = 0;
	
	dist[start] = 0; // 和自己的距离是0
	enqueue(start);  // 源点入队

	while(!isEmpty()){
		int u = dequeue(); // 出队
		for(int i = 0; i < n; i++){
			if(graph[u][i] && dist[i] == -1){ // 如果有边，且未访问过
				dist[i] = dist[u] + 1;        // 到这里的距离就是前一个+1
				prev[i] = u;                  // i的前一个值是u
				enqueue(i);                   // i入队
			}
		}
	}
}
```
### PrintPath
```c
void printpath(int end){
	if(prev[end] == -1){
		printf("%d", end);
		return;
	}
	printpath(prev[end]); // 回溯
	printf("%d", end);
}
```

## Dijkstra：最短路径（带权图）
### Data Structure (Min_heap)
```c
// 堆
typedef struct{
	int node;
	int dist;
}heapNode;

heapNode heap[MAX + 1];
int heapsize = 0;
```
### Heap Functions
#### Swap
```c
void swap(int i, int j){
	heapNode temp = heap[i];
	heap[i] = heap[j];
	heap[j] = temp;
}
```
#### Push
```c
void push(int node, int dist){
	int i = heapsize++;
	heap[i].node = node;
	heap[i].dist = dist;
	while(i > 0 && heap[i].dist < heap[(i-1)/2].dist){
		swap(i, (i-1)/2);
		i = (i-1)/2;
	}
}
```
#### Pop
```c
heapNode pop(){
	heapNode top = heap[1];
	heap[1] = heap[heapsize--];
	int i = 1;
	while(1){
		int left = 2*i;
		int right = 2*i+1;
		int smallest = i;
		if(left <= heapsize && heap[left].dist < heap[smallest].dist)
			smallest = left;
		if(right <= heapsize && heap[right].dist < heap[smallest].dist)
			smallest = right;
		if(smallest == i) break;
		swap(i, smallest);
		i = smallest;
	}
	return top;
}
```
### Data Structure (dijkstra)
```c
// 邻接表
typedef struct edge* Edge
struct edge{
	int to, weight; // v2,权重
	Edge next;
};

Edge adj[MAX]; // 邻接表

int dist[MAX];
int visited[MAX];
int prev[MAX];
```
### Dijkstra
```c
void dijkstra(int n, int start){
	// initialise
	for(int i = 0; i < n; i++){
		dist[i] = INF;  // 无限远
		visited[i] = 0; // 未经过
		prev[i] = -1;   // 无路径
	}
	dist[start] = 0;
	push(start, dist[start]);

	while(heapsize > 0){
		heapNode h = pop();  // 弹出最小值
		int u = h.node;      // 最小值u
		if(visited[u]) continue;  // 访问过，跳过
		visited[u] = 1;      // 标记为访问

		for(Edge e = adj[u]; e != NULL ; e = e->next){
			int v = e->to, w = e->weight;
			if(!visited[v] && dist[u] + w < dist[v]){
				dist[v] = dist[u] + w;
				prev[v] = u;
				push(v, dist[v]);
			}
		}
	}
}
```
### PrintPath
	同上
## Ford-Fulkerson的BFS实现版本 ：最大流问题
### Data Structure
```c
int capacity[MAX][MAX] // 容量图
int flow[MAX][MAX]     // 当前已使用的流量
int parent[MAX]        // 记录增广路径

int queue[MAX]         // BFS队列
int front = 0;
int rear = 0;
```
### BFS
#### Queue Function
	Omitted

#### BFS Checking
	和前面的相似，主要改动是line 10中条件变为capacity[u][v] - flow[u][v] > 0，即还有剩余流量
```c
int bfs(int s, int t){
	int visited[MAX] = {0};
	enqueue(s);
	visited[s] = 1;
	parent[s] = -1;

	while(!isEmpty()){
		int u = dequeue();
		for(int v = 0; v < n; v++){
			if(!visited[v] && capacity[u][v] - flow[u][v] > 0){
				enqueue(v);
				parent[v] = u;
				visited[v] = 1;
				if(v == t) return 1;
			}
		}
	}
	return 0;
}
```
#### Main Procedure
```c
int mainFunction(int s, int t){
	int maxFlow = 0; // 累加记录最大流量

	while(bfs(s, t)){  // 能到达终点的情况下
	// 对于每一条可能的路径
		int pathFlow = INF;
		// 溯源
		for(int v = t; v != s; v = parent[v]){
			int u = parent[v];
			pathFlow = pathFlow < (capacity[u][v] - flow[u][v]) ?
			           pathFlow : (capacity[u][v] - flow[u][v]);
		}
		// 得到这条路上的最小流量

		for(int v = t; v != s; v = parent[v]){
			int u = parent[v];
			flow[u][v] += pathFlow;  // 通过了pathFlow这么多的流量
			flow[v][u] -= pathFlow;  // 更新反向边
		}

		maxFlow += pathFlow;  // 加上这次的流量
	}
	return maxFlow;
}
```

## 最小生成树 Minimum Spanning Tree：Kruskal
	并查集
### Data Structure
```c
typedef struct{
	int u, v, w;
}Edge;

Edge edges[MAXE];
int parent[MAXN]; // 并查集用的父节点
int rank[MAXN];
int n, e;
```
### Union-Find
```c
void initial(int n){
	for(int i=0; i<n; i++){
		parent[i] = i;
		rank[i] = 1;
	}
}

int find(x){
	if(x == parent[x]) return x;
	return find(parent[x]);
}

void Union(int x, int y){
	int ix = find(x);
	int iy = find(y);
	if(ix == iy) return;
	if(rank[ix] < rank[iy]){
		parent[ix] = parent[iy];
	}else{
		parent[iy] = parent[ix];
		if(rank[ix] == rank[iy]){
			rank[ix]++;
		}
	}
}
```
### Kruskal
```c
int kruskal(int n){
	initial(n);

	int countEdge = 0; // 记录经过的边条数
	int totalWeight = 0; // 总权重

	qsort(edges, e, sizeof(Edge), compare); // 升序排列

	for(itn i = 0; i < m; i++){
		int u = edges[i].u;
		int v = edges[i].v;
		int w = edges[i].w;

		if(find(u) != find(v)){ // 如果不在生成树里（避免环）
			Union(u, v);
			totalWeight += w;
			countEdge++;

			if(countEdge == n-1)
				break; // 构成生成树
		}
	}
	return totalWeight;
}
```

## DFS 深度优先搜索

### 寻找强连通分量/割点/桥/双连通分量：Tarjan算法
`articulation point`割点，删除后至少有两个连通分量
`biconnected graph`双连通图，没有割点
`biconnected component`最大双连通子图

#### 找强连通分量(SCC)
##### Data Structure
```c
int graph[MAX][MAX];  // 邻接矩阵
int dfn[MAX];         // 时间戳：第一次访问节点的时间
int low[MAX];         // 最小能回溯到的时间戳
int inStack[MAX];     // 是否在栈中
int stack[MAX], top;  // 栈
int timestamp = 0;    // 时间戳计数器
```

```c
void TarjanSCC(int u, int n){
	dfn[u] = low[u] = ++timestamp; // 访问
	stack[++top] = u; // 入栈
	inStack[u] = 1;   // 在栈里

	for(int v = 0; v < n; v++){
		if(graph[u][v]){
			if(!dfn[v]){
				TarjanSCC(v, n);  // 如果之前没有访问过，访问这个节点
				low[u] = min(low[u], low[v]);
			}else if(inStack[v]){
				low[u] = min(low[u], dfn[v]); // 如果在栈中，更新low[u]
			}
		}
	}
	if(low[u] == dfn[u]){  // 强连通分量的根
		print(stack, u);   // 弹出直到u
	}
}
```

```c
...
for(int i = 0; i < n; i++){
	if(!dfn[i]){
		TarjanSCC(i, n);
	}
}
...
```
#### 找双连通图(BCC, vertex)
`Tree Edge`树边，第一次访问一个新节点时走过的边；帮助建立DFS树
`Back Edge`回边，连接当前节点到它的祖先节点（非父节点）的边，表示存在回路；用来更新`low`
![[Pasted image 20250522143253.png|575]]
##### Data Structure
```c
int dfn[MAX], low[MAX], time;
int graph[MAX][MAX];
int stack_u[MAX*MAX]; // 边的起点
int stack_v[MAX*MAX]; // 边的终点
int top;              // 边栈顶
int is_cut[MAX];      // 标记割点
```

```c
void TarjanBCCE(int u, int parent, int n){
	dfn[u] = low[u] = ++time;
	int child = 0;  // 子节点数

	for(int v=0; v < n; v++){
		if(!graph[u][v]) continue;

		// 访问所有邻接点
		if(!dfn[v]){ // 还没访问过
			stack_u[top] = u;  // 起点入栈
			stack_v[top++] = v;// 终点入栈

			TarjanBCC(v, u, n);// 对v做搜索
			low[u] = min(low[u], low[v]); // 更新
			child++;

        /* 如果low[v]<dfn[u],就说明v可以通过别的路回到u的祖先节点
           反之则说明是割点*/
			if(low[v] >= dfn[u]){ 
				is_cut[u] = 1;
				print(stack_u, u, stack_v, v);
			}else if(v != parent && dfn[v] < dfn[u]){
				stack_u[top] = u;
				stack_v[top++] = v;
				low[u] = min(low[u], dfn[v])
			}
		}
	}

	if(parent == -1 && child < 2)
		is_cut[u] = 0;
	// 如果根节点的子节点只有1个，则不是割点
}
```
### Euler Circuit (欧拉回路)
	一笔画问题
	全都是偶数度节点
	*是欧拉通路但不是欧拉回路：有且只有两个奇数度节点，且从奇数节点开始

**核心思想：**
1. 从某个节点出发，尽可能“走到底”（每条边只能走一次）。
2. 走到底之后回溯，把这条路径记录下来。
3. 如果在已记录路径上遇到还有没用过的边，从那里再次“走到底”，插入路径中。 
4. 最终得到完整欧拉回路。
#### Data Structure
```c
typedef struct edge* Edge;
struct edge{
	int to;
	int used;
	Edge next;
};

Edge graph[MAX]; // 每个点的邻接链表
int degree[MAX];

int circuit[MAX*MAX];
int circuitID = 0;
```
#### GraphBuilding
```c
void addEdge(int u, int v){
	Edge e1 = (Edge)malloc(sizeof(struct edge));
	e1->to = v;
	e1->used = 0;
	e1->next = graph[u];
	graph[u] = e1;   // 头插
	degree[u]++;

	Edge e2 = (Edge)malloc(sizeof(struct edge));
	e2->to = u;
	e2->used = 0;
	e2->next = graph[v];
	graph[v] = e2;   // 头插
	degree[v]++;
// 添加一条(u,v)实际上要添加两条双向边
}
```
#### DFS
```c
void dfs(int u){
	for(Edge e = graph[u]; e != NULL; e = e->next){
		// 如果没走过
		if(!e->used){
			e->used = 1; // 标记为1

			for(Edge rev = graph[e->to]; rev != NULL; rev = rev->next){
				if(rev->to == u && !rev->used){
					rev->used = 1;
					break;
				}
			}  // 找到反向边并标记

			dfs(e->to); // 递归向下走
		}
	}
	// 这个点的邻边都走过了之后
	circuit[circuitID++] = u; // 加入回路
}
```
#### main
```c
int main(){
	input
	// 判断是否每个点度数都是偶数
	for(int i = 0; i < n; i++){
		if(degree[i] % 2 != 0){
			printf("No Euler circuit\n");
			return 0;
		}
	}

	dfs(0);
	if(circuitID != e+1){
		printf("Not connected");
		return 0;
	}

	for(int i = circuitID-1; i >= 0; i--){
		...
	} // 逆序打印
}
```

### Hamilton Cycle 哈密顿回路
	经过图中每个顶点一次且仅一次，最后回到起点
**思路步骤：**
1. 图的表示：
	- 使用邻接矩阵`graph[i][j]` 表示点 `i` 到点 `j` 是否有边。
2. 状态记录：
	- 使用 `path[]` 数组记录当前访问路径。  
	- 初始设定 `path[0] = 0`，表示从顶点 0 开始。
 3. isSafe 函数：
	- 检查一个节点 `v` 是否能放到当前路径的 `pos` 位置：
    - 是否和上一个点有边；
    - 是否已经访问过。
4. 递归搜索：
	- 尝试每个未访问的节点放到路径中；
	- 如果找到一个合法的路径且最后一点可以回到起点，就返回成功；
	- 否则回溯（撤销决策）继续尝试下一个节点。
#### Data Structure
```c
int graph[MAX][MAX];  // 邻接矩阵
int path[MAX];        // 当前路径
int n;                // 节点数
```
#### isSafe
```c
int isSafe(int v, int pos){
	if(graph[ path[pos - 1] ][v] == 0) return 0;
	// 如果点v与路径的最后一个点之间没有边连接，则不能加入

	for(int i = 0; i < pos; i++){
		if(path[i] == v) return 0; // 如果走过这个点了
	}	
	return 1; // 和最后一个点有边连接，且没走过，即可以加入路径
}
```
#### HamiltonUtil
```c
int hamiltonUtil(int pos){
	if(pos == n){ // 所有点都收录了
		return graph[ path[pos-1] ][path[0]] == 1;
		//  如果最后一个点到起点有边，则构成回路
	}
	
	// 还未收录完，尝试将剩下的点加入路径
	for(int v = 1; v < n; v++){
		if(isSafe(v, pos)){ // 和最后一个点有边连接，且没走过
			path[pos] = v;  // 加入路径中
			if(hamiltonUtil(pos+1)) return 1; // 如果能走到底走完，ok
			path[pos] = -1; // 无法走完，撤销选择，回溯
		}
	}
	return 0;
}
```
#### HamiltonCycle
```c
int hamiltonCycle(){
	for(int i = 0; i < n; i++) path[i] = -1; // 初始化
	path[0] = 0; // 从0开始
	return hamiltonUtil(1);
}
```

---
# 排序 sort
## Insertion Sort 插入排序
	数组，一个一个比较，移动插入

```c
void insertionSort(int arr[], int n){
	int j;
	for(int i = 1; i < n; i++){  // 0当作排好了，所以从1开始排
		int target = arr[i];     // 存好要排的元素
		for(j = i; j > 0; j--){  // 在这之前的元素
			if(arr[j-1] <= target){ 
				break;           // 如果这个元素小于等于目标，说明可以插入了
			}else{
				arr[j] = arr[j-1]; // 如果不是的话，往后移动一格
			}
		}
		arr[j] = target;         // 在最终空出来的地方放入目标
	}
}
```

## Shell Sort 希尔排序
	- 先取一个较大的间隔（gap），将数组分成若干个子序列。
		- 如gap = 5, 则arr[0]和arr[5]比...
	- 对每个子序列使用插入排序。
	- 然后缩小间隔，重复上述过程。
	- 当间隔缩小到1时，进行最后一次插入排序。
```c
void shellSort(int arr[], int n){
	for(int gap = n/2; gap > 0; gap /= 2){ // gap每次减半
		
		for(int i = gap; i < n; i++){
			int temp = arr[i];
			
			// 插入排序
			int j;
			for(j = i; j >= gap; j -= gap){  // 在这之前的元素
				if(arr[j-gap] <= temp){ 
					break;           // 如果这个元素小于等于目标，说明可以插入了
				}else{
					arr[j] = arr[j-gap]; // 如果不是的话，往后移动一格
				}
			}
			arr[j] = temp;         // 在最终空出来的地方放入目标
		}
	}
}
```
## Heap Sort 堆排序
	- 构建一个最大堆（Max-Heap），堆顶元素是最大值。
	- 将堆顶（最大值）与最后一个元素交换，然后将剩余元素重新调整成最大堆。
	- 反复执行上述步骤，直到堆大小变为 1。
#### Heapify (1-based)
```c
void heapify(int arr[], int n, int i){
	int left = 2*i;
	int right = 2*i + 1;
	int largest = i;

	// 找到最大的
	if(left  <= n && arr[left]  > arr[largest]) largest = left;
	if(right <= n && arr[right] > arr[largest]) largest = right;
	
	// 如果的最大的不是父节点, 交换
	if(largest != i){
		int temp = arr[i];
		arr[i] = arr[largest];
		arr[largest] = temp;
		
		// 递归调整受影响的子树
		heapify(arr, n, largest);
	}
}
```
#### HeapSort
```c
void heapSort(int arr[], int n){
	// 建堆，从最后一个非叶子节点开始调整
	for(int i = n/2; i > 0; i--){
		heapify(arr, n, i);
	}
	for(int i = n; i > 1; i--){
		// 把第一个（最大值）和最后一个交换
		int temp = arr[1];
		arr[1] = arr[i];
		arr[i] = temp;
		
		// 调整，把下一个最大值放前面
		heapify(arr, i-1, 1); // i-1：未排序的元素
	}
}
```

## Merge Sort 归并排序
	分治法
	- 分解（Divide）
    把待排序数组从中间分成两半，分别对左半部分和右半部分进行递归排序。
	- 解决（Conquer）
	    当分割到数组只剩一个元素时，数组自然是有序的。
	- 合并（Combine）
	    将两个已经排好序的子数组合并成一个有序数组。
#### Merge
	合并两个有序数组arr[left...middle], arr[middle+1...right]
```c
void merge(int arr[], int left, int middle, int right){
	int n1 = middle - left + 1;
	int n2 = right - middle;

	// 临时数组
	int* L = (int*)malloc(n1 * sizeof(int));
	int* R = (int*)malloc(n2 * sizeof(int));

	// 复制数据到临时数组
	for(int i = 0; i < n1; i++) L[i] = arr[left + 1];
	for(int j = 0; j < n2; j++) R[j] = arr[middle + 1 + j];

	int i = 0, j=0, k = left;
	// 合并两个有序数组
	while(i < n1 && j < n2){
		if(L[i] <= R[j]) arr[k++] = L[i++];
		else             arr[k++] = R[j++];
		// 哪边小就放哪边
	}

	while(i < n1) arr[k++] = L[i++];
	while(j < n2) arr[k++] = R[j++];
	// 把没放完的继续放完（L，R本身都有序）

	free(L);
	free(R);
}
```
#### MergeSort
```c
void mergeSort(int arr[], int left, int right){
	if(left < right){
		int middle = left + (right - 1)/2;   // 找中点, r-1是为了防止溢出
		mergeSort(arr, left, middle);        // 排序左半部分
		mergeSort(arr, middle+1, right);     // 排序右半部分
		merge(arr, left, merge, right);      // 合并两个有序部分
	}
}
```

## Quick Sort 快速排序
	分治法，基本思想：
	1. 选一个“基准值”（pivot），通常是数组的第一个或最后一个元素。
	2. 划分数组：将数组中小于 pivot 的元素移到它左边，大于它的移到右边。
	3. 递归排序：对左边和右边的子数组递归执行相同的操作。
#### pivot
	可以列个式子理解一下，有点难描述

```c
void swap(int* a, int* b){
	int temp = *a;
	*a = *b;
	*b = temp;
}

int partition(int arr[], int low, int high){
	int pivot = arr[high]; // 选最后一个作为pivot
	int i = low - 1;       // i: 小于pivot的元素的最后位置
	
	for(int j = low; j < high; j++){
		if(arr[j] < pivot){
			i++;
			if(i != j) swap(&arr[i], &arr[j]);
		}
	}
	swap(&arr[i+1], &arr[high]);
	return i+1; // 返回pivot的位置
}
```
#### quickSort
```c
void quickSort(int arr[], int low, int high){
	if(low < high){
		int pi = partition(arr, low, high);
		quickSort(arr, low,  pi - 1); // 排左边
		quickSort(arr, pi + 1, high); // 排右边
	}
}
```
#### 调用
```c
quickSort(arr, 0, n - 1);
```
## Bucket Sort 桶排序
- **找到最大值和最小值**（用于确定桶的范围）。
- **创建若干个桶**（可以是数组的数组、链表数组等）。
- **将元素分配到桶中**。
- **对每个桶内部排序**（可使用快速排序、插入排序等）。
- **合并所有桶的元素**，得到排序后的数组。
- 适用于：浮点数、均匀分布整数
### FindMax, FindMin
```c
float findmax(float arr[], int n){
	float max = arr[0];
	for(int i = 0; i < n; i++){
		if(max < arr[i]) max = arr[i];
	}
	return max;
}

float findmin(float arr[], int n){
	float min = arr[0];
	for(int i = 0; i < n; i++){
		if(min < arr[i]) min = arr[i];
	}
	return min;
}
```
### bucketSort
```c
void bucketSort(float arr[], int n){
	float bucket[10][n];
	int size[10] = {0};
	int rear = (int)findmax(arr, n);
	int front = (int)findmin(arr, n);
	
	for(int i = 0; i < n; i++){
		int id =(int) (arr[i] * 10);
		bucket[id][size[id]++] = arr[i];
	}

	for(int i = front; i <= rear; i++){
		if(size[i]){
			insertSort(bucket[i], n);
			print(bucket[i]);
		}
	}
}
```
## Radix Sort 基数排序
- 比如先按个位排，再按十位，再按百位...
### FindMaxID
```c
int findmaxID(int arr[], int n){
	int max = arr[0];
	for(int i = 0; i < n; i++){
		if(max < arr[i]) max = arr[i];
	}
	int id = 0;
	while(max>0){
		max/=10;
		id++;
	}
	return id;
}
```
### radixUnit
```c
void radixUnit(int arr[], int n, int base){
	int radix[10][n];
	int count[10] = {0};
	for(int k = 0; k < n; k++){
		int cmp = arr[k] % base / (base/10);
		radix[cmp][count[cmp]++] = arr[k];
	}

	int u=0;
	for(int i = 0; i < 10; i++){
		if(count[i]){
			for(int k = 0; k < count[i]; k++){
				arr[u++] = radix[i][k];
			}
		}
	}
}
```
### radixSort
```c
void radixSort(int arr[], int n){
	int num = findmaxID(arr, n);
	int base = 10;
	for(int i = 0; i < num; i++){
		radixUnit(arr, n, base);
		base *= 10;
	}
	print(arr);
}
```

---
# Hash 哈希
- T：x的值的个数
- n：已经放进表里的元素数
- identifier density：n/T，放进表中的元素占所有元素的百分比
- loading density λ：n/capacity，表中非空格占所有格的百分比
- collision 冲突：两个不同的数有相同的key，理论上都放在一个格子里
- overflow 溢出：表满了继续放

## 几种常用的处理
### $f(x)\ =\ x\ \%\ TableSize\ /* if\ x\ is\ an\ integer\ */$
	TableSize 最好是prime number

### $f(x)\ =\ \sum x[i]\ \%\ TableSize\ /* if\ x\ is\ a\ string\ */$

### $f(x)\ =\ (x[0]+x[1]*27+x[2]*27^2)\ \%\ TableSize$
	三个字母，总共出现可能的组合有26*26*26=17576种
	但如果tablesize小，冲突会很严重

### $f(x)\ =\ (\sum x[N-i-1]*32^i)\ \%\ TableSize$
	(hashVal << 5) + *x++

## 冲突解决办法
### Separate Chaining 分离链接法
	每个桶是一个链表
	遇到冲突时挂到对应链表
### Open Addressing 开放定址法
#### Linear Probing 线性探测
	f(i) = i
	hash(x), hash(x)+1, hash(x)+2, ...
	如果已被占用就找下一个空位
	👎容易发生主聚集(Primary Clustering)，冲突区域越来越大
#### Quadratic Probing 平方探测
	f(i) = i^2
	hash(x), hash(x)+1, hash(x)+4, hash(x)+9, ...
	至少有一半空位时，新元素一定可以放进去
	👍减少主聚集
	👎仍存在次聚集(Secondary Clustering)，相同初始哈希值将探测同一序列
	
#### Double Hashing 双重哈希
	f(i) = i * hash2(x)
	hash2(x) = R - (x % R), R为小于tableSize的质数
### Rehashing 再哈希
	哈希表过于拥挤或插入失败时
	新建一个更大的表(2*tableSize)
	把原表中的有效元素重新哈希并插入新表
	通常在以下条件触发
		- λ>0.5
		- 插入失败
		- 插入次数达到上限