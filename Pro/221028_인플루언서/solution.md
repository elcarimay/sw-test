```cpp
#if 1
#include <vector>
#include <algorithm>
#include <set>
using namespace std;

#define MAXN 20003
struct Data {
	int point, id;
	bool operator<(const Data& r)const {
		if (point != r.point) return point > r.point;
		return id < r.id;
	}
};

int N, db[MAXN];
vector<int> adj[MAXN];

void add(int a, int b) {
	adj[a].push_back(b), adj[b].push_back(a);
}

struct Edge {
	int to, chon;
};

Edge que[MAXN];
int head, tail;
bool visit[MAXN];
int bfs(int s) {
	int ret = 0, p;
	for (int i = 0; i < N; i++) visit[i] = false;
	head = tail = 0;
	que[tail++] = { s,0 };
	visit[s] = true;
	while (head < tail) {
		Edge cur = que[head++];
		p = db[cur.to];
		if (cur.chon == 0) ret += p * 10;
		else if (cur.chon == 1) ret += p * 5;
		else if (cur.chon == 2) ret += p * 3;
		else ret += p * 2;
		for (int nx : adj[cur.to]) {
			if (visit[nx]) continue;
			if (cur.chon + 1 <= 3) {
				que[tail++] = { nx, cur.chon + 1 };
				visit[nx] = true;
			}
		}
	}
	return ret;
}
set<Data>::iterator iter[20003];
set<Data> S;
void init(int N, int mPurchasingPower[20000], int M, int mFriend1[20000], int mFriend2[20000]) {
	::N = N;
	for (int i = 0; i < N; i++)
		db[i] = mPurchasingPower[i], adj[i].clear();
	for (int i = 0; i < M; i++)
		add(mFriend1[i], mFriend2[i]);
	S.clear();
	for (int i = 0; i < N; i++)
		iter[i] = S.insert({ bfs(i), i }).first;
}

int influencer(int mRank) {
	return next(S.begin(), mRank - 1)->id;
}

int bfs2(int s, int power) {
	int ret = 0;
	for (int i = 0; i < N; i++) visit[i] = false;
	head = tail = 0;
	que[tail++] = { s,0 };
	visit[s] = true;
	while (head < tail) {
		auto cur = que[head++];
		int sum = iter[cur.to]->point;
		if (cur.chon == 0) sum += power * 10;
		else if (cur.chon == 1) sum += power * 5;
		else if (cur.chon == 2) sum += power * 3;
		else sum += power * 2;
		S.erase(iter[cur.to]);
		iter[cur.to] = S.insert({ sum, cur.to }).first;
		for (int nx : adj[cur.to]) {
			if (visit[nx]) continue;
			if (cur.chon + 1 <= 3) {
				que[tail++] = { nx, cur.chon + 1 };
				visit[nx] = true;
			}
		}
	}
	return iter[s]->point;
}

int addPurchasingPower(int mID, int mPower) {
	db[mID]+= mPower;
	int ret = bfs2(mID, mPower);
	return ret;
}

int bfs(int s1, int s2) {
	vector<int> t;
	for (int i = 0; i < N; i++) visit[i] = false;
	head = tail = 0;
	que[tail++] = { s1,0 }, que[tail++] = { s2,0 };
	visit[s1] = true, visit[s2] = true;
	while (head < tail) {
		auto cur = que[head++];
		t.push_back(cur.to);
		S.erase(iter[cur.to]);
		for (int nx : adj[cur.to]) {
			if (visit[nx]) continue;
			if (cur.chon + 1 <= 3) {
				que[tail++] = { nx, cur.chon + 1 };
				visit[nx] = true;
			}
		}
	}
	for (int nx : t)
		iter[nx] = S.insert({ bfs(nx), nx }).first;
	return iter[s1]->point + iter[s2]->point;
}

int addFriendship(int mID1, int mID2) {
	add(mID1, mID2);
	int ret = bfs(mID1, mID2);
	return ret;
}
#endif // 1
```
