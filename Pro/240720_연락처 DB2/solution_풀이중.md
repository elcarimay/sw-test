```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <unordered_map>
#include <vector>
using namespace std;

struct DB {
	char name[10], tel[10];
	bool state;
}db[3003];

#define MAX_N			(5)
#define MAX_L			(8)

struct Result{
	int size;
	char mNameList[MAX_N][MAX_L + 1];
	char mTelephoneList[MAX_N][MAX_L + 1];
};

unordered_map<string, vector<int>> idMap;
int idCnt;

vector<int> lg;

void init(){
	idCnt = 0, idMap.clear();
	return;
}

void add(char mName[], char mTelephone[]){
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

void remove(char mStr[]){
	auto& v = idMap[mStr];
	for (auto id : v) {
		db[id].state = false;
	}
}

void call(char mTelephone[]){
	auto& v = idMap[mTelephone];
	for (auto id : v) {
		lg.push_back(id);
	}
}

Result search(char mStr[]){
	Result ret;
	ret.size = -1;
	auto& v = idMap[mStr];

	return ret;
}
```
