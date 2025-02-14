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
	int id, product, price, quantity;
}buydb[MAXN];

struct SellDB{
	int id, product, price, quantity;
}selldb[MAXN];

unordered_map<int, int> bidMap, sidMap, pidMap;
int bidCnt, sidCnt, pidCnt;
int stocks[MAXN];
bool canceled[MAXN];
void init() {
	bidCnt = sidCnt = pidCnt = 0, bidMap.clear(), sidMap.clear(), pidMap.clear();
	memset(stocks, 0, sizeof(stocks)), memset(canceled, 0, sizeof(canceled));
}

int getID(int flag, int c) {
	if(flag == BUY)	return bidMap.count(c) ? bidMap[c] : bidMap[c] = bidCnt++;
	if(flag == SELL) return sidMap.count(c) ? sidMap[c] : sidMap[c] = sidCnt++;
	if(flag == PRODUCT)	return pidMap.count(c) ? pidMap[c] : pidMap[c] = pidCnt++;
}

int buy(int bId, int mProduct, int mPrice, int mQuantity) {
	int bid = getID(BUY, bId), pid = getID(PRODUCT, mProduct);
	buydb[bid] = { bId, mProduct, mPrice, mQuantity };
	stocks[pid] = mQuantity;
	return stocks[pid];
}

int cancel(int bId) {
	if (!bidMap.count(bId)) return -1;
	int id = getID(BUY, bId);
	int pid = getID(BUY, buydb[id].product);
	if (!stocks[pid]) return -1;
	stocks[pid] -= buydb[id].quantity;
	buydb[id].quantity = 0; canceled[pid] = true;
	return stocks[pid];
}

int sell(int sId, int mProduct, int mPrice, int mQuantity) {
	int sid = getID(SELL, sId), pid = getID(PRODUCT, mProduct);
	selldb[sid] = { sId, mProduct, mPrice, mQuantity };
	if (stocks[pid] < mQuantity) return -1;

	stocks[pid] -= mQuantity;
	return 0;
}

int refund(int sId) {
	return 0;
}
```
