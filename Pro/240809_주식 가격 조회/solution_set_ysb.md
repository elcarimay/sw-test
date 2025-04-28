```cpp
#if 1 // 277 ms
#define _CRT_SECURE_NO_WARNINGS
#include <string>
#include<set>
#include<unordered_map>
using namespace std;
using pii = pair<int, int>;

struct Stock {
	int bizCode;	string type;
	set<pii>::iterator it;
};

char delim[] = "=[]{},";
char delim1[] = "=[]{},;";

unordered_map<int, Stock> hmap;	// hmap[stockCode] = {bizCode, type}
set<pii> priority[4][2];		// priority[bizcode][type] = { price, stockCode }

void init() {
	hmap.clear();
	for (int i = 1; i < 4; i++) for (int j = 0; j < 2; j++) priority[i][j].clear();
}
//parsing format: [PRICE]={1500},[STOCKCODE]={200},[BIZCODE]={1},[TYPE]={preferred} 200
int add(char mStockInfo[]) {
	int res = -1, price, stockCode;
	Stock s;
	char* p = strtok(mStockInfo, delim);
	while (p) {
		if (p[0] == 'P') { p = strtok(nullptr, delim); price = atoi(p); }
		else if (p[0] == 'S') { p = strtok(nullptr, delim); stockCode = atoi(p); }
		else if (p[0] == 'B') { p = strtok(nullptr, delim);	s.bizCode = atoi(p); }
		else if (p[0] == 'T') { p = strtok(nullptr, delim);	s.type = p; }
		p = strtok(nullptr, delim);
	}
	int type = s.type == "common" ? 0 : 1;
	auto it = priority[s.bizCode][type].insert({ price, stockCode }).first;
	hmap[stockCode] = { s.bizCode, s.type, it };
	return res = priority[s.bizCode][type].rbegin()->second;
}

int remove(int mStockCode) {
	if (!hmap.count(mStockCode)) return -1;
	Stock s = hmap[mStockCode];
	int type = s.type == "common" ? 0 : 1;
	priority[s.bizCode][type].erase(s.it);
	hmap.erase(mStockCode);
	return priority[s.bizCode][type].empty() ? -1 : priority[s.bizCode][type].begin()->second;;
}
//parsing format: [TYPE] = { common,preferred }; [PRICE] = { 3000 }; [BIZCODE] = { 2,3 } 100
int search(char mCondition[]) {
	int price, bizCode[3] = { 0 };
	int bCnt = 0, tCnt = 0;
	string s[2];
	char* p = strtok(mCondition, delim1);
	while (p) {
		if (p[0] == 'P') { p = strtok(nullptr, delim1); price = atoi(p); p = strtok(nullptr, delim1); }
		else if (p[0] == 'B') {
			p = strtok(nullptr, delim1);
			while ((p[0] == '1' || p[0] == '2' || p[0] == '3')) {
				bizCode[bCnt++] = atoi(p);	p = strtok(nullptr, delim1);
				if (p == NULL) break;
			}
		}
		else if (p[0] == 'T') {
			p = strtok(nullptr, delim1);
			while ((p[0] == 'c' || p[0] == 'p')) {
				s[tCnt++] = p; p = strtok(nullptr, delim1);
				if (p == NULL) break;
			}
		}
	}
	pii res = { 300001, -1 };
	for (int i = 0; i < bCnt; i++) {
		for (int j = 0; j < tCnt; j++) {
			int type = s[j] == "common" ? 0 : 1;
			auto it = priority[bizCode[i]][type].lower_bound({ price, 0 });
			if (it != priority[bizCode[i]][type].end()) res = min(res, *it);
		}
	}
	return res.second;
}
#endif // 0 // 277 ms

```
