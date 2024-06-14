```cpp
#include <vector>
#include <queue>
using namespace std;

struct Edge
{
    int to, time;
    bool operator<(const Edge& r)const {
        return time > r.time;
    }
};

const int MAX = 50;
const int INF = 987654321;

int N;
vector<Edge> adj[MAX];

void add(int sCity, int eCity, int mTime) {
    adj[sCity].push_back({ eCity, mTime });
    return;
}

void init(int N, int E, int sCity[], int eCity[], int mTime[]) {
    ::N = N;
    for (int i = 0; i < N; i++) adj[i].clear();
    for (int i = 0; i < E; i++)
        add(sCity[i], eCity[i], mTime[i]);
}
/*
    재귀 함수 : 자기 자신을 호출하는 함수
    n! = 1*2*...*n
    n! = (n-1) * n

    기저사례(Base Case) 더이상 쪼개지지 않는 경우
*/
int factorial(int n) {
    if (n == 1)
        return 1;

    return factorial(n - 1) * n;
}
int M; // 경유지 갯수 M개
int sender[8], receiver[8];
int selected[8];

int cost[MAX][MAX];

int dfs(int m, int cur, int totalCost) {
    // 전체 탐색이 완료된 경우 -> 전체 코스트 합을 리턴(다구했으니까)
    if (m == M) return totalCost;
    int ret = INF;
    // 0 ~ M-1까지 순차적으로 탐색
    for (int i = 0; i < M; i++) {
        if (selected[i]) continue;
        selected[i] = 1;
        int nextCost = cost[cur][sender[i]] + cost[sender[i]][receiver[i]];
        int nextResult = dfs(m + 1, receiver[i], totalCost + nextCost);
        ret = min(ret, nextResult);
        selected[i] = 0;
    }
    return ret;
}

void dijkstra(int start) {
    for (int i = 0; i < N; i++) cost[start][i] = INF;
    cost[start][start] = 0;
    priority_queue<Edge> Q;
    Q.push({ start, 0 });
    while (!Q.empty()) {
        auto cur = Q.top(); Q.pop();
        if (cur.time > cost[start][cur.to]) continue;
        for (auto& next : adj[cur.to]) {
            int nextCost = cost[start][cur.to] + next.time;
            if (cost[start][next.to] > nextCost) {
                cost[start][next.to] = nextCost;
                Q.push({ next.to, nextCost });
            }
        }
    }
}

bool checked[MAX];

int deliver(int mPos, int M, int mSender[], int mReceiver[]) {
    ::M = M;
    for (int i = 0; i < N; i++) checked[i] = false;
    for (int i = 0; i < M; i++) sender[i] = mSender[i], receiver[i] = mReceiver[i];

    dijkstra(mPos);
    checked[mPos] = true;
    for (int i = 0; i < M; i++) {
        if (checked[mSender[i]] == false) {
            dijkstra(mSender[i]);
            checked[mSender[i]] = true;
        }
        if (checked[mReceiver[i]] == false) {
            dijkstra(mReceiver[i]);
            checked[mReceiver[i]] = true;
        }
    }
    int ret = dfs(0, mPos, 0);
    return ret;
}
```
