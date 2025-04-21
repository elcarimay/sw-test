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
	while (x >= 0) {
		totalq[x] += quantity;
		x = p[x];
	}
}
int N;
void init(int N, int mParent[], int mDistance[], int mQuantity[]) {
	// 트리 초기화 시 양방향 연결과 거리 정보를 저장하고, 각 노드에 대해 totalq값을 상향 누적.
	::N = N, depth[0] = 0;
	for (int i = 0; i < N; i++) child[i].clear();
	for (int i = 0; i < N; i++) {
		p[i] = mParent[i], q[i] = mQuantity[i], totalq[i] = 0;
		updateTotal(i, mQuantity[i]);
		if (i == 0) continue;
		depth[i] = depth[p[i]] + 1;
		child[p[i]].push_back(i), child[i].push_back(p[i]);
		adj[i][p[i]] = mDistance[i], adj[p[i]][i] = mDistance[i];
	}
}

int carry(int mFrom, int mTo, int mQuantity) {
	// 두 노드간 LCA를 찾아 수량 옮기고 그 비용(거리x수량)을 계산
	q[mFrom] -= mQuantity, q[mTo] += mQuantity;
	int x = mFrom, y = mTo, ret = 0;
	int dx = -mQuantity, dy = mQuantity;
	if (depth[x] < depth[y]) swap(x, y), swap(dx, dy);
	while (depth[x] != depth[y]) {
		totalq[x] += dx;
		ret += adj[x][p[x]];
		x = p[x];
	}
	while (x != y) {
		totalq[x] += dx, totalq[y] += dy;
		ret += adj[x][p[x]], ret += adj[y][p[y]];
		x = p[x], y = p[y];
	}
	return ret * mQuantity;
}

struct Data {
	int mid, dist;
	bool operator<(const Data& r)const {
		return dist == r.dist ? mid > r.mid:dist > r.dist;
	}
};
priority_queue<Data> pq, popped;
bool visit[MAXN];

void bfs(int x) {
	pq.push({ x, 0 }), popped = {};
	while (!pq.empty()) {
		Data cur = pq.top(); pq.pop();
		visit[cur.mid] = true;
		popped.push(cur);
		for (int nx : child[cur.mid]) {
			if (visit[nx]) continue;
			pq.push({ nx, cur.dist + adj[cur.mid][nx] });
		}
	}
	while (!popped.empty()) pq.push(popped.top()), popped.pop();
}

int gather(int mID, int mQuantity) {
	// bfs 기반 우큐를 통해 가까운 노드들의 수량 합계를 반환.
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
		q[cur.mid] -= c;
		updateTotal(cur.mid, -c);
		mQuantity -= c;
	}
	return cost;
}

int sum(int mID) { // 특정 노드에 포함된 모든 하위 노드들의 수량 합계를 반환.
	return totalq[mID];
}
```
