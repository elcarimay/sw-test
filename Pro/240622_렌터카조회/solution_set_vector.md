```cpp
#if 1 // 300 ms
// color, seat, type, size로 hash키를 만들고 set으로 chainning하였음
// set<pii> S[hash]에다 price, carid를 입력
#include<vector>
#include<iostream>
#include<queue>
#include<set>
#include<algorithm>
using namespace std;
using pii = pair<int, int>;

#define MAX_N 100001

vector<int> carList[1003];

struct CarInfo {
	int hash, number, price;
	set<pii>::iterator it; // price, companyid
	vector<pii> date; // start, end
}car[MAX_N];

set<pii> S[MAX_N];

void init(int N) {
	for (int i = 0; i <= N; i++) carList[i].clear();
	for (auto& s : S) s.clear();
}

int gethash(int* a) { // color, seat, type, size
	return (a[0] - 1) * 1000 + (a[1] - 2) * 100 + a[2] * 10 + a[3];
}

void add(int mCarID, int mCompanyID, int mCarInfo[]) {
	carList[mCompanyID].push_back(mCarID);
	int hash = gethash(mCarInfo);
	car[mCarID] = { hash, mCarInfo[4], mCarInfo[5]
		, S[hash].insert({mCarInfo[5], mCarID}).first };
}

bool overlap(int carid, int s, int e) {
	for (pii d : car[carid].date)
		if ((d.second <= s || e <= d.first) == 0) return 1;
	return 0;
}

int rent(int mCondition[]) {
	int s = mCondition[0], e = mCondition[1], hash = gethash(mCondition + 2);

	for (pii p : S[hash]) { // price, carid
		int carid = p.second;
		auto& c = car[carid];
		if (overlap(carid, s, e)) continue;
		if (c.price <= 0 || c.number <= 0) continue;
		c.number--;
		c.date.push_back({ s, e });
		return carid;
	}
	return -1;
}

int promote(int mCompanyID, int mDiscount) {
	int res = 0;
	for (int mCarID : carList[mCompanyID]) {
		auto& c = car[mCarID];
		if (c.price == 0 || c.number <= 0) continue;
		c.price -= mDiscount;
		if (c.price < 0) c.price = 0;
		S[c.hash].erase(c.it);
		c.it = S[c.hash].insert({ c.price, mCarID }).first;
		res += c.price;
	}
	return res;
}
#endif  

```
