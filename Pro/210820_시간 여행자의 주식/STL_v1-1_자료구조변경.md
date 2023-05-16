```cpp
#define MAX_NUMBER  200001
#define BUY         true
#define SELL        false
#define MAX_STOCK   6

#include <queue>
#include <vector>
#include <unordered_map>
#include <array>
#include <iostream>
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

unordered_map<int, Order> orders; // unorered_map
// vector<Order> orders; // vector
// array<Order, MAX_NUMBER> orders; // array
// Order orders[MAX_NUMBER];
int minPrice[MAX_STOCK];
int maxProfit[MAX_STOCK];

struct minComp
{
    bool operator()(const Data& o1, const Data& o2)
    {
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
    bool operator()(const Data& o1, const Data& o2)
    {
        if (o1.price < o2.price) return true;
        else if (o1.price > o2.price) return false;
        else {
            if (o1.number > o2.number) return true;
            else return false;
        }
    }
};

priority_queue<Data, vector <Data>, minComp> sellQueue[6];
priority_queue<Data, vector <Data>, maxComp> buyQueue[6];

void init() {
    orders.clear();
    // orders.clear(); // vector
    // orders.push_back(Order()); // vector
    // for (int i = 0; i < MAX_NUMBER; i++)
    // {
    //     orders[i].init();
    // } // array적용시
    
    for (int i = 0; i < MAX_STOCK; i++)
    {
        minPrice[i] = 1000000;
        maxProfit[i] = 0;

        while (buyQueue[i].empty() == false) buyQueue[i].pop();
        while (sellQueue[i].empty() == false) sellQueue[i].pop();
    }
}

void conclude(int mBuyIdx, int mSellIdx, bool isSell) {
    // orders.push_back(Order()); // vector
    // orders.emplace_back(Order()); // vector
    
    Order buy = orders[mBuyIdx];
    Order sell = orders[mSellIdx];
    if (buy.quantity < sell.quantity) {
        sell.quantity -= buy.quantity;
        buy.quantity = 0;
    }
    else if (buy.quantity == sell.quantity) {
        buy.quantity = 0;
        sell.quantity = 0;
    }
    else {  // buy.quantity > sell.quantity
        buy.quantity -= sell.quantity;
        sell.quantity = 0;

    }
    int price = -1;
    int stock = buy.stock;

    if (isSell) price = sell.price;
    else price = buy.price;

    if (price < minPrice[stock]) minPrice[stock] = price;
    if (price - minPrice[stock] > maxProfit[stock])
        maxProfit[stock] = price - minPrice[stock];

    orders[mBuyIdx] = buy;
    orders[mSellIdx] = sell;
}

int buy(int mNumber, int mStock, int mQuantity, int mPrice) {
    // 1. 입력을 받아 저장 및 추가한다.
    // orders.push_back(Order()); // vector
    // orders.emplace_back(Order()); // vector
    orders[mNumber] = { mStock, mQuantity, mPrice };
    int bIdx = mNumber;
    while (1) {
        if (sellQueue[mStock].empty() == true) break;
        Data d = sellQueue[mStock].top();
        sellQueue[mStock].pop();

        int sIdx = d.number;
        if (orders[sIdx].quantity == 0) continue;
        if (d.price > mPrice) {
            sellQueue[mStock].push(d);
            break;
        }
        conclude(bIdx, sIdx, BUY);

        if (orders[sIdx].quantity > 0) sellQueue[mStock].push(Data{ sIdx, orders[sIdx].price });
        if (orders[bIdx].quantity == 0) break;
    }
    if (orders[bIdx].quantity > 0) buyQueue[mStock].push(Data{ bIdx, orders[bIdx].price });
    return orders[bIdx].quantity;
}

int sell(int mNumber, int mStock, int mQuantity, int mPrice) {
    // orders.push_back(Order()); // vector
    // orders.emplace_back(Order()); // vector
    orders[mNumber] = { mStock, mQuantity, mPrice };
    int sIdx = mNumber;

    while (1) {
        if (buyQueue[mStock].empty() == true) break;
        Data d = buyQueue[mStock].top();
        buyQueue[mStock].pop();

        int bIdx = d.number;
        if (orders[bIdx].quantity == 0) continue;
        if (d.price < mPrice) {
            buyQueue[mStock].push(d);
            break;
        }
        conclude(bIdx, sIdx, SELL);

        if (orders[bIdx].quantity > 0) buyQueue[mStock].push(Data{ bIdx, orders[bIdx].price });
        if (orders[sIdx].quantity == 0) break;
    }
    if (orders[sIdx].quantity > 0) sellQueue[mStock].push(Data{ sIdx, orders[sIdx].price });
    return orders[sIdx].quantity;
}

void cancel(int mNumber) {
    orders[mNumber].quantity = 0;
}

int bestProfit(int mStock) {
    return maxProfit[mStock];
}
```

### 시간여행자의 각 Case별 실행시간
```
1. 
정답만들기_v1-1 
정답만들기_v1-2
STL_v1-1 206 ms
STL_v1-2 201 ms
2. 
STL_v2 208 ms
3. 
wo_STL_v1 195 ms

vector적용시 220ms
array적용시 205 ms
unordered_map적용시 250 ms
![image](https://github.com/elcarimay/sw-test/assets/103011254/f8880c9b-f510-4a0e-9fdb-07516b4b3e4b)
```
