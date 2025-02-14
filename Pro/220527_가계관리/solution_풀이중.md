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
int quantity[MAXN];

void init() {
	bidCnt = sidCnt = pidCnt = 0, bidMap.clear(), sidMap.clear(), pidMap.clear();
	memset(quantity, 0, sizeof(quantity));
}

int getID(int flag, int c) {
	if(flag == BUY)	return bidMap.count(c) ? bidMap[c] : bidMap[c] = bidCnt++;
	if(flag == SELL) return sidMap.count(c) ? sidMap[c] : sidMap[c] = sidCnt++;
	if(flag == PRODUCT)	return pidMap.count(c) ? pidMap[c] : pidMap[c] = pidCnt++;
}

int buy(int bId, int mProduct, int mPrice, int mQuantity) {
	int bid = getID(BUY, bId), pid = getID(PRODUCT, mProduct);
	buydb[bid] = { bId, mProduct, mPrice, mQuantity };
	quantity[pid] = mQuantity;
	return quantity[pid];
}

int cancel(int bId) {
	if (!bidMap.count(bId)) return -1;
	int id = getID(BUY, bId);
	int pid = getID(BUY, buydb[id].product);
	if (!quantity[pid]) return -1;
	quantity[pid] -= buydb[id].quantity;
	buydb[id].quantity = 0;
	return quantity[pid];
}

int sell(int sId, int mProduct, int mPrice, int mQuantity) {
	int id = getID(SELL, sId), pid = getID(mProduct);

	return 0;
}

int refund(int sId) {
	return 0;
}
```
