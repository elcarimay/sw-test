```cpp
#include <algorithm>
#include <vector>
#include <unordered_map>
using namespace std;
 
struct HOUSE {
	int x, y;
	bool isRemoved;
};
unordered_map<int, HOUSE> houseInfo;
unordered_map<int, int> mIdTOindex;
struct DSU {
	vector<int> town, houseCnt, rank;
	int totalTownCnt;
 
	void init() {
		town.clear();
		houseCnt.clear();
		rank.clear();
		totalTownCnt = 0;
	}
 
	void addHouse() {
		int townID = town.size();
		town.push_back(townID);
		houseCnt.push_back(1);
		rank.push_back(0);
		totalTownCnt++;
	}
	
	int find(int a) {
		if (town[a] != a)
			town[a] = find(town[a]);
		return town[a];
	}
 
	void unionSet(int a, int b) {
		int townA = find(a);
		int townB = find(b);
 
		if (townA != townB) {
			if (rank[townA] < rank[townB])
				swap(townA, townB);
			town[townB] = townA;
			if (rank[townA] == rank[townB])
				rank[townA]++;
			houseCnt[townA] += houseCnt[townB];
			totalTownCnt--;
		}
	}
	void deleteHouse(int houseID) {
		int townID = find(houseID);
		houseCnt[townID]--;
		houseInfo[houseID].isRemoved = true;
		if (houseCnt[townID] == 0)
			totalTownCnt--;
	}
}dsu;
int L, R, P_CNT;
vector<vector<vector<int>>> P_map;
void init(int L, int R) {
	::L = L;
	::R = R;
	P_CNT = (R + L - 1) / L;
	P_map.clear();
	P_map.resize(P_CNT, vector<vector<int>>(P_CNT));
	houseInfo.clear();
	mIdTOindex.clear();
	dsu.init();
	return;
}
 
int add(int mId, int mX, int mY) {
	int mIdx = houseInfo.size();
	dsu.addHouse();
	mIdTOindex.insert({ mId, mIdx });		//이건 erase로 진짜 지워줘야 할듯
	HOUSE newHouse = { mX, mY, false };
	houseInfo.insert({ mIdx, newHouse });	//이건 flag를 사용해서 삭제 여부 기록하고
 
	int pX = (mX - 1) / L;
	int pY = (mY - 1) / L;
	int sX = (mX - 1 - L) < 0 ? 0 : (mX - 1 - L) / L;
	int sY = (mY - 1 - L) < 0 ? 0 : (mY - 1 - L) / L;
	int eX = (mX - 1 + L) >= R ? P_CNT - 1 : (mX - 1 + L) / L;
	int eY = (mY - 1 + L) >= R ? P_CNT - 1 : (mY - 1 + L) / L;
	for (int x = sX; x <= eX; x++)	{
		for (int y = sY; y <= eY; y++){
			for (auto& homeID : P_map[x][y]) {
				if (!houseInfo[homeID].isRemoved) {
					HOUSE orgHouse = houseInfo[homeID];
					int distance = abs(orgHouse.x - mX) + abs(orgHouse.y - mY);
					if (distance <= L)			// 두 집이 마을이 되는 경우( 거리가 L 이하)
						dsu.unionSet(homeID, mIdx);
				}
			}
		}
	}
	P_map[pX][pY].push_back(mIdx);
	int townID = dsu.find(mIdx);
	int ret = dsu.houseCnt[townID];
	return ret;
}
int remove(int mId) {
	auto ret1 = mIdTOindex.find(mId);
	if (ret1 != mIdTOindex.end()) {
		int townId = dsu.find(ret1->second);
		int ret = dsu.houseCnt[townId] - 1;
		dsu.deleteHouse(ret1->second);
		mIdTOindex.erase(mId);
		return ret;
	}
	return -1;
}
int check(int mId) {
	int ret = 0x7fffffff;
	auto ret1 = mIdTOindex.find(mId);
	if (ret1 != mIdTOindex.end()) {
		int townId = dsu.find(ret1->second);
		for (auto& house : mIdTOindex) {
			if (townId == dsu.find(house.second))
				ret = min(ret, house.first);
		}
		return ret;
	}
	return -1;
}
int count() {
	int ret = dsu.totalTownCnt;
	return ret;
}
```
