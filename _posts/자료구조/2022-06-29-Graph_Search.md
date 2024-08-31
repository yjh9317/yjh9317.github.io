---
title: Graph Search
date: 2022-06-29
categories: [자료구조, Tree]
tags: [data_structure]		# TAG는 반드시 소문자로 이루어져야함!
---

그래프 탐색
======================
 * 한 정점에서 시작하여 그래프에 있는 모든 정점을 한 번씩 방문하여 처리하는 연산

 * 탐색 종류
   * 깊이 우선 탐색 (DFS)

   * 너비 우선 탐색 (BFS)



깊이 우선 탐색(DFS)
========================
 * 시작 정점에서 한 방향으로 계속 가다가 더 이상 갈 수 없게 되면, 다시 가장 마지막 갈림길로 돌아와서 다른 방향으로 다시 탐색을 진행



 * 과정
 1. 시작 정점v를 방문한다.<br>
 2. 정점 v에 인접한 정점 중에서<br>
    (1) 방문하지 않은 정점 w가 있으면 정점v를 스택에 push하고 w를 방문한다. 그리고 w를 v로하여 다시 2번을 반복한다.<br>

    (2) 방문하지 않은 정점이 없으면 스택을 pop하여 받은 가장 마지막에 방문한 정점을 v로 설정한뒤 다시 2번을 수행한다.<br>
 3. 스택이 공백이 될 때까지 2번을 반복한다.


DFS Code
=========================

```c++
#include <iostream>
#include <stdio.h>
#include <vector>
#include <algorithm>

using namespace std;

// dfs에 들어온 정점은 check에서 true로 설정(방문했다고 표시)
// 방문한 정점 , 그래프 , 방문표시할 배열
void dfs(int start, vector<int> graph[], bool check[]){
    check[start]= true; //방문
    printf("%d ", start);

    for(int i=0; i < graph[start].size(); i++){
        int next = graph[start][i];
        // 방문하지 않았다면
        if(check[next]==false){
            // 다음 정점을 방문(재귀)
            dfs(next, graph, check);
        }
    }
}

int main (){

    int n, m, start;
    cin >> n >> m >> start;

    vector<int> graph[n+1];

    bool check [n+1];
    fill(check, check+n+1, false);

    for(int i=0; i<m; i++){
        int u,v;
        cin >> u >> v;

        graph[u].push_back(v);
        graph[v].push_back(u);
    }

    
    //Sorting한 이유는 하나의 정점에서 다음을 탐색할 때 node 값에 순차적으로 접근해야하기 때문
    for(int i=1; i<=n; i++){
        sort(graph[i].begin(), graph[i].end());
    }

    //dfs
    dfs(start, graph, check);
    printf("\n");

    return 0;
}
```

<br><br>

BFS
============================
* 시작 정점에 인접한 정점을 모두 차례로 방문하고 나서 방문했던 정점을 시작으로 다시 인접한 정점을 차례로 방문하고 처리하는 연산



* 과정
  1. 시작 정점v를 결정하여 방문한다.

  2. 정점 v에 인접한 정점 중에서 방문하지 않은 정점을 차례로 방문하면서 큐에 enQueue한다.

  3. 방문하지 않은 인접한 정점이 없으면, 방문했던 정점에서 인접한 정점을 다시 차례로 방문하기 위해 큐에서 deQueue하여 받은 정점을 v로 설정하고 2번을 반복한다

  4. 큐가 공백이 될 때까지 2번,3번을 반복한다



BFS Code
====================

```c++
#include <iostream>
#include <stdio.h>
#include <vector>
#include <algorithm>
#include <queue>

using namespace std;
        
void bfs(int start, vector<int> graph[], bool check[]){
    queue<int> q;   //  다음번 방문할 정점을 저장하는 큐

    q.push(start);  // 처음 들어온 정점을 push
    check[start] = true; // 처음 들어온 정점 방문표시

    while(!q.empty()){
        int tmp = q.front(); // 다음번 방문할 정점인덱스
        q.pop();
        printf("%d ",tmp);
        for(int i=0; i<graph[tmp].size(); i++){

            // 방문하지 않았다면
            if(check[graph[tmp][i]] == false){
                // 큐에 넣어주고 방문표시
                q.push(graph[tmp][i]);
                check[graph[tmp][i]] = true;
            }
        }
    }
}

int main (){

    int n, m, start;
    cin >> n >> m >> start;

    vector<int> graph[n+1];
    bool check [n+1];
    fill(check, check+n+1, false);

    for(int i=0; i<m; i++){
        int u,v;
        cin >> u >> v;

        graph[u].push_back(v);
        graph[v].push_back(u);
    }

    //Sorting한 이유는 하나의 정점에서 다음을 탐색할 때 node 값에 순차적으로 접근해야하기 때문
    for(int i=1; i<=n; i++){
        sort(graph[i].begin(), graph[i].end());
    }

    //bfs
    bfs(start, graph, check);
    printf("\n");

    return 0;
}
```