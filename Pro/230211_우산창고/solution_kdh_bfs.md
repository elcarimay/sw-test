```cpp
// 미리 자식들의 모든 mQuantity를 totalq에 저장
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;
#define MAXN 10003

int p[MAXN], q[MAXN], totalq[MAXN], depth[MAXN];
vector<int> child[MAXN];
int adj[MAXN][MAXN];
void updateTotal(int x, int quantity) {
	while (x != -1) {
		totalq[x] += quantity;
		x = p[x];
	}
}
int N;
void init(int N, int mParent[], int mDistance[], int mQuantity[]) {
	::N = N, depth[0] = 0;
	for (int i = 0; i < N; i++) {
		p[i] = mParent[i], q[i] = mQuantity[i], totalq[i] = 0;
		if (i) {
			depth[i] = depth[p[i]] + 1, child[p[i]].push_back(i);
			adj[i][p[i]] = mDistance[i], adj[p[i]][i] = mDistance[i];
		}
		updateTotal(i, mQuantity[i]);
	}
}

int carry(int mFrom, int mTo, int mQuantity) {
	int x = mFrom, y = mTo;
	int dx = -mQuantity, dy = mQuantity;
	if (depth[x] < depth[y]) swap(x, y), swap(dx, dy);
	int ret = 0;
	while (depth[x] != depth[y]) {
		totalq[x] += dx;
		ret += adj[x][p[x]];
		x = p[x];
	}
	while (x != y) {
		totalq[x] += dx;
		totalq[y] += dy;
		ret += adj[x][p[x]];
		ret += adj[y][p[y]];
		x = p[x], y = p[y];
	}
	return ret * mQuantity;
}

struct Data {
	int mid, dist;
	bool operator<(const Data& r)const {
		if (dist != r.dist) return dist > r.dist;
		return mid > r.mid;
	}
};
priority_queue<Data> pq;

bool visit[MAXN];

int getLCA(int x, int y) {
	if (depth[x] < depth[y]) swap(x, y);
	while (depth[x] != depth[y]) x = p[x];
	while (x != y) x = p[x], y = p[y];
	return x;
}

int distance(int x) {
	int ret = 0;
	while (x != -1) {
		ret += adj[x][p[x]];
		x = p[x];
	}
	return ret;
}

int gather(int mID, int mQuantity) {
	fill(visit, visit + N, 0);
	q[mID] += mQuantity;
	updateTotal(mID, mQuantity);
	int cost = 0;
	while (!pq.empty() && mQuantity) {
		Data cur = pq.top(); pq.pop();
		if (cur.mid == mID) continue;
		int c = min(mQuantity, q[cur.mid]);
		cost += c * cur.dist;
		mQuantity -= c;
		q[cur.mid] -= c;
		updateTotal(cur.mid, -c);
		int d1 = distance(cur.mid);
		for (int nx : child[cur.mid]) {
			if (visit[nx]) continue;
			visit[nx] = true;
			int lca = getLCA(cur.mid, nx);
			int dist = d1 + distance(nx) - distance(lca) * 2;
			pq.push({ nx, dist });
		}
	}
	return cost;
}

int sum(int mID) {
	return totalq[mID];
}
```
