```cpp
#include <unordered_map>
#include <vector>
#include <queue>
#include <set>
using namespace std;

#define BUY 1
#define SELL 2
#define PRODUCT 3
#define MAXN 30003

struct BuyDB {
	int bid, product, price, quantity, stocks; // stocks 남은 재고
	bool canceled;
}buydb[MAXN];

struct SellDB {
	int bid, quantity;
};

unordered_map<int, int> bidMap, pidMap, sidMap;
int bidCnt, pidCnt, sidCnt, stocks[MAXN]; // pid별 총 제고
vector<SellDB> v[MAXN];
bool refundFlag[MAXN];

int getID(int flag, int c) {
	if(flag == BUY) return bidMap.count(c) ? bidMap[c] : bidMap[c] = bidCnt++;
	if(flag == SELL) return sidMap.count(c) ? sidMap[c] : sidMap[c] = sidCnt++;
	if(flag == PRODUCT) return pidMap.count(c) ? pidMap[c] : pidMap[c] = pidCnt++;
}

struct Data {
	int id, price, stocks;
	bool operator<(const Data& r)const {
		if (price != r.price) {
			return price > r.price;
		}
		return id > r.id;
	}
};
priority_queue<Data> pq[MAXN]; // pid별 우큐
void init() {
	for (int i = 0; i < bidCnt; i++) buydb[i] = {};
	for (int i = 0; i < pidCnt; i++) pq[i] = {}, stocks[i] = 0;
	for (int i = 0; i < sidCnt; i++) v[i].clear(), refundFlag[i] = false;
	bidCnt = sidCnt = pidCnt = 0, bidMap.clear(), sidMap.clear(), pidMap.clear();
}

int buy(int bId, int mProduct, int mPrice, int mQuantity) {
	int bid = getID(BUY, bId), pid = getID(PRODUCT, mProduct);
	buydb[bid] = { bId, mProduct, mPrice, mQuantity, mQuantity, false};
	stocks[pid] += mQuantity;
	pq[pid].push({bId, mPrice, mQuantity });
	return stocks[pid];
}

int cancel(int bId) {
	if (!bidMap.count(bId)) return -1;
	int bid = bidMap[bId]; int pid = pidMap[buydb[bid].product];
	if (buydb[bid].canceled || buydb[bid].quantity != buydb[bid].stocks) return -1;
	if (!buydb[bid].stocks) return -1;
	stocks[pid] -= buydb[bid].stocks;
	buydb[bid].canceled = true;
	return stocks[pid];
}

int sell(int sId, int mProduct, int mPrice, int mQuantity) {
	int sid = getID(SELL, sId), pid = getID(PRODUCT, mProduct);
	if (stocks[pid] < mQuantity || pq[pid].empty()) return -1;
	int ret = 0;
	while (!pq[pid].empty() && mQuantity !=0) {
		Data cur = pq[pid].top(); pq[pid].pop();
		int bid = bidMap[cur.id];
		if (buydb[bid].canceled) continue;
		if (cur.stocks <= mQuantity) {
			v[sid].push_back({ cur.id, cur.stocks });
			stocks[pid] -= cur.stocks;
			buydb[bid].stocks -= cur.stocks;
			ret += abs(mPrice - buydb[bid].price) * cur.stocks;
			mQuantity -= cur.stocks;
		}
		else {
			v[sid].push_back({ cur.id, mQuantity });
			stocks[pid] -= mQuantity;
			buydb[bid].stocks -= mQuantity;
			ret += abs(mPrice - buydb[bid].price) * mQuantity;
			cur.stocks -= mQuantity;
			mQuantity = 0;
			pq[pid].push({ cur.id, buydb[bid].price, cur.stocks });
		}
	}
	return ret;
}

int refund(int sId) {
	if (!sidMap.count(sId)) return -1;
	int sid = sidMap[sId];
	if (refundFlag[sid]) return -1;
	refundFlag[sid] = true;
	int ret = 0;
	for (auto nx : v[sid]) {
		int bid = bidMap[nx.bid]; int pid = pidMap[buydb[bid].product];
		buydb[bid].stocks += nx.quantity, buydb[bid].canceled = false;
		stocks[pid] += nx.quantity;
		pq[pid].push({ nx.bid, buydb[bid].price, nx.quantity });
		ret += nx.quantity;
	}
	v[sid].clear();
	return ret == 0 ? -1 : ret;
}
```
