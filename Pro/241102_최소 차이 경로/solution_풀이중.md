```cpp
#include <vector>
#include <queue>
#include <unordered_map>
#include <string.h>
#include <algorithm>
#include <set>
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
    if (mId == 948385188) {
        mId = mId;
    }
    auto& a = road[mId];
    adj[a.start].erase(find(adj[a.start].begin(), adj[a.start].end(), Edge{ a.end, a.cost }));
    costs.erase(find(costs.begin(), costs.end(), Edge{ a.end, a.cost }));
}

bool visited[303];
//vector<pair<int, int>> tmp;
bool canReachWithRange(int start, int end, int minCost, int maxCost) {
    //if (minCost == 48 || minCost == 110 || minCost == 133 || minCost == 254 || minCost == 302) tmp.push_back({ minCost, maxCost });
    //printf("Checking range: %d ~ %d\n", minCost, maxCost);
    //printf("Difference: %d\n", maxCost - minCost);
    queue<int> q;
    memset(visited, 0, sizeof(visited));
    q.push(start);
    visited[start] = true;
    while (!q.empty()) {
        int node = q.front(); q.pop();
        //printf("Visiting: %d\n", node);
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
int minCostDifference(int start, int end) { // 최소 비용 차이를 찾는 이분 탐색 함수
    Cost.clear();
    for (Edge nx : costs) Cost.push_back(nx.cost);
    sort(Cost.begin(), Cost.end()); // 정렬
    Cost.erase(unique(Cost.begin(), Cost.end()), Cost.end()); // 중복제거
    int left = 0, right = Cost.size() - 1, answer = INF;
    for (int i = 0; i < Cost.size(); i++) for (int j = i; j < Cost.size(); j++) {
        int minCost = Cost[i], maxCost = Cost[j];
        if (canReachWithRange(start, end, minCost, maxCost)) {
            answer = min(answer, maxCost - minCost);
        }
    }
    return answer;
}

int cost(int sCity, int eCity) {
    //printf("\n\nsCity = %d, eCity = %d\n", sCity, eCity);
    int ret;
    ret = minCostDifference(sCity, eCity);
    return (ret == INF || ret < 0) ? -1 : ret;
}
```
