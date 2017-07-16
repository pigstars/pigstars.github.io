---
layout: post
title: "重读数据结构之图"
description: "description"
category: "编程"
tags: "数据结构"
---
# 重读数据结构之图
图由顶点及顶点间的关系组成的集合

图：

    - 连通图：图中任意两个顶点之间都连通，称为连通图。两点都能互相到达则称为强连通图。
    - 非连通图中极大连通子图称为连通分量
    - 有向图
    - 无向图


* 图的表示：邻接表，邻接矩阵和关联矩阵
* 图的遍历： 深度优先和宽度优先 最短路径，环的检测
* 图中最小生成树： PRIM算法和Kruskal算法
* 网络：最大流，成本最低最大流，匹配

## 图的表示

### 邻接表实现：
```C
//  邻接表中点成员结构
struct ArcNode      
{
    int NO;
    ArcNode * nextarc ;
}
// 邻接表中表首节点结构
struct VNode       
{
    int NO;
    char data;
    ArcNode * firsrarc;
}

class AGraph
{
public:
    int n,e;   // 分别表示总节点数和总边数
    VNode adjlist[MAXSIZE]; 
}
 
```
### 邻接矩阵实现

```C
struct VertexType
{
    int NO;
    char info;
}

class MGraph
{
public:
    int n,e;
    int edges[MAXSIZE][MAXSIZE]；
    VertexType node[MAXSIZE];
}
```

## 遍历

### 深度优先算法
图的深度优先算法和图的递归深度优先算法有异曲同工之妙

```C
void DFS(AGraph * G,int v,int * visited)
{
    if(G.n == 0 || visited == nullptr)
        return ;
    ArcNode * p;
    visit(v);
    visited[v] = 1;
    p = G -> adjlist[v].firstarc;
    
    while (p != nullptr)
    {
        if(visit[p->NO] == 0)
        {
            DFS(G,p -> NO,visited);
        }
        p = p -> nextarc;
    }
}

// 若为非连通图

for (int i = 0;i < G.n;i++)
{
    if(visited[i] == 0)
    {
        DFS(&G,i,visited);
    }
}  
```
### 宽度优先算法

```C
void BFS(AGraph * G , int v, int * visited)
{
    if(G.n == 0 || visited == nullptr)
    return ;
    queue <int> que;
    visit(v);
    visited[v] = 1;
    que.push(v);
    while (!que.empty())
    {
        
        ArcNode * p = G -> adjlist[que.front()].firstarc;
        que.pop();
        while(p != nullptr)
        {
            if(visit[p.NO] == 0)
            {
                visit(p.NO);
                visit[p.NO] == 1;
                que.push(p.NO);
            }
            p = p -> nextarc;
        }
    }
        
}

// 若为非连通图

for(int i = 0;i < G.n;i++)
{
    if(visited[v] == 0)
        BFS(&G,V,visited);
}
```

### 最小生成树
PRIM算法

```C
void PRIM (MGraph * G,int v,int * visited)
{
    int vset[MAXSIZE],lowcast[MAXSIZE];
    int sum = 0;
    vset[v] = 1;
    // 访问节点
    for(int i = 0;i < G.n;i++)
    {
        lowcast[i] = G -> edges[v][i];
        //  将费用更新为当前节点与其他节点的费用
    }

    
    for (int i =1 ;i < G.n;i++)
    {
        int min = INF:
        int loc = 0;
        for (int j = 0;j < G->n;j++)
        {
            if(vset[j] == 0 && lowcast[j] < min)
            {
                loc = j;
                min = lowcast[j];
                // 找到费用最少的节点所在的位置
            }
        }
        
        visited[j];
        sum += min;
        for(int j = 0;j < G->n;j++)
        {
            if(visited[j] == 0 && lowcast[j] > G->edges[loc][j])
                lowcast[j] = G->edges[loc][j];
            // 更新周围未被访问的节点，更新费用    
        }
    }
    cout<<"sum:"<<sum;
}
```

Kruskal 算法

```C
struct Node 
{
	int a,b;
	int w;
}Node;
Node node[MAXSIZE]; // 在主函数内已经输入完毕
void Kruskal(AGraph * G,int &sum ,Road road[])
{
	// 初始化操作
	int visited[MAXSIZE] = {0};
	int v[MAXSIZE];  
	// 作为公共祖先数组，可用于查找节点的父节点
	sum = 0;
	int a,b;
	for(int i = 0;i < G->n;i++)
	{
		v[i] = i;
	}
	sort(node,G->e); // 排序
	int min = INF;
	for(int i = 0;i < G -> e;i++)
	{
		a = getRoot(node[i].a);
		b = getRoot(node[i].b);
		if(a != b)
		{
			v[a] = b;
			sum += node[i].w;
		}
	}
}
```

## 最短路径

dijkstra 最短路径算法

```C
void shortPath(MGraph *G,int v)
{
    int visited[MAXSIZE] = {0};
    int dict[MAXSIZE],father[MAXSIZE];
    for(int i = 0;i < G->n;i++)
    {
        dict[i] = G->edges[v][i];
        if(dict[i] < INF)
        {
            father[i] = v;
        }
        else
        {
            father[i] = -1;
        }
    }
    for(int i = 0;i < G.n -1;i++)
    {
        int min = INF;
        int loc = 0;
        for(int j = 0;j < G.n;j++)
        {
            if(visited[j] == 0 && dict[j] < min)
            {
                min = dict[j];
                loc = j;
            }
        }
        visite[loc] = 1;
        for(int j = 0;j < G.n;j++)
        {
            if(visited[j] == 0 && dict[j] < G->edges[loc][j] + dict[loc])
            {
            dict[j] = G->edges[loc][j] + dict[loc];
            father[j] = loc;
            }
        }
    }
}
            
```

