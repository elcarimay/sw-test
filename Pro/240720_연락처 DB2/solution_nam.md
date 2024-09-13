```cpp
#if 1
#define MAX_N			(5)
#define MAX_L			(8)
#define MAX_DB      (13000)         // add 3,000 + call 10,000
#define MAX_LOG     (13000 * 8)     // (add 3,000 + call 10,000) * MAX_L
#define _CRT_SECURE_NO_WARNINGS
#include <string>
#include <cstring>
#include <unordered_map>
#include <vector>
using namespace std;

enum State { EMPTY, ADDED, REMOVED };

struct Result
{
	int size;
	char mNameList[MAX_N][MAX_L + 1];
	char mTelephoneList[MAX_N][MAX_L + 1];
};

struct DB {
	char mName[MAX_L + 1];
	char mTelephone[MAX_L + 1];
	State state;

	DB(const char mName[], const char mTelephone[], State state) {
		strcpy(this->mName, mName);
		strcpy(this->mTelephone, mTelephone);
		this->state = state;
	}
	DB() {
		strcpy(this->mName, "");
		strcpy(this->mTelephone, "");
		this->state = EMPTY;
	}
}db[MAX_DB];
int dbCnt;
unordered_map<string, int> dbMap;

struct LOG {
	char mName[MAX_L + 1];
	char mTelephone[MAX_L + 1];

	LOG(const char mName[], const char mTelephone[]) {
		strcpy(this->mName, mName);
		strcpy(this->mTelephone, mTelephone);
	}
	LOG() {
		strcpy(this->mName, "");
		strcpy(this->mTelephone, "");
	}
};
vector<LOG> logs[MAX_LOG];
int logCnt;
unordered_map<string, int> logMap;

int get_logIndex(const char mStr[]) {
	int idx;
	auto iter = logMap.find(mStr);
	if (iter == logMap.end()) {
		idx = logCnt++;
		logMap.emplace(mStr, idx);
	}
	else idx = iter->second;
	return idx;
}

void add_db(char mName[], char mTelephone[]) {
	int dbIdx = dbCnt++;
	db[dbIdx] = { mName, mTelephone, ADDED };
	dbMap.emplace(mTelephone, dbIdx);
	if (strlen(mName)) dbMap.emplace(mName, dbIdx);
}

void add_log(char mName[], char mTelephone[]) {
	int logIdx;
	char prefix[MAX_L + 1];

	for (int i = 1; i <= strlen(mName); i++) {
		strncpy_s(prefix, mName, i);
		logIdx = get_logIndex(prefix);
		logs[logIdx].push_back({ mName, mTelephone });
	}
	for (int i = 1; i <= strlen(mTelephone); i++) {
		strncpy_s(prefix, mTelephone, i);
		logIdx = get_logIndex(prefix);
		logs[logIdx].push_back({ mName, mTelephone });
	}
}

void init()
{
	for (int i = 0; i < dbCnt; i++) db[i] = {};
	dbCnt = 0;
	dbMap.clear();

	for (int i = 0; i < logCnt; i++) logs[i].clear();
	logCnt = 0;
	logMap.clear();
}

void add(char mName[], char mTelephone[])
{
	add_db(mName, mTelephone);
	add_log(mName, mTelephone);
}

void remove(char mStr[])
{
	db[dbMap[mStr]].state = REMOVED;
}

void call(char mTelephone[])
{
	char mName[MAX_L + 1];
	if (dbMap.find(mTelephone) != dbMap.end())
		strcpy(mName, db[dbMap[mTelephone]].mName);
	else {
		strcpy(mName, "");
		add_db(mName, mTelephone);
	}
	add_log(mName, mTelephone);
}

Result search(char mStr[])
{
	Result ret = {};
	if (logMap.find(mStr) == logMap.end()) return ret;

	auto& log = logs[logMap[mStr]];
	int cnt = 0;
	for (int i = (int)log.size() - 1; i >= 0 && cnt < 5; i--) {
		if (db[dbMap[log[i].mTelephone]].state == REMOVED) continue;
		if (strlen(log[i].mName) && db[dbMap[log[i].mName]].state == REMOVED) continue;
		bool duplicated = false;
		for (int j = 0; j < cnt; j++) {
			if (strcmp(ret.mTelephoneList[j], log[i].mTelephone) == 0) {
				duplicated = true;
				break;
			}
		}
		if (duplicated) continue;
		strcpy(ret.mNameList[cnt], log[i].mName);
		strcpy(ret.mTelephoneList[cnt], log[i].mTelephone);
		ret.size++;
		cnt++;
	}
	return ret;
}
#endif // 1

```
