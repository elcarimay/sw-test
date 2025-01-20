```cpp
#if 1
#define _CRT_SECURE_NO_WARNINGS
#include <unordered_map>
#include <vector>
#include <string>
#include <string.h>
using namespace std;

#define EMPTY 0
#define ADDED 1
#define REMOVED -1

struct DB {
	char name[10], tel[10];
	int state;
}db[13003];

#define MAX_N			(5)
#define MAX_L			(8)

unordered_map<string, int> dbMap;
unordered_map<string, int> logMap;
int dbCnt, logCnt;

vector<DB> lg[13003*8];

void init() {
	dbCnt = logCnt = 0, dbMap.clear(), logMap.clear();
	for (int i = 0; i < 13000; i++) db[i] = {};
	for (int i = 0; i < 13000 * 8; i++) lg[i].clear();
	return;
}

int getID(char prefix[]) {
	if (!logMap.count(prefix)) return logMap[prefix] = logCnt++;
	return logMap[prefix];
}

void add(char mName[], char mTelephone[]) {
	int dbIdx = dbCnt++;
	strcpy(db[dbIdx].name, mName);
	strcpy(db[dbIdx].tel, mTelephone);
	db[dbIdx].state = ADDED;
	dbMap[mTelephone] = dbIdx;
	if (strlen(mName)) dbMap[mName] = dbIdx;

	int logIdx;
	char prefix[10];
	DB tmp;
	strcpy(tmp.name, mName);
	strcpy(tmp.tel, mTelephone);
	tmp.state = ADDED;
	for (int i = 1; i <= strlen(mName); i++) {
		strncpy_s(prefix, mName, i);
		logIdx = getID(prefix);
		lg[logIdx].push_back(tmp);
	}
	for (int i = 1; i <= strlen(mTelephone); i++) {
		strncpy_s(prefix, mTelephone, i);
		logIdx = getID(prefix);
		lg[logIdx].push_back(tmp);
	}
}

void remove(char mStr[]) {
	db[dbMap[mStr]].state = REMOVED;
}

void call(char mTelephone[]) {
	DB tmp;
	strcpy(tmp.tel, mTelephone);
	tmp.state = ADDED;
	if (!dbMap.count(mTelephone)) { // db에 없을때
		strcpy(tmp.name, "");
		char prefix[10];
		int dbIdx = dbCnt++;
		strcpy(db[dbIdx].name, tmp.name);
		strcpy(db[dbIdx].tel, tmp.tel);
		db[dbIdx].state = ADDED;
		dbMap[tmp.tel] = dbIdx;
		if (strlen(tmp.name)) dbMap[tmp.name] = dbIdx;
	}
	else strcpy(tmp.name, db[dbMap[mTelephone]].name); // db에 있을때
	int logIdx;
	char prefix[20];
	for (int i = 1; i <= strlen(tmp.name); i++) {
		strncpy_s(prefix, tmp.name, i);
		logIdx = getID(prefix);
		lg[logIdx].push_back(tmp);
	}
	for (int i = 1; i <= strlen(tmp.tel); i++) {
		strncpy_s(prefix, tmp.tel, i);
		logIdx = getID(prefix);
		lg[logIdx].push_back(tmp);
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
	for (vector<DB>::reverse_iterator it = log.rbegin(); it != log.rend(); it++) {
		if (db[dbMap[it->tel]].state == REMOVED) continue;
		if (strlen(it->name) && db[dbMap[it->name]].state == REMOVED) continue;
		bool duplicated = false;
		for (int j = 0; j < ret.size; j++) {
			if (!strcmp(ret.mTelephoneList[j], it->tel)) {
				duplicated = true; break;
			}
		}
		if (duplicated) continue;
		strcpy(ret.mNameList[ret.size], it->name);
		strcpy(ret.mTelephoneList[ret.size], it->tel);
		ret.size++;
		if (ret.size == 5) break;
	}
	return ret;
}
#endif // 1
```
