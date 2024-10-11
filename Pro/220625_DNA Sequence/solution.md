```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <string.h>
#include <unordered_map>
#include <map>
using namespace std;

unordered_map<int, string> dMap;
map<string, int> seqMap;

void init(){
	dMap.clear(); seqMap.clear();
}

int addSeq(int mID, int mLen, char mSeq[]){
	if (dMap.count(mID) || seqMap.count(mSeq)) return 0;
	dMap[mID] = mSeq; seqMap[mSeq] = mID;
	return 1;
}

int searchSeq(int mLen, char mBegin[]){
	auto it = seqMap.lower_bound(mBegin);
	int cnt = 0, id;
	while (it != seqMap.end()) {
		if (it->first.substr(0, mLen) != mBegin) break;
		cnt++, id = it->second;
		++it;
	}
	if (cnt == 0) return -1;
	if (cnt == 1) return id;
	return cnt;
}

int eraseSeq(int mID){
	if (!dMap.count(mID)) return 0;
	string st = dMap[mID];
	dMap.erase(mID);
	seqMap.erase(st);
	return 1;
}

int changeBase(int mID, int mPos, char mBase){
	if (!dMap.count(mID)) return 0;
	string st = dMap[mID];
	if (st.size() < mPos + 1 || st[mPos] == mBase) return 0;
	st[mPos] = mBase;
	if (seqMap.count(st)) return 0;
	eraseSeq(mID);
	char c[61];
	strcpy(c, st.c_str());
	addSeq(mID, st.size(), c);
	return 1;
}
```
