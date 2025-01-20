```cpp
#if 1
#define _CRT_SECURE_NO_WARNINGS
#include <unordered_map>
#include <vector>
#include <string>
#include <string.h>
using namespace std;

#define MAX_N			(5)
#define MAX_L			(8)

struct DB {
	char name[MAX_L + 1], tel[MAX_L + 1];
	bool REMOVED; // false: ADDED, true: REMOVED
}db[13003];

unordered_map<string, int> dbMap;
unordered_map<string, int> logMap;
int dbCnt, logCnt;

vector<int> lg[13000*2];

void init() {
	dbCnt = logCnt = 0, dbMap.clear(), logMap.clear();
	for (int i = 0; i < 13000; i++) db[i] = {};
	for (int i = 0; i < 13000*2; i++) lg[i].clear();
}

int getID(char prefix[]) {
	if (!logMap.count(prefix)) return logMap[prefix] = logCnt++;
	return logMap[prefix];
}

void add(char mName[], char mTelephone[]) {
	int dbIdx = dbMap[mTelephone] = dbMap[mName] = dbCnt++;
	strcpy(db[dbIdx].name, mName);
	strcpy(db[dbIdx].tel, mTelephone);

	int logIdx;
	char prefix[MAX_L + 1];
	for (int i = 1; i <= strlen(mName); i++) {
		strncpy_s(prefix, mName, i);
		logIdx = getID(prefix);
		lg[logIdx].push_back(dbIdx);
	}
	for (int i = 1; i <= strlen(mTelephone); i++) {
		strncpy_s(prefix, mTelephone, i);
		logIdx = getID(prefix);
		lg[logIdx].push_back(dbIdx);
	}
}

void remove(char mStr[]) {
	db[dbMap[mStr]].REMOVED = true;
}

void call(char mTelephone[]) {
	int dbIdx;
	if (!dbMap.count(mTelephone)) { // db에 없을때
		dbIdx = dbMap[mTelephone] = dbCnt++;
		strcpy(db[dbIdx].name, "");
		strcpy(db[dbIdx].tel, mTelephone);
	}
	else
		dbIdx = dbMap[mTelephone];// db에 있을때
	char prefix[MAX_L + 1];
	for (int i = 1; i <= strlen(db[dbIdx].name); i++) {
		strncpy_s(prefix, db[dbIdx].name, i);
		lg[getID(prefix)].push_back(dbIdx);
	}
	for (int i = 1; i <= strlen(mTelephone); i++) {
		strncpy_s(prefix, mTelephone, i);
		lg[getID(prefix)].push_back(dbIdx);
	}
}

struct Result {
	int size;
	char mNameList[MAX_N][MAX_L + 1];
	char mTelephoneList[MAX_N][MAX_L + 1];
};

Result search(char mStr[]) {
	Result ret = {};
	if (!logMap.count(mStr)) return ret;

	auto& log = lg[logMap[mStr]];
	for (vector<int>::reverse_iterator it = log.rbegin(); it != log.rend(); it++) {
		auto& d = db[*it];
		if (d.REMOVED || strlen(d.name) && d.REMOVED) continue;
		bool duplicated = false;
		for (int j = 0; j < ret.size; j++)
			if (!strcmp(ret.mTelephoneList[j], d.tel)) {
				duplicated = true; break;
			}
		if (duplicated) continue;
		strcpy(ret.mNameList[ret.size], d.name);
		strcpy(ret.mTelephoneList[ret.size], d.tel);
		if (++ret.size == 5) break;
	}
	return ret;
}
#endif // 1
```
