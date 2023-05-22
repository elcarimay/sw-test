```cpp
#define MAX_NUMBER       200001
#define BUY             false
#define SELL            true
#define MAX_STOCK       6 // 1 - 5
#define INVALID_VALUE   0
#include <queue>
using namespace std;

struct Order {
    int stock;
    int quantity;
    int price;
    void init() {
        stock = 0;
        quantity = 0;
        price = 0;
    }
};

struct Data
{
    int number;
    int price;
};

struct minComp
{
    bool operator()(const Data& o1, const Data& o2) {
        if (o1.price < o2.price) return false;
        else if (o1.price > o2.price) return true;
        else {
            if (o1.number > o2.number) return true;
            else return false;
        }
    }
};

struct maxComp
{
    bool operator()(const Data& o1, const Data& o2) {
        if (o1.price < o2.price) return true;
        else if (o1.price > o2.price) return false;
        else {
            if (o1.number > o2.number) return true;
            else return false;
        }
    }
};

priority_queue<Data, vector <Data>, minComp> sellQueue[MAX_STOCK];
priority_queue<Data, vector <Data>, maxComp> buyQueue[MAX_STOCK];

Order orders[MAX_NUMBER];
int minPrice[MAX_STOCK];
int maxProfit[MAX_STOCK];

void init() {
    for (int i = 1; i < MAX_NUMBER; i++)
        orders[i].init();

    for (int i = 1; i < MAX_STOCK; i++)
    {
        minPrice[i] = 1000000;
        maxProfit[i] = 0;
        while (buyQueue[i].empty() == false) buyQueue[i].pop();
        while (sellQueue[i].empty() == false) sellQueue[i].pop();
    }

}

void conclude(int mBID, int mSID, bool type) {
    Order buy = orders[mBID];
    Order sell = orders[mSID];

    // 체결수량 갱신
    if (buy.quantity < sell.quantity) {
        sell.quantity -= buy.quantity;
        buy.quantity = 0;
    }
    else if (buy.quantity > sell.quantity) {
        buy.quantity -= sell.quantity;
        sell.quantity = 0;
    }
    else {  // buy.quantity == sell.quantity
        buy.quantity = 0;;
        sell.quantity = 0;
    }

    // 체결덮어 씌워주기
    orders[mBID] = buy;
    orders[mSID] = sell;

    // 체결 가격 정하기
    int price = -1;
    if (type == SELL) price = buy.price;
    else if (type == BUY) price = sell.price;

    // 최소값, 최대이익 매번 갱신
    int stock = orders[mBID].stock;
    if (price < minPrice[stock]) minPrice[stock] = price;
    if (price - minPrice[stock] > maxProfit[stock])
        maxProfit[stock] = price - minPrice[stock];

    return;
}

int buy(int mNumber, int mStock, int mQuantity, int mPrice) {
    // 매수 주문 생성
    orders[mNumber] = { mStock, mQuantity, mPrice };
    int bIdx = mNumber;

    while (1)
    {
        if (sellQueue[mStock].empty() == true) break;
        Data d = sellQueue[mStock].top();
        sellQueue[mStock].pop();

        int sIdx = d.number;
        if (orders[sIdx].quantity == 0)   continue;

        // 체결이 가능한가?
        if (d.price > mPrice) {
            sellQueue[mStock].push(d);
            break;
        }

        // 체결
        conclude(bIdx, sIdx, BUY);

        if (orders[sIdx].quantity > 0)
            sellQueue[mStock].push(Data{ sIdx, orders[sIdx].price });
        if (orders[bIdx].quantity == 0) break;
    }
    if (orders[bIdx].quantity > 0)
        buyQueue[mStock].push(Data{ bIdx, orders[bIdx].price });
    // 현재 매수 주문의 남은 수량 반환
    return orders[bIdx].quantity;
}

int sell(int mNumber, int mStock, int mQuantity, int mPrice) {
    // 매도 주문 생성
    orders[mNumber] = { mStock, mQuantity, mPrice };
    int sIdx = mNumber;

    while (1)
    {
        if (buyQueue[mStock].empty() == true) break;
        Data d = buyQueue[mStock].top();
        buyQueue[mStock].pop();

        int bIdx = d.number;
        if (orders[bIdx].quantity == 0)   continue;

        // 체결이 가능한가?
        if (d.price < mPrice) {
            buyQueue[mStock].push(d);
            break;
        }

        // 체결
        conclude(bIdx, sIdx, SELL);

        if (orders[bIdx].quantity > 0)
            buyQueue[mStock].push(Data{ bIdx, orders[bIdx].price });
        if (orders[sIdx].quantity == 0) break;
    }
    if (orders[sIdx].quantity > 0)
        sellQueue[mStock].push(Data{ sIdx, orders[sIdx].price });
    // 현재 매수 주문의 남은 수량 반환
    return orders[sIdx].quantity;
}

void cancel(int mNumber) {
    // 지금 들어온 주문번호가 어디 주문인지?
    orders[mNumber].quantity = INVALID_VALUE;
}

int bestProfit(int mStock) {
    return maxProfit[mStock];
}
```
