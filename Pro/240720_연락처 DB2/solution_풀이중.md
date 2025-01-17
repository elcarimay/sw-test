```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <unordered_map>
#include <vector>
#include <string>
using namespace std;

struct DB {
	char name[10], tel[10];
	bool state;
}db[3003];

#define MAX_N			(5)
#define MAX_L			(8)

unordered_map<string, vector<int>> idMap;
int idCnt;

vector<int> lg;

void init() {
	idCnt = 0, idMap.clear();
	return;
}

void add(char mName[], char mTelephone[]) {
	char temp[20];
	int id = idCnt++;
	int ln = strlen(mName), lt = strlen(mTelephone);
	for (int i = 0; i <= ln + lt; i++) {
		if (i >= ln) db[id].tel[i - ln] = temp[i] = mTelephone[i - ln];
		else db[id].name[i] = temp[i] = mName[i];
	}
	db[id].name[ln] = db[id].tel[lt] = '\0', db[id].state = true;
	char s[20];
	for (int i = 1; i <= ln + lt; i++) {
		strncpy_s(s, temp, i);
		idMap[s].push_back(id);
	}
}

void remove(char mStr[]) {
	for (auto id : idMap[mStr]) {
		db[id].state = false;
	}
}

void call(char mTelephone[]) {
	for (auto id : idMap[mTelephone]) {
		lg.push_back(id);
	}
}

struct Result {
	int size;
	char mNameList[MAX_N][MAX_L + 1];
	char mTelephoneList[MAX_N][MAX_L + 1];
};

Result search(char mStr[]) {
	Result ret;
	ret.size = -1;
	auto& v = idMap[mStr];
	int cnt = 0;
	for (vector<int>::reverse_iterator it = v.rbegin(); it != v.rend(); it++) {
		if (!db[*it].state) continue;
		strcpy(ret.mNameList[cnt], db[*it].name);
		strcpy(ret.mTelephoneList[cnt], db[*it].tel);
		cnt++;
		if (cnt == 5) break;
	}
	if (cnt) ret.size = cnt;
	return ret;
}
```
