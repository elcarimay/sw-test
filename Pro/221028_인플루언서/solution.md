```cpp
#if 1
#include <iostream>
#include <string.h>
#include <vector>
#include <queue>
#include <set>
using namespace std;
using pii = pair<int, int>;

struct Data {
	int id, sum;
	bool operator<(const Data& r)const {
		if(sum!= r.sum) return sum > r.sum;
		return id < r.id;
	}
};

vector<int> adj[20001];
int power[20001];
int effect[] = { 10, 5, 3, 2 };
int N, vcnt;
set<Data> S;
set<Data>::iterator it[20001]; // it[mID]로 mID의 영향령 파악과 S에서 빠르게 삭제

pii que[20001];
int head, tail;
int visit[20001];
int bfs(int s) {
	int sum = 0;
	head = tail = 0;
	que[tail++] = { s, 0 };
	vcnt++;
	visit[s] = vcnt;
	while (head < tail) {
		pii cur = que[head++];
		if (cur.second + 1 == 5) continue;
		sum += power[cur.first] * effect[cur.second];
		for (int nx : adj[cur.first]) {
			if (visit[nx] == vcnt) continue;
			que[tail++] = { nx, cur.second + 1 };
			visit[nx] = vcnt;
		}
	}
	return sum;
}

void init(int N, int mPurchasingPower[20000], int M, int mFriend1[20000], int mFriend2[20000]) {
	for (int i = 0; i < N; i++) {
		adj[i].clear();
		power[i] = mPurchasingPower[i];
	}
	for (int i = 0; i < M; i++) {
		adj[mFriend1[i]].push_back(mFriend2[i]);
		adj[mFriend2[i]].push_back(mFriend1[i]);
	}
	S.clear();
	for (int i = 0; i < N; i++) it[i] = S.insert({ i, bfs(i) }).first;
}

int influencer(int mRank) {
	auto iter = S.begin();
	for (int i = 0; i < mRank - 1; i++) iter++;
	return iter->id;
}

int addPurchasingPower(int mID, int mPower) {
	power[mID] += mPower;
	head = tail = 0;
	vcnt++;
	que[tail++] = { mID, 0 };
	visit[mID] = vcnt;
	while (head < tail) {
		int id = que[head].first, chon = que[head++].second;
		int newSum = it[id]->sum + mPower * effect[chon]; // id상 새롭게 추가되는 영향력 계산
		S.erase(it[id]);
		it[id] = S.insert({ id, newSum }).first;
		if (chon == 3) continue;
		for (int nx : adj[id]) {
			if (visit[nx] == vcnt) continue;
			que[tail++] = { nx, chon + 1 };
			visit[nx] = vcnt;
		}
	}
	return it[mID]->sum;
}

vector<int> updateID;
int addFriendship(int mID1, int mID2) {
	adj[mID1].push_back(mID2);
	adj[mID2].push_back(mID1);
	que[tail++] = { mID1, 0 };
	que[tail++] = { mID2, 0 };
	vcnt++;
	visit[mID1] = visit[mID2] = vcnt;
	updateID.clear();
	while (head < tail) {
		int id = que[head].first, chon = que[head++].second;
		updateID.push_back(id);
		if (chon == 3) continue;
		for (int nx : adj[id]) {
			if (visit[nx] == vcnt) continue;
			que[tail++] = { nx, chon + 1 };
			visit[nx] = vcnt;
		}
	}
	for (int nx : updateID) {
		S.erase(it[nx]);
		it[nx] = S.insert({ nx, bfs(nx)}).first;
	}
	return it[mID1]->sum + it[mID2]->sum;
}
#endif // 0

```
