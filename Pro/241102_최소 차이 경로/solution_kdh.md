```cpp
#if 1
// cost를 정렬하여 two-pointer를 사용
// cost 정렬시 중복되지 않아야 하며 지울때 중복된 노드도 있기때문에 노드를 같이 저장하면서 지워야 함
// 투포인터로 범위를 증가시키면서 canReachWithRange함수로 도달할수 있는지 확인함
#include <vector>
#include <queue>
#include <unordered_map>
#include <string.h>
#include <algorithm>
using namespace std;
#define INF 987654321

struct Edge {
    int to, cost;
    bool operator==(const Edge& r)const {
        return to == r.to && cost == r.cost;
    }
};

struct Data {
    int start, end, cost;
};

vector<Edge> adj[303];
unordered_map<int, Data> road;
int N;
vector<Edge> costs;
void add(int mId, int sCity, int eCity, int mCost) {
    road[mId] = { sCity, eCity, mCost };
    adj[sCity].push_back({ eCity, mCost });
    costs.push_back({ eCity, mCost });
}

void init(int N, int K, int mId[], int sCity[], int eCity[], int mCost[]) {
    ::N = N; road.clear(), costs.clear();
    for (int i = 0; i < 303; i++) adj[i].clear();
    for (int i = 0; i < K; i++) add(mId[i], sCity[i], eCity[i], mCost[i]);
}

void remove(int mId) {
    auto& a = road[mId];
    adj[a.start].erase(find(adj[a.start].begin(), adj[a.start].end(), Edge{ a.end, a.cost }));
    costs.erase(find(costs.begin(), costs.end(), Edge{ a.end, a.cost }));
}

bool visited[303];
bool canReachWithRange(int start, int end, int minCost, int maxCost) {
    queue<int> q;
    memset(visited, 0, sizeof(visited));
    q.push(start);
    visited[start] = true;
    while (!q.empty()) {
        int node = q.front(); q.pop();
        if (node == end) return true;
        for (Edge edge : adj[node]) {
            if (!visited[edge.to] && minCost <= edge.cost && edge.cost <= maxCost) {
                visited[edge.to] = true;
                q.push(edge.to);
            }
        }
    }
    return false;
}

vector<int> Cost;
int minCostDifference(int start, int end) { 
    Cost.clear();
    for (Edge nx : costs) Cost.push_back(nx.cost);
    sort(Cost.begin(), Cost.end()); // 정렬
    Cost.erase(unique(Cost.begin(), Cost.end()), Cost.end()); // 중복제거
    int left = 0, right = 0, answer = INF; // 최소 비용 차이를 찾는 two-pointer
    while (right < Cost.size()) {
        int minCost = Cost[left], maxCost = Cost[right];
        if (canReachWithRange(start, end, minCost, maxCost)) {
            answer = min(answer, maxCost - minCost);
            left++;
        }
        else right++;
    }
    return answer;
}

int cost(int sCity, int eCity) {
    int ret = minCostDifference(sCity, eCity);
    return ret == INF ? -1 : ret;
}
#endif // 1
```
