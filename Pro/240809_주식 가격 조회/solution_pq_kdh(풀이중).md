```cpp
#if 1 // 9300 ms
#define _CRT_SECURE_NO_WARNINGS
#include <string>
#include <vector>
#include <queue>
#include <unordered_map>
#include <algorithm>
using namespace std;

#define COMMON 0
#define PREFERRED 1
struct Info {
	int bizCode, type;
	bool removed;
};
unordered_map<int, Info> idMap;
struct MaxDB {
	int stockCode, price;
	bool operator<(const MaxDB& r)const {
		return price == r.price ? stockCode < r.stockCode : price < r.price;
	}
}; 
struct MinDB {
	int stockCode, price;
	bool operator<(const MinDB& r)const {
		return price == r.price ? stockCode > r.stockCode : price > r.price;
	}
};
priority_queue<MaxDB> maxdb[4][2]; // bizCode, type
priority_queue<MinDB> mindb[4][2], tmp, popped; // bizCode, type
void init() {
	for (int i = 1; i <= 3; i++) for (int j = 0; j < 2; j++) maxdb[i][j] = {}, mindb[i][j] = {};
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
	maxdb[bizCode][type].push({ stockCode, price }), mindb[bizCode][type].push({ stockCode, price });
	idMap[stockCode] = { bizCode, type, false};
	while (!maxdb[bizCode][type].empty()) {
		auto cur = maxdb[bizCode][type].top(); maxdb[bizCode][type].pop();
		if (idMap[cur.stockCode].removed) continue;
		maxdb[bizCode][type].push(cur);
		break;
	}
	return maxdb[bizCode][type].top().stockCode;
}

int remove(int mStockCode) {
	idMap[mStockCode].removed = true;
	int bizCode = idMap[mStockCode].bizCode, type = idMap[mStockCode].type;
	while (!mindb[bizCode][type].empty()) {
		auto cur = mindb[bizCode][type].top(); mindb[bizCode][type].pop();
		if (idMap[cur.stockCode].removed) continue;
		mindb[bizCode][type].push(cur);
		break;
	}
	return mindb[bizCode][type].empty() ? -1 : mindb[bizCode][type].top().stockCode;
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
	tmp = {};
	for (int nb : b) {
		for (int nt : t) {
			popped = {};
			while (!mindb[nb][nt].empty()) {
				auto cur = mindb[nb][nt].top(); mindb[nb][nt].pop();
				if (idMap[cur.stockCode].removed) continue;
				popped.push(cur);
				if (cur.price >= price){
					tmp.push(cur); break;
				}
			}
			while (!popped.empty()) mindb[nb][nt].push(popped.top()), popped.pop();
		}
	}
	return tmp.empty() ? -1 : tmp.top().stockCode;
}
#endif // 1

```
