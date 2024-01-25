```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <vector>
#include <queue>
using namespace std;

const int MAX = 5010;
const int BRAND_MAX = 50;
const int INF = 987654321;

int N, brand[MAX];

struct Edge
{
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
		brand[i] = mBrands[i];
		brandList[brand[i]].push_back(i);
		adj[i].clear();
	}
}

void connect(int mHotelA, int mHotelB, int mDistance){
	adj[mHotelA].push_back({ mHotelB, mDistance });
	adj[mHotelB].push_back({ mHotelA, mDistance });
}

int merge(int mHotelA, int mHotelB){
	int brandA = brand[mHotelA];
	int brandB = brand[mHotelB];

	if (brandA == brandB) return brandList[brandA].size();

	for (auto b:brandList[brandB])
	{
		brandList[brandA].push_back(b);
		brand[b] = brandA;
	}
	return brandList[brandA].size();
}

int cost[MAX];

int dijkstra(int mStart, int mBrandA, int mBrandB) {
	for (int i = 0; i < N; i++) cost[i] = INF;
	cost[mStart] = 0;
	priority_queue<Edge> Q;
	Q.push({ mStart, 0 });
	int selectedHotel = 0;
	while (Q.size()) {
		auto cur = Q.top(); Q.pop();
		if (cur.dist > cost[cur.to]) continue;
		if (cur.to != mStart && cur.to != mBrandB && brand[cur.to] == mBrandA) {
			selectedHotel = cur.to; break;
		}
		for (auto next:adj[cur.to])
		{
			int nextCost = cost[cur.to] + next.dist;
			if (cost[next.to] > nextCost) {
				cost[next.to] = nextCost;
				Q.push({ next.to,cost[next.to] });
			}
		}
	}
	return selectedHotel;
}

int move(int mStart, int mBrandA, int mBrandB){
	int selectA = dijkstra(mStart, mBrandA, -1);
	int costA = cost[selectA]; //cost는 초기화되어 바뀌기 때문에 할때마다 cost를 구해야 함.
	return costA + cost[dijkstra(mStart, mBrandB, selectA)];
}
```
