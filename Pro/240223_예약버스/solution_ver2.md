```cpp
// ver2: 순열을 구해놓고 계산
// ysb: 순열을 모두 다 구해놓고 계산
#include <vector>
#include <queue>
#include <string.h>
using namespace std;

#define MAXN 503
#define INF 1'000'000 // 987654321로 바꾸면 음수가 나오기때문에 안됨

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

void init(int N, int K, int mRoadAs[], int mRoadBs[], int mLens[]) {
    ::N = N;
    for (int i = 1; i <= N; i++) adj[i].clear();
    for (int i = 0; i < K; i++) addRoad(mRoadAs[i], mRoadBs[i], mLens[i]);
}

int path[7];
void dijkstra(int s) {
    for (int i = 1; i <= N; i++) cost[i] = INF;
    cost[s] = 0;
    priority_queue<Edge> pq;
    pq.push({ s,0 });
    while (!pq.empty()) {
        Edge cur = pq.top(); pq.pop();
        if (cur.cost > cost[cur.to]) continue;
        for (auto nx : adj[cur.to]) {
            if (nx.to == path[0] || nx.to == path[1]) continue;
            int nextCost = cost[cur.to] + nx.cost;
            if (cost[nx.to] > nextCost) pq.push({ nx.to, cost[nx.to] = nextCost });
        }
    }
    for (int i = 1; i <= N; i++) {
        if (i != path[0] || i != path[1]) dist[s][i] = cost[i];
    }
}

int R[5], tail, sum, ret;
bool visit[7];
void dfs(int level) {
    if (level == M) {
        for (int i = 0; i < M - 1; i++) sum += dist[R[i]][R[i + 1]];
        sum += dist[path[0]][R[0]] + dist[path[1]][R[M - 1]];
        ret = min(ret, sum); return;
    }
    sum = 0;
    for (int i = 2; i < 2 + M; i++) {
        if (visit[i]) continue; visit[i] = true;
        R[tail++] = path[i];
        dfs(level + 1);
        visit[i] = false;
        tail--;
    }
}

int findPath(int mStart, int mEnd, int M, int mStops[]) {
    ::M = M, ret = INF;
    path[0] = mStart, path[1] = mEnd;
    for (int i = 0; i < M; i++) path[i + 2] = mStops[i];
    for (int i = 0; i < 2 + M; i++) dijkstra(path[i]);
    sum = tail = 0; memset(visit, 0, sizeof(visit));
    dfs(0);
    return ret >= INF ? -1 : ret;
}
```
