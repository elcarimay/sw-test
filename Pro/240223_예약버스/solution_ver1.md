```cpp
// ver1: 순열 다 구해놓고 최단거리 구함
// 다익스트라로 미리 최소거리에 대한 배열을 구함
// 경유지가 최대 5개인데 5!하면 120정도이지만 출발지와 도착지가 포함되면 안되므로 continue로 건너뛰므로 더 낮아짐
// 전체탐색으로 다 구하는데 순열이 필요하므로 dfs사용
// 경유할때 데 끝점은 역으로 구해서 계산함
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

void init(int N, int K, int mRoadAs[], int mRoadBs[], int mLens[]) {
    ::N = N;
    for (int i = 1; i <= N; i++) adj[i].clear();
    for (int i = 0; i < K; i++) addRoad(mRoadAs[i], mRoadBs[i], mLens[i]);
}

int path[7];
void dijkstra(int s) {
    for (int i = 1; i <= N; i++) cost[i] = INT_MAX;
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
        dist[s][i] = INT_MAX;
        if (i != path[0] || i != path[1]) dist[s][i] = cost[i];
    }
}

int R[5], tail, sum, ret;
bool visit[7];
void dfs(int level) {
    if (level == M) {
        for (int i = 0; i < M - 1; i++) {
            if (dist[R[i]][R[i + 1]] == INT_MAX) return;
            sum += dist[R[i]][R[i + 1]];
        }
        if (dist[path[0]][R[0]] == INT_MAX || dist[path[1]][R[M - 1]] == INT_MAX)
            return;
        sum += dist[path[0]][R[0]] + dist[path[1]][R[M - 1]];
        ret = min(ret, sum); sum = 0; return;
    }
    for (int i = 2; i < 2 + M; i++) {
        if (visit[i]) continue; visit[i] = true;
        R[tail++] = path[i];
        dfs(level + 1);
        visit[i] = false;
        tail--;
    }
}

int findPath(int mStart, int mEnd, int M, int mStops[]) {
    ::M = M, ret = INT_MAX;
    path[0] = mStart, path[1] = mEnd;
    for (int i = 0; i < M; i++) path[i + 2] = mStops[i];
    for (int i = 0; i < 2 + M; i++) dijkstra(path[i]);
    sum = tail = 0; memset(visit, 0, sizeof(visit));
    dfs(0);
    return ret == INT_MAX ? -1 : ret;
}
```
