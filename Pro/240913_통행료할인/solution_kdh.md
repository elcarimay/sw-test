```cpp
#if 1
// 도로는 초기 2000 + 추가 1400해서 3400개가 최대임
// 다익스트라계산시 costFinal[MAX_CITY][MAX_DISCOUNT]로 할인에 따른 최소 비용을 계산함 
// 다익스트라계산시 주어진 할인권개수까지 나오도록 종료조건 설정
// cost함수에서 할인권을 사용했을때를 계산하므로 할인권 0인 경우는 포함하지 않고 계산
// 할인권이 0인 경우를 포함하면 case 21에서 정답이 안나옴

#include<vector>
#include<unordered_map>
#include<queue>
#include<algorithm>
using namespace std;

#define MAX_CITY 303
#define MAX_ROAD 3403
#define MAX_DISCOUNT_TICKET 11
#define INF 987654321
struct DB {
	int s, e, c;
}db[MAX_ROAD];

struct Edge {
	int to, cost, used_coupon;
	bool operator<(const Edge& r)const {
		return cost > r.cost;
	}
	bool operator==(const Edge& r)const {
		return to == r.to && cost == r.cost;
	}
};

vector<Edge> adj[MAX_CITY];
unordered_map<int, int> idMap;
int idCnt, N, M;

int getID(int c) {
	return idMap.count(c) ? idMap[c] : idMap[c] = idCnt++;
}

void add(int mId, int sCity, int eCity, int mToll) {
	db[getID(mId)] = { sCity, eCity, mToll };
	adj[sCity].push_back({ eCity,mToll });
}

void init(int N, int K, int mId[], int sCity[], int eCity[], int mToll[]) {
	::N = N, idMap.clear(), idCnt = 0;
	for (int i = 0; i < MAX_CITY; i++) adj[i].clear();
	for (int i = 0; i < K; i++)	add(mId[i], sCity[i], eCity[i], mToll[i]);
}

void remove(int mId) {
	int id = getID(mId);
	int s = db[id].s, e = db[id].e, c = db[id].c;
	adj[s].erase(find(adj[s].begin(), adj[s].end(), Edge{ e, c }));
}

int costFinal[MAX_CITY][MAX_DISCOUNT_TICKET]; // 사용한 할인권 개수에 따른 최소 비용
int dijkstra(int m, int s, int e) {
	for (int i = 0; i < N; i++) for (int j = 0; j < MAX_DISCOUNT_TICKET; j++) costFinal[i][j] = INF;
	costFinal[s][0] = 0;
	priority_queue<Edge> pq;
	pq.push({ s, 0, 0 }); // to, cost, used_coupon

	while (!pq.empty()) {
		Edge cur = pq.top(); pq.pop();
		if (cur.to == e && cur.used_coupon == m) {
			int ret = INF;
			for (int i = 1; i <= m; i++) ret = min(ret, costFinal[e][i]); // 할인권을 사용한다는 가정이 있음
			return ret == INF ? -1 : ret;
		}

		if (cur.cost > costFinal[cur.to][cur.used_coupon]) continue;

		for (Edge nx : adj[cur.to]) {
			int nextCost = cur.cost + nx.cost;
			if (costFinal[nx.to][cur.used_coupon] > nextCost) { // 할인권을 사용하지 않는 경우
				costFinal[nx.to][cur.used_coupon] = nextCost;
				pq.push({ nx.to, nextCost, cur.used_coupon });
			}
			if (cur.used_coupon <= m) { // 할인권을 사용할 수 있는 경우
				int discountCost = cur.cost + nx.cost / 2;
				//int discountCost = cost + (int)ceil(nx.cost / 2.0);
				if (costFinal[nx.to][cur.used_coupon + 1] > discountCost) {
					costFinal[nx.to][cur.used_coupon + 1] = discountCost;
					pq.push({ nx.to, discountCost, cur.used_coupon + 1 });
				}
			}
		}
	}
	return -1; // 도착하지 못하는 경우
}

int cost(int M, int sCity, int eCity) {
	return dijkstra(M, sCity, eCity);
}
#endif // 1
```
