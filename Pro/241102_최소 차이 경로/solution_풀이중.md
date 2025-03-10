```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif
#include <stdio.h>
extern void init(int N, int K, int mId[], int sCity[], int eCity[], int mCost[]);
extern void add(int mId, int sCity, int eCity, int mCost);
extern void remove(int mId);
extern int cost(int sCity, int eCity);
/////////////////////////////////////////////////////////////////////////
#define MAX_K 1000
#define CMD_INIT 100
#define CMD_ADD 200
#define CMD_REMOVE 300
#define CMD_COST 400
static bool run() {
	int q;
	scanf("%d", &q);
	int n, k;
	int mIdArr[MAX_K], sCityArr[MAX_K], eCityArr[MAX_K], mCostArr[MAX_K];
	int mId, sCity, eCity, mCost;
	int cmd, ans, ret = 0;
	bool okay = false;
	for (int i = 0; i < q; ++i) {
		scanf("%d", &cmd);
		switch (cmd) {
		case CMD_INIT:
			okay = true;
			scanf("%d %d", &n, &k);
			for (int j = 0; j < k; ++j) {
				scanf("%d %d %d %d", &mIdArr[j], &sCityArr[j], &eCityArr[j], &mCostArr[j]);
			}
			init(n, k, mIdArr, sCityArr, eCityArr, mCostArr);
			break;
		case CMD_ADD:
			scanf("%d %d %d %d", &mId, &sCity, &eCity, &mCost);
			add(mId, sCity, eCity, mCost);
			break;
		case CMD_REMOVE:
			scanf("%d", &mId);
			remove(mId);
			break;
		case CMD_COST:
			scanf("%d %d %d", &sCity, &eCity, &ans);
			ret = cost(sCity, eCity);
			if (ans != ret)
				okay = false;
			break;
		default:
			okay = false;
			break;
		}
	}
	return okay;
}

#include <time.h>

int main() {
	clock_t start = clock();
	setbuf(stdout, NULL);
	freopen("sample_input-copy.txt", "r", stdin);
	int T, MARK;
	scanf("%d %d", &T, &MARK);
	for (int tc = 1; tc <= T; tc++) {
		int score = run() ? MARK : 0;
		printf("#%d %d\n", tc, score);
	}
	printf("Performance: %d ms\n", (clock() - start));
	return 0;
}
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
int minCostDifference(int start, int end) { // 최소 비용 차이를 찾는 이분 탐색 함수
    Cost.clear();
    for (Edge nx : costs) Cost.push_back(nx.cost);
    sort(Cost.begin(), Cost.end()); // 정렬
    Cost.erase(unique(Cost.begin(), Cost.end()), Cost.end()); // 중복제거
    int left = 0, right = Cost.size() - 1, answer = INF;
    while (left <= right) {
        int mid = (left + right) / 2;
        bool valid = false;

        // (최소 비용, 최대 비용)의 범위를 이동하면서 확인
        for (int i = 0; i < (int)Cost.size() - mid; i++) {
            int minCost = Cost[i], maxCost = Cost[i + mid];
            if (canReachWithRange(start, end, minCost, maxCost)) {
                valid = true;
                answer = min(answer, maxCost - minCost);
                break;
            }
        }
        if (valid) right = mid - 1; // 더 작은 범위를 찾기 위해 감소
        else left = mid + 1; // 현재 범위로 도달 불가능하므로 증가
    }
    return answer;
}

int cost(int sCity, int eCity) {
    if (sCity == 5 && eCity == 2) {
        sCity = sCity;
    }
    int ret;
    ret = minCostDifference(sCity, eCity);
    return ret == INF ? -1 : ret;
}
```
