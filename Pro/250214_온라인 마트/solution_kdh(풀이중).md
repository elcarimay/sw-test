```cpp
#if 1
#include <unordered_map>
#include <vector>
#include <algorithm>
using namespace std;

#define MAXN 50003

struct RESULT{ int cnt, IDs[5];};

unordered_map<int, int> hmap;
struct Info {
	int mid, cat, com, price;
	bool removed;
}info[MAXN];

int idCnt;
struct Data {
	int id;
	bool operator<(const Data& r)const {
		if(info[id].price != info[r.id].price) return info[id].price < info[r.id].price;
		return info[id].mid < info[r.id].mid;
	}
	bool operator==(const Data& r)const {
		return id == r.id;
	}
};
vector<Data> catcom[6][6];
int removedcatcom[6][6];
int getID(int c) {
	return hmap.count(c) ? hmap[c] : hmap[c] = idCnt++;
}
void init(){
	idCnt = 0, hmap.clear();
	for (int i = 1; i < 6; i++) for (int j = 1; j < 6; j++) catcom[i][j].clear(), removedcatcom[i][j] = 0;
}

int sell(int mID, int mCategory, int mCompany, int mPrice){
	int id = getID(mID);
	info[id] = { mID, mCategory, mCompany, mPrice, false };
	catcom[mCategory][mCompany].push_back({ id });
	return (int)catcom[mCategory][mCompany].size() - removedcatcom[mCategory][mCompany];
}

int closeSale(int mID){
	if (!hmap.count(mID)) return -1;
	int id = getID(mID);
	hmap.erase(mID);
	removedcatcom[info[id].cat][info[id].com]++;
	info[id].removed = true;
	return info[id].price;
}

int discount(int mCategory, int mCompany, int mAmount){
	for (auto& nx : catcom[mCategory][mCompany]) {
		int id = nx.id;
		if (!hmap.count(info[id].mid) || info[id].removed) continue;
		if (info[id].price - mAmount <= 0) {
			hmap.erase(info[id].mid); 
			info[id].removed = true;
			removedcatcom[info[id].cat][info[id].com]++;
		}
		else {
			info[id].price -= mAmount;
		}
	}
	return (int)catcom[mCategory][mCompany].size() - removedcatcom[mCategory][mCompany];
}

RESULT show(int mHow, int mCode){
	RESULT res = { 0, '\0' };
	vector<Data> tmp;
	for (int i = 1; i <= 5; i++) for (int j = 1; j <= 5; j++) {
		if (mHow == 1 && i != mCode) continue;
		if (mHow == 2 && j != mCode) continue;
		int ccSize = catcom[i][j].size(), rccSize = removedcatcom[i][j];
		if (!ccSize || (ccSize - rccSize == 0)) continue;
		if (ccSize <= 5 && ccSize > 1)
			sort(catcom[i][j].begin(), catcom[i][j].end());
		else if (ccSize > 5)
			partial_sort(catcom[i][j].begin(), catcom[i][j].begin() + min(ccSize, 5 + rccSize), catcom[i][j].end());
		for (Data& nx : catcom[i][j]) if (!info[nx.id].removed) tmp.push_back({ nx.id });
	}
	//unique(tmp.begin(), tmp.end());
	int size = tmp.size();
	if (size < 5 && size > 1) sort(tmp.begin(), tmp.end());
	else if (size >= 5) partial_sort(tmp.begin(), tmp.begin() + 5, tmp.end());
	if (tmp.size() > 5) tmp.resize(5);
	for (Data& nx : tmp) res.IDs[res.cnt++] = info[nx.id].mid;
	return res;
}
#endif // 1

```
