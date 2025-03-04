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
            int nextCost = cost[cur.to] + nx.cost;
            if (cost[nx.to] > nextCost)
                pq.push({ nx.to, cost[nx.to] = nextCost });
        }
    }
    for (int i = 0; i < 2 + M; i++) {
        if (s == path[0] && (i == 0 || i == 1)) continue;
        if (s == path[1] && (i == 0 || i == 1)) continue;
        dist[s][path[i]] = cost[path[i]];
    }
}

int R[3], tail, sum, ret;
bool visit[7];
int dfs(int s, int level) {
    if (level == M) {
        for (int i = 0; i < M - 1; i++) sum += dist[R[i]][R[i + 1]];
        if (dist[path[0]][R[0]] == INT_MAX || dist[path[1]][R[M - 1]] == INT_MAX)
            return INT_MAX;
        sum += dist[path[0]][R[0]] + dist[path[1]][R[M - 1]];
        return ret = min(ret, sum);
    }
    for (int i = 2; i < 2 + M; i++) {
        R[tail++] = path[s];
        if (visit[i]) continue;
        visit[i] = true;
        dfs(i, ++level);
        visit[i] = false;
        tail--;
    }
}

int findPath(int mStart, int mEnd, int M, int mStops[]){
    ::M = M, ret = INT_MAX;
    for (int i = 0; i < N; i++) {
        cost[i] = INT_MAX;
        for (int j = 0; j < N; j++) dist[i][j] = INT_MAX;
    }
    path[0] = mStart, path[1] = mEnd;
    for (int i = 0; i < M; i++) path[i + 2] = mStops[i];
    for (int i = 0; i < 2 + M; i++) {
        for (int i = 0; i < N; i++) cost[i] = INT_MAX;
        dijkstra(path[i]);
    }
    sum = tail = 0;
    memset(visit, 0, sizeof(visit));
    ret = dfs(2, 0);
    return ret == INT_MAX ? -1 : ret;
}
```
