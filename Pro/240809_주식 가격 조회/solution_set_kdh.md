```cpp
#if 1 // 177 ms
#define _CRT_SECURE_NO_WARNINGS
#include <string>
#include <queue>
#include <unordered_map>
#include <algorithm>
#include <set>
using namespace std;

#define COMMON 0
#define PREFERRED 1
struct Info {
	int bizCode, type, price;
};
unordered_map<int, Info> idMap;
struct DB {
	int price, stockCode;
	bool operator<(const DB& r)const {
		return price == r.price ? stockCode < r.stockCode : price < r.price;
	}
}; 
set<DB> db[4][2]; // bizCode, type
void init() {
	for (int i = 1; i <= 3; i++) for (int j = 0; j < 2; j++) db[i][j].clear();
	idMap.clear();
}
char delim[] = "[]={},;";
int add(char mStockInfo[]) {
	int stockCode = 0, bizCode = 0, type = 0, price = 0;
	char* p = strtok(mStockInfo, delim);
	while (p) {
		if (p[0] == 'S') {
			p = strtok(nullptr, delim);
			for (int i = 0; p[i]; i++) stockCode *= 10, stockCode += p[i] - 48;
		}
		else if (p[0] == 'B') {
			p = strtok(nullptr, delim);
			for (int i = 0; p[i]; i++) bizCode *= 10, bizCode += p[i] - 48;
		}
		else if (p[0] == 'T') {
			p = strtok(nullptr, delim);
			type = !strcmp(p, "common") ? COMMON : PREFERRED;
		}
		else if (p[0] == 'P') {
			p = strtok(nullptr, delim);
			for (int i = 0; p[i]; i++) price *= 10, price += p[i] - 48;
		}
		p = strtok(nullptr, delim);
	}
	db[bizCode][type].insert({ price, stockCode});
	idMap[stockCode] = { bizCode, type, price };
	return db[bizCode][type].rbegin()->stockCode;
}

int remove(int mStockCode) {
	auto& i = idMap[mStockCode];
	int bizCode = i.bizCode, type = i.type, price = i.price;
	db[bizCode][type].erase({ price, mStockCode });
	return db[bizCode][type].empty() ? -1 : db[bizCode][type].begin()->stockCode;
}

int search(char mCondition[]) {
	vector<int> b, t;
	char* p = strtok(mCondition, delim);
	int bizCode = 0, type = 0, price = 0;
	while (p) {
		if (p[0] == 'B') {
			p = strtok(nullptr, delim);
			while (p && p[0] != 'B' && p[0] != 'T' && p[0] != 'P') {
				b.push_back(p[0] - 48);
				p = strtok(nullptr, delim);
			}
		}
		else if (p[0] == 'T') {
			p = strtok(nullptr, delim);
			while (p && p[0] != 'B' && p[0] != 'T' && p[0] != 'P') {
				type = !strcmp(p, "common") ? COMMON : PREFERRED;
				t.push_back(type);
				p = strtok(nullptr, delim);
			}
		}
		else if (p[0] == 'P') {
			p = strtok(nullptr, delim);
			for (int i = 0; p[i]; i++) price *= 10, price += p[i] - 48;
			if(p) p = strtok(nullptr, delim);
		}
	}
	int rp = 300001, rs = -1; // price와 stockCode를 전부 비교해야 함
	for (int nb : b) {
		for (int nt : t) {
			if (!db[nb][nt].empty()) {
				auto it = db[nb][nt].lower_bound({ price });
				if (it != db[nb][nt].end()) {
					if (rp > it->price) rp = it->price, rs = it->stockCode;
					else if(rp == it->price && rs > it->stockCode) rp = it->price, rs = it->stockCode;
				}
			}
		}
	}
	return rs;
}
#endif // 1
```
