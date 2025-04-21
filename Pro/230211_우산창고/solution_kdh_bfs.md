```cpp
#if 1 // 165 ms
// 미리 자식들의 모든 mQuantity를 totalq에 저장
// carry는 depth 및 parent비교를 통해 lca를 구하여 비용계산
// gather는 bfs로 부모 및 자식 노드들을 순회하면서 우큐에 노드번호 및 거리를 입력후 가까운 노드부터 계산
// 상기 bfs를 위하여 init함수에서 자식노드를 부모-자식뿐만 아니라 자식-부모관계도 입력함
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
// 어떤 창고에서 출발하든, 50개 이하의 도로를 거쳐 0번 창고에 도달할 수 있음이 보장된다.
void init(int N, int mParent[], int mDistance[], int mQuantity[]) {
	::N = N, depth[0] = 0;
	for (int i = 0; i < N; i++) child[i].clear();
	for (int i = 0; i < N; i++) {
		p[i] = mParent[i], q[i] = mQuantity[i], totalq[i] = 0;
		updateTotal(i, mQuantity[i]);
		if (i) {
			depth[i] = depth[p[i]] + 1;
			child[p[i]].push_back(i); child[i].push_back(p[i]);
			adj[i][p[i]] = mDistance[i], adj[p[i]][i] = mDistance[i];
		}
	}
}

int carry(int mFrom, int mTo, int mQuantity) {
	q[mFrom] -= mQuantity, q[mTo] += mQuantity;
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
priority_queue<Data> pq, popped;

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
bool visit[MAXN];
void bfs(int mID) {
	popped = {};
	pq.push({ mID, 0 });
	while (!pq.empty()) {
		auto cur = pq.top(); pq.pop();
		popped.push(cur);  
		visit[cur.mid] = true;
		for (int nx : child[cur.mid]) {
			if (visit[nx]) continue;
			pq.push({ nx, cur.dist + adj[nx][cur.mid]});
		}
	}
	while (!popped.empty()) pq.push(popped.top()), popped.pop();
}

int gather(int mID, int mQuantity) {
	fill(visit, visit + N, 0);
	pq = {};
	bfs(mID);
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
	}
	return cost;
}

int sum(int mID) {
	return totalq[mID];
}
#endif // 1
```
