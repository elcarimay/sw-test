```cpp
#include <vector>
#include <queue>
#include <unordered_map>
using namespace std;
#define MAXN 603

struct Edge {
    int to, cost;
    bool operator<(const Edge& r)const {
        return cost > r.cost;
    }
};

vector<Edge> adj[MAXN], radj[MAXN];
unordered_map <int, int> m;
int N, mCnt, Cost[MAXN];
bool visit[MAXN];

int getID(int c) {
    if (m.count(c)) return m[c];
    else return m[c] = mCnt++;
}

int dijkstra(int s, vector<Edge> v[]) {
    priority_queue<Edge> pq;
    pq.push({ s,0 });
    for (int i = 0; i < MAXN; i++) Cost[i] = INT_MAX, visit[i] = false;
    Cost[s] = 0;
    while (!pq.empty()) {
        Edge cur = pq.top(); pq.pop();
        if (cur.cost < Cost[cur.to]) continue;
        if (visit[cur.to]) continue;
        visit[cur.to] = true;
        for (auto nx : v[cur.to]) {
            int nextCost = cur.cost + nx.cost;
            if (Cost[nx.to] > nextCost) {
                Cost[nx.to] = nextCost;
                pq.push({ nx.to, Cost[nx.to] });
            }
        }
    }
    int ret = 0;
    for (int i = 0; i < mCnt; i++) ret += Cost[i];
    return ret;
}

void add(int sCity, int eCity, int mCost) {
    adj[getID(sCity)].push_back({ getID(eCity), mCost });
    radj[getID(eCity)].push_back({ getID(sCity), mCost });
}

int init(int N, int sCity[], int eCity[], int mCost[]) {
    ::N = N, mCnt = 0, m.clear();
    for (int i = 0; i < MAXN; i++) {
        adj[i].clear(); radj[i].clear();
    }
    for (int i = 0; i < N; i++) add(sCity[i], eCity[i], mCost[i]);
    return mCnt;
}

int cost(int mHub) {
    return dijkstra(m[mHub], adj) + dijkstra(m[mHub], radj);
}
```
