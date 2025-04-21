```cpp
// 미리 자식들의 모든 cnt를 totalCnt에 저장
// 출발/도착지의 ID크기비교를 통하여 공통조상(LCA)까지 진행
// dfs 및 bfs로 트리를 부모방향, 자식방향으로 탐색하면서 우큐에 저장
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;

#define LM 10003
int N, P[LM], Dist[LM];
vector<int> C[LM];
int totCnt[LM], Cnt[LM];

void updateTotal(int x, int c) {
	while (x >= 0) {
		totCnt[x] += c;
		x = P[x];
	}
}

void init(int N, int mParent[], int mDistance[], int mQuantity[]) {
	::N = N;
	for (int i = 0; i < N; i++) C[i].clear();
	for (int i = 0; i < N; i++) {
		if (i) C[mParent[i]].push_back(i);
		P[i] = mParent[i], Cnt[i] = mQuantity[i], Dist[i] = mDistance[i];
		totCnt[i] = 0;
		updateTotal(i, Cnt[i]);
	}
}

int carry(int mFrom, int mTo, int mQuantity) {
	Cnt[mFrom] -= mQuantity, Cnt[mTo] += mQuantity;
	int dist = 0;
	while (mFrom != mTo) {
		if (mFrom < mTo) {
			totCnt[mTo] += mQuantity;
			dist += Dist[mTo];
			mTo = P[mTo];
		}
		else {
			totCnt[mFrom] -= mQuantity;
			dist += Dist[mFrom];
			mFrom = P[mFrom];
		}
	}
	return dist * mQuantity;
}

struct Data {
	int dist, node;
	bool operator<(const Data& r)const {
		if (dist != r.dist) return dist > r.dist;
		return node > r.node;
	}
};

int visited[LM], vcnt;
priority_queue<Data> pq;
void dfs(int x, int dist) {
	if (x == -1 || visited[x] == vcnt) return;
	pq.push({ dist, x });
	visited[x] = vcnt;
	dfs(P[x], dist + Dist[x]);
	for (int cid : C[x]) dfs(cid, dist + Dist[cid]);
}

int gather(int mID, int mQuantity) {
	vcnt++;
	pq = {};
	dfs(mID, 0);
	Cnt[mID] += mQuantity;
	updateTotal(mID, mQuantity);
	int cost = 0;
	while (mQuantity) {
		int tID = pq.top().node, dist = pq.top().dist; pq.pop();
		if (tID == mID) continue;
		int c = min(mQuantity, Cnt[tID]);
		cost += c * dist;
		mQuantity -= c;
		Cnt[tID] -= c;
		updateTotal(tID, -c);
	}
	return cost;
}

int sum(int mID) {
	return totCnt[mID];
}

```
