```cpp
#if 1
#define _CRT_SECURE_NO_WARNINGS
#include <unordered_map>
#include <vector>
#include <string>
#include <string.h>
using namespace std;

#define ll long long
#define MAX_N			(5)
#define MAX_L			(8)

struct DB {
	char name[MAX_L + 1], tel[MAX_L + 1];
	bool REMOVED; // false: ADDED, true: REMOVED
}db[13003];

ll getHash(char c[]) {
	ll value = 0;
	for (int i = 0; c[i]; i++) {
		value *= 26;
		value += c[i];
	}
	return value;
}

unordered_map<ll, int> dbMap, logMap;
int dbCnt, logCnt;

vector<int> lg[13000 * 2];

void init() {
	dbCnt = logCnt = 0, dbMap.clear(), logMap.clear();
	for (int i = 0; i < 13000; i++) db[i] = {};
	for (int i = 0; i < 13000 * 2; i++) lg[i].clear();
}

int getID(char prefix[]) {
	ll value = getHash(prefix);
	if (!logMap.count(value)) return logMap[value] = logCnt++;
	return logMap[value];
}

void add(char mName[], char mTelephone[]) {
	ll nv = getHash(mName), tv = getHash(mTelephone);
	int dbIdx = dbMap[nv] = dbMap[tv] = dbCnt++;
	strcpy(db[dbIdx].name, mName);
	strcpy(db[dbIdx].tel, mTelephone);

	char prefix[MAX_L + 1];
	for (int i = 1; i <= strlen(mName); i++) {
		strncpy_s(prefix, mName, i);
		lg[getID(prefix)].push_back(dbIdx);
	}
	for (int i = 1; i <= strlen(mTelephone); i++) {
		strncpy_s(prefix, mTelephone, i);
		lg[getID(prefix)].push_back(dbIdx);
	}
}

void remove(char mStr[]) {
	db[dbMap[getHash(mStr)]].REMOVED = true;
}

void call(char mTelephone[]) {
	ll value = getHash(mTelephone);
	int dbIdx;
	if (!dbMap.count(value)) { // db에 없을때
		dbIdx = dbMap[value] = dbCnt++;
		strcpy(db[dbIdx].name, "");
		strcpy(db[dbIdx].tel, mTelephone);
	}
	else
		dbIdx = dbMap[value];// db에 있을때
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
	ll value = getHash(mStr);
	if (!logMap.count(value)) return ret;

	for (vector<int>::reverse_iterator it = lg[logMap[value]].rbegin(); it != lg[logMap[value]].rend(); it++) {
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
