# Shortest Path Algorithm



[TOC]

### 一、Floyd



```C++
/*
* 整体思想为Dynamic Programming(动态规划)
*/

/*
* Floyd 算法可以求任意A点到任意B点的最短路径
* 适用于无向图和有向图，也可以用于负权边，但是不能存在负权回路(这将毫无意义)
* 其时间复杂度较高为 O(n^3)
*/

/*
* 路线的初始化：
*	对于Floyd一般用二维数组是比较方便的，Edge[i][j]指从i到j的距离。如果实际中没有从i到j的路线
* 我们令Edge[i][j]=0x3f3f3f
*
* 思路：
*	对于任意点A到任意点B,一种方法就是从直接从A到B,即Edge[A][B],另一种方法就是从A到X,然后从X到
* B.所以有 Edge[A][X]+Edge[X][B],对这两种结果比较取最小即可
*/

void Floyd(vector<vector<int>>& Edge){
    int n=Edge.size();
    for(int k=0;k<n;++k){           // k为中转站
        for(int i=0;i<n;++i){  		// i为起点
            for(int j=0;j<n;++j){	// j为终点
                Edge[i][j]=min(Edge[i][j],Edge[i][k]+Edge[k][j]);
            }
        }
    } 
}
    
```



### 二、Dijkstra



````C++
/*
* 以V1为结点找到它的邻接，最小的就是最近的，以此为引子，进行贪心和动态规划，求出所有单源最短路径
*/

/*
* Dijkstra 算法是典型的单源最短路径算法,即点A到任意点B的最短路径
* 适用于无向图和有向图，不能用于负权边
* 时间复杂度为O(n^2)
*/

/*
* 路线的初始化：
*	对于Dijkstra一般用二维数组是比较方便的，Edge[i][j]指从i到j的距离。如果实际中没有从i到j的路线
* 我们令Edge[i][j]=0x3f3f3f
*
* 思路：
* 	这是一个基于贪心的思想，以局部最优解来确定全局最优解。具体实现是dis数组来保存A点到其它点的距离，
* hash数组来确定当前A点到该点的距离是否是已经最短。
*/

// 我们假设求 0 到任意点的最短距离
vector<int> Dijkstra(vector<vector<int>> Edge){
    int n=Edge.size();
    vector<int> dis(n,0x3f3f);
    vector<bool> hash(n,false);
    dis[0]=0;        // 0到0的最小距离为0
    int upMin=0;     // 上次遍历已经确定的最小距离,初始为0
    for(int i=1;i<n;++i){
        int upDis=0x3f3f;     // 上次遍历的最小距离，未确定
        for(int k=0;k<n;++k){  // 找到上次遍历的最小未确定距离 
            if(!hash[k]&&dis[k]<upDis){
                upDis=dis[k];upMin=k;
            }
        }
        hash[upMin]=true;      // 将上次遍历的最小未确定距离，标记为已确定
        for(int j=1;j<n;++j){
            if(!hash[j]){ // 如果已经是局部最优，无需更新
                // 更新局部的解
                dis[j]=min(dis[j],dis[upMin]+Edge[upMin][j]);
            }
        }
    }
    return dis[n-1];
}

````


### 三、Bellman Ford

````C++
/*
* Bellman Ford 算法也是求单源最短路径的算法，不同的是它可以求带有负权的边，同时也可以判断是否存在
* 负权回路。当然适用于 有向图&无向图
* 时间复杂度为O(VE), V为顶点数，E为边数。
*/

/*
* 路线的初始化：
*	这里我们用了一个struct来储存一条有向的边，to为起点，from为终点，cost为权
*
* 思路：
* 	对所有的边进行n-1轮松弛操作，因为在一个含有n个顶点的图中，任意两点之间的最短路径最多包含n-1边。
* 换句话说，第1轮在对所有的边进行松弛后，得到的是源点最多经过一条边到达其他顶点的最短距离；
* 第2轮在对所有的边进行松弛后，得到的是源点最多经过两条边到达其他顶点的最短距离；
* 第3轮在对所有的边进行松弛后，得到的是源点最多经过一条边到达其他顶点的最短距离......
*/


struct Edge //边
{
	int from;
    int to;
	int cost;
};
 
                           //   边集            // 点的个数
vector<int> Bellman_Ford(vector<Edge*>& edge,int nodeNum){
    vector<int> dis(nodenum,0x3f3f3f);
    dis[0]=0;
    int edgeNum=edge.size();    
    for(int i=0;i<nodeNum;++i){ // 会对每一个边做 n-1 次
        for(int j=0;j<edgeNum;++j){  // 每一轮对所有边做松弛操作
            dis[edge[i]->to]=min(dis[edge[i]->to],dis[edge[i]->from]+edge[i]->cost);
        }
    }
    for(int k=0;k<edgeNum;++k){ // 判断负回路
        // if条件如果为True说明存在负回路
        if(dis[edge[k]->to]<dis[edge[k]->from]+edge[k]->cost){
            return {};
        }
    }
    return dis; // 不存才负回路；
}

````



### 四、SPFA



```C++
/*
* SPFA 算法是可以说是基于 Bellman Ford 算法基于队列优化
* 但是无法对求存在的负回路的图
* 复杂度一般是小于 O(VE)
*/

/*
* 路线初始化：
* 	同Bellman Ford 算法相同
*
* 优化思路：
*	因为如果我们知道Dijkstra,其实每一条边都是非必要进行n-1,所以我们基于此优化
* 创建一个队列，对每某次松弛操作成功的点且不再队列的点将其入队，等到下次遍历取
* 队列的front点进行松弛，取后出队，当队列为空时完成操作
* 本质就是用经过松弛的点，以此进行下一步松弛
*/

struct Edge //边
{
    int from;
    int to;
	int cost;
};

vector<int> SPFA(vector<Edge*> edge,int nodeNum){
    int edgeNum=edge.size();
    queue<int> qu; // 创建队列
    vector<bool> hash(node,false); // 判断是否在队列
    vector<int> dis(0,0x3f3f3f);
    // 初始化各数据结构的初始状态
    dis[0]=0;
    qu.push(0);
    hash[0]=true;
    while(!qu.empty()){
        int temp=qu.front();
        qu.pop();
        hash[temp]=false;
        for(int i=0;i<edgeNum;++i){
                        // 是否邻接                 是否松弛成功               
            if(edge[i]->from==temp&&dis[edge[i]->to]<dis[temp]+edge[i]){ 
                dis[edgge[i]->to]=dis[temp]+edge[i];
                if(!hash[edge[i]->to]){  // 如果队列中没有该点将其入队
                    qu.push(edge[i]->to);
                    hash[to]=true;
                }
            }
        }
    }
    return dis;
}

```

