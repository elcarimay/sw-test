```cpp
#if 1
// ver2. dijkstra하면서 cost[N][N] 배열 update
// 배송과 도착에 관한 순열을 dfs로 만들어서 계산
// 배송지나 도착지는 중복이 될 수 있으므로 dijkstra계산시 visit함수로 방문체크, visit함수는 N개로 만들어야 함
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;

#define MAXN 53
#define INF 1000000

struct Edge {
    int to, cost;
    bool operator<(const Edge& r)const {
        return cost > r.cost;
    }
};

vector<Edge> adj[MAXN];
int N;

void add(int sCity, int eCity, int mTime) {
    adj[sCity].push_back({ eCity, mTime });
}

void init(int N, int E, int sCity[], int eCity[], int mTime[]) {
    ::N = N;
    for (int i = 0; i < N; i++) adj[i].clear();
    for (int i = 0; i < E; i++) add(sCity[i], eCity[i], mTime[i]);
}

int cost[MAXN][MAXN];
void dijkstra(int s) {
    priority_queue<Edge> pq;
    pq.push({ s, 0 });
    cost[s][s] = 0;
    while (!pq.empty()) {
        Edge cur = pq.top(); pq.pop();
        if (cur.cost > cost[s][cur.to]) continue;
        for (Edge nx : adj[cur.to]) {
            int nextCost = cost[s][cur.to] + nx.cost;
            if (cost[s][nx.to] > nextCost) pq.push({ nx.to, cost[s][nx.to] = nextCost });
        }
    }
}

int M; bool visit[MAXN];
int sender[8], receiver[8];
int dfs(int level, int u, int sum) {
    if (level == M) return sum;
    int ret = INF;
    for (int i = 0; i < M; i++) {
        if (visit[i]) continue; visit[i] = true;
        int nextSum = sum + cost[u][sender[i]] + cost[sender[i]][receiver[i]];
        ret = min(ret, dfs(level + 1, receiver[i], nextSum));
        visit[i] = false;
    }
    return ret;
}

void v_clear() {
    memset(visit, 0, sizeof(visit));
}

int deliver(int mPos, int M, int mSender[], int mReceiver[]) {
    ::M = M;
    for (int i = 0; i < M; i++) sender[i] = mSender[i], receiver[i] = mReceiver[i];
    for (int i = 0; i < N; i++) for (int j = 0; j < N; j++) cost[i][j] = INF;
    dijkstra(mPos); v_clear();
    for (int i = 0; i < M; i++) if (!visit[sender[i]]) visit[sender[i]] = 1, dijkstra(sender[i]);
    v_clear();
    for (int i = 0; i < M; i++) if (!visit[receiver[i]]) visit[receiver[i]] = 1, dijkstra(receiver[i]);
    v_clear();
    int result = dfs(0, mPos, 0);
    return result;
}
#endif // 0
```
