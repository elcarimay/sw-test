```cpp
#include <vector>
#include <queue>
#include <string.h>
using namespace std;

#define MAXN 503

struct Edge {
    int to, cost;
    bool operator<(const Edge& r)const {
        return cost > r.cost;
    }
};

vector<Edge> adj[MAXN];
int N, M, cost[MAXN], dist[MAXN][MAXN];
void addRoad(int mRoadA, int mRoadB, int mLen) {
    adj[mRoadA].push_back({ mRoadB, mLen });
    adj[mRoadB].push_back({ mRoadA, mLen });
}

void init(int N, int K, int mRoadAs[], int mRoadBs[], int mLens[]){
    ::N = N;
    for (int i = 1; i <= N; i++) adj[i].clear();
    for (int i = 0; i < K; i++) 
        addRoad(mRoadAs[i], mRoadBs[i], mLens[i]);
}

int path[7];
void dijkstra(int s) {
    cost[s] = 0;
    priority_queue<Edge> pq;
    pq.push({ s,0 });
    while (!pq.empty()) {
        Edge cur = pq.top(); pq.pop();
        if (cur.cost > cost[cur.to]) continue;
        if ((s != path[0] && s != path[1]) && (cur.to == path[0] || cur.to == path[1])) continue;
        if (s == path[0] && cur.to == path[1]) continue;
        if (s == path[1] && cur.to == path[0]) continue;
        for (auto nx : adj[cur.to]) {
            if (nx.to == path[0] || nx.to == path[1]) continue;
            int nextCost = cost[cur.to] + nx.cost;
            if (cost[nx.to] > nextCost)
                pq.push({ nx.to, cost[nx.to] = nextCost });
        }
    }
    for (int i = 1; i <= N; i++) {
        if (i == path[0] || i == path[1]) continue;
        dist[s][i] = cost[i];
    }
}

int R[3], tail, sum, ret;
bool visit[7];
void dfs(int level) {
    if (level == M) {
        for (int i = 0; i < M - 1; i++) {
            if (dist[R[i]][R[i + 1]] == INT_MAX) {
                ret = INT_MAX; return;
            }
            sum += dist[R[i]][R[i + 1]];
        }
        if (dist[path[0]][R[0]] == INT_MAX || dist[path[1]][R[M - 1]] == INT_MAX) {
            ret = INT_MAX; return;
        }
        sum += dist[path[0]][R[0]] + dist[path[1]][R[M - 1]];
        if (sum == 0) {
            ret = INT_MAX; return;
        }
        ret = min(ret, sum); sum = 0; return;
    }
    for (int i = 2; i < 2 + M; i++) {
        if (visit[i]) continue;
        visit[i] = true;
        R[tail++] = path[i];
        dfs(level + 1);
        visit[i] = false;
        tail--;
    }
}

int findPath(int mStart, int mEnd, int M, int mStops[]){
    ::M = M, ret = INT_MAX;
    for (int i = 1; i <= N; i++) {
        cost[i] = INT_MAX;
        for (int j = 1; j <= N; j++) dist[i][j] = INT_MAX;
    }
    path[0] = mStart, path[1] = mEnd;
    for (int i = 0; i < M; i++) path[i + 2] = mStops[i];
    for (int i = 0; i < 2 + M; i++) {
        for (int i = 1; i <= N; i++) cost[i] = INT_MAX;
        dijkstra(path[i]);
    }
    sum = tail = 0;
    memset(visit, 0, sizeof(visit));
    dfs(0);
    return ret == INT_MAX ? -1 : ret;
}
```
```
2 100
41
1 10 10 10 6 2 7 3 6 3 4 9 7 4 2 7 6 1 8 5 7 10 9 10 29 26 27 12 26 27 20 20 25
2 2 1 27
3 1 3 4 2 6 4 10 -1
2 1 7 24
2 3 10 14
3 1 9 4 3 10 6 4 135
2 10 8 15
2 6 3 2
3 7 8 4 9 6 10 3 87
3 2 3 4 5 9 10 1 -1
3 8 1 4 3 10 9 5 135
2 2 9 4
2 5 10 26
2 6 1 19
2 5 9 14
2 9 3 23
2 1 4 18
3 1 4 4 3 8 6 9 99
2 5 8 28
2 8 3 29
2 5 4 16
3 9 6 4 7 2 10 3 76
2 8 1 22
2 6 9 17
2 3 7 18
2 9 4 22
2 4 3 27
2 3 2 22
2 7 8 3
2 6 5 19
3 5 3 4 1 4 7 9 89
2 10 1 14
2 1 9 27
3 9 5 4 8 7 1 4 84
2 8 4 13
2 7 5 18
2 6 10 6
2 9 8 25
2 2 8 22
2 10 2 8
3 3 10 4 8 4 6 5 65
```
