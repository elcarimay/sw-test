```cpp
#include <queue>
using namespace std;

#define MAX_ORDER       200001
#define MAX_STOCK       6
#define TYPE_BUY        true
#define TYPE_SELL       false
#define INVALID_ORDER   0

struct Order {
    int stock;
    int quantity;
    int price;
    bool type;
};

Order orders[MAX_ORDER];
int minPrice[MAX_STOCK];
int maxProfit[MAX_STOCK];

struct Data {
    int value;
    int index;
};

struct buyComparator {
    bool operator()(const Data & o1, const Data & o2)
    {
        if (o2.value > o1.value) return true;
        else if (o2.value < o1.value) return false;
        else
        {
            if (o2.index < o1.index) return true;
            else return false;
        }
    }
};

struct sellComparator {
    bool operator()(const Data & o1, const Data & o2)
    {
        if (o2.value < o1.value) return true;
        else if (o2.value > o1.value) return false;
        else
        {
            if (o2.index < o1.index) return true;
            else return false;
        }
    }
};

priority_queue <Data, vector <Data>, buyComparator> buyQueue[MAX_STOCK];
priority_queue <Data, vector <Data>, sellComparator> sellQueue[MAX_STOCK];

void init() {
    for (int i = 1; i < MAX_STOCK; i++) {
        minPrice[i]  = 1000000;
        maxProfit[i]  = 0;
        while (buyQueue[i].empty() == false) buyQueue[i]. pop();
        while (sellQueue[i].empty() == false) sellQueue[i]. pop();
    }
    for (int oIdx = 1; oIdx < MAX_ORDER; oIdx++) {
        orders[oIdx].quantity = INVALID_ORDER;
    }
}

int findBuyOrder(int mStock, int mPrice) {
    int stock = mStock;
    int price = mPrice;
    while (buyQueue[stock].empty() == false) {
        Data d = buyQueue[stock].top();
        buyQueue[stock]. pop();
        int bIdx = d.index;
        if (orders[bIdx].quantity == INVALID_ORDER) continue;
        if (d.value >= price) return bIdx;
        buyQueue[stock].push(d);
        break;
    }
    return -1;
}

int findSellOrder(int mStock, int mPrice) {
    int stock = mStock;
    int price = mPrice;

    while (sellQueue[stock].empty() == false) {
        Data d = sellQueue[stock].top();
        sellQueue[stock].pop();
        int sIdx = d.index;
        if (orders[sIdx].quantity == INVALID_ORDER) continue;
        if (d.value <= price) return sIdx;
        sellQueue[stock].push(d);
        break;
    }
    return -1;
}

void conclude(int mBID, int mSID, bool mType)
{
    Order buy = orders[mBID];
    Order sell = orders[mSID];

    if (buy.quantity < sell.quantity) {
        sell.quantity -= buy.quantity;
        buy.quantity = 0;
    }
    else if (buy.quantity == sell.quantity) {
        buy.quantity = 0;
        sell.quantity = 0;
    }
    else {
        buy.quantity -= sell.quantity;
        sell.quantity = 0;
    }
    int price = -1;
    int stock = buy.stock;

    if (mType == TYPE_BUY) price = sell.price;
    else price  = buy.price;

    if (price < minPrice[stock]) minPrice[stock]  = price;

    if (price - minPrice[stock] > maxProfit[stock])
        maxProfit[stock] = price - minPrice[stock];

    orders[mBID] = buy;
    orders[mSID] = sell;
}

int buy(int mNumber, int mStock, int mQuantity, int mPrice) {
    int bIdx = mNumber;
    orders[bIdx] = {mStock, mQuantity, mPrice, TYPE_BUY};
    while (1) {
        if (orders[bIdx].quantity == INVALID_ORDER) break;
        int sIdx = findSellOrder(mStock, mPrice);
        if (sIdx == - 1) break;
        conclude(bIdx, sIdx, TYPE_BUY);

        if (orders[sIdx].quantity != INVALID_ORDER)
            sellQueue[mStock].push({orders[sIdx].price, sIdx});
    }

    if (orders[bIdx].quantity != INVALID_ORDER)
        buyQueue[mStock].push({mPrice, mNumber});
    return orders[bIdx].quantity;
}

int sell(int mNumber, int mStock, int mQuantity, int mPrice) {
    int sIdx = mNumber;
    orders[sIdx] = {mStock, mQuantity, mPrice, TYPE_SELL};
    while (1) {
        if (orders[sIdx].quantity == INVALID_ORDER) break;
        int bIdx = findBuyOrder(mStock, mPrice);
        if (bIdx == - 1) break;
        conclude(bIdx, sIdx, TYPE_SELL);

        if (orders[bIdx].quantity != INVALID_ORDER)
            buyQueue[mStock].push({orders[bIdx].price, bIdx});
    }

    if (orders[sIdx].quantity != INVALID_ORDER)
        sellQueue[mStock].push({mPrice, mNumber});
    return orders[sIdx].quantity;
}

void cancel(int mNumber) {
    orders[mNumber].quantity = INVALID_ORDER;
}

int bestProfit(int mStock) {
    return maxProfit[mStock];
}
```
