```cpp
#include <vector>
#include <algorithm>
#include <unordered_map>
using namespace std;

int L, R, GN;

vector<int> G[105][105]; // g[x][y] = {hid} 그룹을 100*100으로 나누고 그룹당 hid등록
unordered_map<int, int> idmap; // key: mid, value: hid
int idcnt;

vector<int> village[25005]; // village[vid] = {hid}
int vcnt;
int minmid[25005]; // minHid[vid] = vid 마을의 mid 최소값

struct House {
	int x, y, vid, mid;
}house[25005];

void init(int L, int R) {
	::L = L, ::R = R, GN = R / L;
	vcnt = idcnt = 0;
	for (auto& v : village) v.clear();
	for (int i = 0; i <= GN; i++) {
		for (int j = 0; j <= GN; j++)
			G[i][j].clear();
	}
}

void unionVillage(int a, int b) {
	if (a == b) return;
	if (village[a].size() > village[b].size()) swap(a, b);
	vcnt--;
	minmid[b] = min(minmid[b], minmid[a]);
	for (int hid : village[a]) {
		village[b].push_back(hid);
		house[hid].vid = b;
	}
}

int add(int mId, int mX, int mY) {
	int hid = idmap[mId] = idcnt++;
	auto&h = house[hid] = { mX, mY, hid, mId };
	vcnt++;
	village[hid].push_back(hid);
	minmid[hid] = mId;

	int sX = max(0, mX / L - 1), sY = max(0, mY / L - 1);
	int eX = min(GN, mX / L + 1), eY = min(GN, mY / L + 1);
	
	for (int i = sX; i <= eX; i++)
		for (int j = sY; j <= eY; j++) {
			for (int hid2 : G[i][j]) {
				auto& h2 = house[hid2];
				if (abs(h.x - h2.x) + abs(h.y - h2.y) <= L)
					unionVillage(h.vid, h2.vid);
			}
		}
	G[mX / L][mY / L].push_back(hid);
	return village[h.vid].size();
}

int remove(int mId) {
	if (idmap.count(mId) == 0) return -1;
	int hid = idmap[mId];
	auto& h = house[hid];
	int vid = h.vid;
	auto& v = village[vid];
	auto& g = G[h.x / L][h.y / L];

	idmap.erase(mId);
	g.erase(find(g.begin(), g.end(), hid));
	v.erase(find(v.begin(), v.end(), hid));
	if (minmid[vid] == mId) {
		minmid[vid] = 1e9;
		for (int hid : v) minmid[vid] = min(minmid[vid], house[hid].mid);
	}
	if (v.empty()) vcnt--;
	return v.size();
}

int check(int mId) {
	if (idmap.count(mId) == 0) return -1;
	int hid = idmap[mId];
	return minmid[house[hid].vid];
}

int count() {
	return vcnt;
}
```
