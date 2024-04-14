```cpp
#define _CRT_NO_SECURE_WARNGINS
#include <vector>
#include <queue>
using namespace std;

#define MAX 5010
#define BRAND_MAX 50
#define INF 987654321

int N, brand[MAX];

struct Edge {
	int to, dist;
	bool operator<(const Edge& edge)const {
		return dist > edge.dist;
	}
};

vector<Edge> adj[MAX];
vector<int> brandList[BRAND_MAX];

void init(int _N, int mBrands[]) {
	N = _N;
	for (int i = 0; i < BRAND_MAX; i++) brandList[i].clear();
	for (int i = 0; i < N; i++) {
		brandList[brand[i] = mBrands[i]].push_back(i);
		adj[i].clear();
	}
}

void connect(int mHotelA, int mHotelB, int mDistance) {
	adj[mHotelA].push_back({ mHotelB, mDistance });
	adj[mHotelB].push_back({ mHotelA, mDistance });
}

int merge(int mHotelA, int mHotelB) {
	int brandA = brand[mHotelA];
	int brandB = brand[mHotelB];
	if (brandA != brandB)
		for (auto b : brandList[brandB])
			brandList[brand[b] = brandA].push_back(b);
	return brandList[brandA].size();
}

int cost[MAX];
int dijkstra(int mStart, int mBrand, int mHotel) {
	fill(cost, cost + N, INF);
	cost[mStart] = 0;
	priority_queue<Edge> Q;
	Q.push({ mStart, 0 });
	int selectedHotel = 0;
	while (!Q.empty()) {
		auto cur = Q.top(); Q.pop();
		if (cur.dist > cost[cur.to]) continue;
		if (cur.to != mStart && cur.to != mHotel &&
			brand[cur.to] == mBrand) {
			selectedHotel = cur.to; break;
		}
		for (auto next: adj[cur.to]) {
			int nextCost = cost[cur.to] + next.dist;
			if (cost[next.to] > nextCost)
				Q.push({ next.to, cost[next.to] = nextCost });
		}
	}
	return selectedHotel;
}

int move(int mStart, int mBrandA, int mBrandB) {
	int selectA = dijkstra(mStart, mBrandA, -1);
	int costA = cost[selectA]; //cost는 초기화되어 바뀌기 때문에 할때마다 cost를 구해야 함.
	return costA + cost[dijkstra(mStart, mBrandB, selectA)];
}
```
