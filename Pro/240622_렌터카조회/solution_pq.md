```cpp
#if 1 // 429 ms
#include<vector>
#include<iostream>
#include<queue>
#include<set>
#include<algorithm>
#include<unordered_map>
using namespace std;
using pii = pair<int, int>;

#define MAX_N 100001


unordered_map<int, vector<int>> carList; // companyid, carid

struct Car {
	int mCarID, number, price;
	bool operator<(const Car& r)const {
		if (price != r.price) return price > r.price;
		return mCarID > r.mCarID;
	}
}cars[MAX_N];

struct CarInfo {
	int color, seat, type, size;
}carInfo[MAX_N];;

priority_queue<Car> carMap[MAX_N];
vector<pii> booking[MAX_N]; // start, end

bool overlap(int mCarID, int start, int end) {
	for (const auto& date : booking[mCarID])
		if (end > date.first && date.second > start) return false;
	return true;
}

void init(int N) {
	for (int i = 0; i <= N; i++) carList[i].clear();
	for (int i = 0; i < MAX_N; i++) booking[i].clear(), carMap[i] = {};
}

int encode(const CarInfo& info) {
	return (info.color - 1) * 1000 + (info.seat - 2) * 100 + info.type * 10 + info.size;
}

void add(int mCarID, int mCompanyID, int mCarInfo[]) {
	carList[mCompanyID].push_back(mCarID);
	cars[mCarID] = { mCarID, mCarInfo[4], mCarInfo[5] };
	carInfo[mCarID] = { mCarInfo[0],mCarInfo[1] ,mCarInfo[2],mCarInfo[3] };
	carMap[encode(carInfo[mCarID])].push(cars[mCarID]);
}

int rent(int mCondition[]) {
	int start = mCondition[0], end = mCondition[1];
	CarInfo info = { mCondition[2], mCondition[3], mCondition[4], mCondition[5] };
	auto& pq = carMap[encode(info)];
	vector<int> popped;

	int res = -1;
	while (!pq.empty()) {
		Car car = pq.top(); pq.pop();
		int mCarID = car.mCarID;
		if (car.price != cars[mCarID].price) continue;
		if (car.number == 0) continue;
		if (car.price < 1) continue;
		popped.push_back(mCarID);
		if (overlap(mCarID, start, end)) {
			booking[mCarID].push_back({ start, end });
			cars[mCarID].number--;
			res = mCarID;
			break;
		}
	}
	for (int mCarID : popped) pq.push(cars[mCarID]);
	return res;
}

int promote(int mCompanyID, int mDiscount) {
	int res = 0;
	for (int mCarID : carList[mCompanyID]) {
		if (cars[mCarID].price == 0) continue;
		if (cars[mCarID].number == 0) continue;

		cars[mCarID].price -= mDiscount;
		if (cars[mCarID].price < 0) cars[mCarID].price = 0;
		res += cars[mCarID].price;
		carMap[encode(carInfo[mCarID])].push(cars[mCarID]);
	}
	return res;
}
#endif  

```
