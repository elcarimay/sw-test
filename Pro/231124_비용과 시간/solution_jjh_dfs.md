```cpp
#include <vector>
using namespace std;
struct Edge
{
        int to;
        int cost, time;
};

const int MAX = 100;
const int INF = 987'654'321;

vector<Edge> adj[MAX];
int N;

void init(int _N, int K, int sCity[], int eCity[], int mCost[], int mTime[]){
        N = _N;
        for (int i = 0; i < N; i++)
                adj[i].clear();
        for (int i = 0; i < K; i++)
                adj[sCity[i]].push_back({ eCity[i], mCost[i], mTime[i] });
        return;
}

void add(int sCity, int eCity, int mCost, int mTime) {
        adj[sCity].push_back({ eCity, mCost, mTime });
}

// dp[C][to] C의 cost를 갖고 to까지 도착하는데 최소 시간
int dp[1001][MAX];

int dfs(int cur, int dest , int cost){
        if (cur == dest)
                return 0;
        int& ret = dp[cost][cur];
        if (ret != -1)
                return ret;
        ret = INF;
        for (auto & n : adj[cur])
        {
                // 갈수 없는 경우 제외
                if (cost < n.cost)
                        continue;

                int next = dfs(n.to, dest, cost - n.cost) + n.time;
                ret = min(ret, next);
        }
        return ret;
}

int cost(int M, int sCity, int eCity) {
        for (int i = 0; i <= M; i++)
                for (int j = 0; j < N; j++)
                        dp[i][j] = -1;
        int ret = dfs(sCity, eCity, M);
        return ret == INF ? -1 : ret;
}
```
