```cpp
#if 1 // 500 ms
// color, seat, type, size로 hash키를 만들고 set으로 chainning하였음
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
	set<pii> date; // start, end
}car[MAX_N];

set<pii> S[MAX_N];

void init(int N) {
	for (int i = 0; i <= N; i++) carList[i].clear();
	for (auto& s:S) s.clear();
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

int rent(int mCondition[]) {
	int s = mCondition[0], e = mCondition[1], hash = gethash(mCondition + 2);
	
	for (pii p : S[hash]) { // price, companyid
		int carid = p.second;
		auto& date = car[carid].date;
		auto it = date.lower_bound({ s,e }); // 요청날짜 바로 앞뒤 예약 날짜만 확인
		if (car[carid].number <= 0 || car[carid].price == 0) continue;
		if (it != date.end() && !(e <= it->first || it->second <= s)) continue;
		if (it != date.begin()) {
			--it;
			if (!(e <= it->first || it->second <= s))continue;
			
		}
		car[carid].number--;
		date.insert({ s, e });
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
