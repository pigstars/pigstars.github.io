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
    for(int i = 0;i < G.n;i++)
    {
        lowcast[i] = G -> edges[v][i];
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
            }
        }
        
        visited[j];
        sum += min;
        for(int j = 0;j < G->n;j++)
        {
            if(visited[j] == 0 && lowcast[j] > G->edges[loc][j])
                lowcast[j] = G->edges[loc][j];
        }
    }
    cout<<"sum:"<<sum;
}
```


