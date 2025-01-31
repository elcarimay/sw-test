```cpp
#if 1
#include <vector>
#include <queue>
#include <unordered_map>
using namespace std;

struct Restaurant {
	int x, y;
}rest[6003];

struct Data {
	int cnt, d, mid;
	bool operator<(const Data& r)const {
		if (cnt != r.cnt) return cnt < r.cnt;
		if (d != r.d) return d > r.d;
		return mid > r.mid;
	}
};

struct User {
	int x, y;
	int o[6003]; // order count
	int t[6003]; // total count
}u[28];
priority_queue<Data> pq[28];
bool f[28][28]; // friend
unordered_map<int, int> idMap;
int idCnt;

int dist(int uid, int pid) {
	return abs(u[uid].x - rest[pid].x) + abs(u[uid].y - rest[pid].y);
}

int N;
void init(int N, int px[], int py[]) {
	::N = N, idCnt = 0; idMap.clear();
	memset(f, 0, sizeof(f));
	for (int i = 0; i < 6000; i++) rest[i] = {};
	for (int i = 0; i < N; i++)
		u[i] = { px[i], py[i] }, pq[i] = {};
}

void addRestaurant(int pID, int x, int y) {
	int id = idMap[pID] = idCnt++;
	rest[id] = { x, y };
	for (int i = 0; i < N; i++)
		pq[i].push({ 0, dist(i, id), pID });
}

void removeRestaurant(int pID) {
	idMap.erase(pID);
}

void order(int uID, int pID) {
	int id = idMap[pID];
	u[uID].o[id]++, u[uID].t[id]++;
	pq[uID].push({ u[uID].t[id], dist(uID, id), pID });
	for (int i = 0; i < N; i++) {
		if (!f[uID][i]) continue;
		u[i].t[id]++;
		pq[i].push({u[i].t[id], dist(i, id), pID});
	}
}

void beFriend(int uID1, int uID2) {
	f[uID1][uID2] = f[uID2][uID1] = true;
	for (auto id : idMap) {
		if (u[uID1].o[id.second]) {
			u[uID2].t[id.second] += u[uID1].o[id.second];
			pq[uID2].push({ u[uID2].t[id.second], dist(uID2, id.second), id.first });
		}
		if (u[uID2].o[id.second]) {
			u[uID1].t[id.second] += u[uID2].o[id.second];
			pq[uID1].push({ u[uID1].t[id.second], dist(uID1, id.second), id.first });
		}
	}
}

int recommend(int uID) {
	int cnt = 1;
	vector<Data> tmp;
	while (!pq[uID].empty()) {
		Data cur = pq[uID].top(); pq[uID].pop();
		if (!idMap.count(cur.mid)) continue;
		if (u[uID].t[idMap[cur.mid]] != cur.cnt) continue;
		tmp.push_back(cur);
		if (cnt++ == 10) {
			for (auto nx : tmp) pq[uID].push(nx);
			return cur.mid;
		}
	}
}
#endif // 0

```
