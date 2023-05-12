```cpp
//# define MAX_ORDER 200001
//
//struct Order
//{
//	int stock;
//	int quantity;
//	int price;
//	bool isSell;
//
//	void init() {
//		stock = 0;
//		quantity = 0;
//		price = 0;
//		isSell = false;
//	}
//};
//
//Order orders[MAX_ORDER];
//int owp; // order write point
//int minPrice[6];
//int maxPrice[6];
//
//void init() {
//	owp = 0;
//
//	for (int i = 0; i < 6; i++)
//	{
//		minPrice[i] = 1000000;
//		maxPrice[i] = 0;
//	}
//	for (int i = 0; i < MAX_ORDER; i++)
//	{
//		orders[i].init();
//	}
//
//}
//
//int findHighPrice(int mStock) {
//	int max = 1;
//	for (int i = 0; i < MAX_ORDER; i++)
//	{
//		Order o = orders[i];
//		if (o.isSell == true)
//			continue;
//		if (o.stock != mStock)
//			continue;
//		if (o.quantity == 0)
//			continue;
//		if (o.price > max)
//			max = o.price;
//	}
//	return max;
//}
//
//int findLowPrice(int mStock) {
//	int min = 9999999;
//	for (int i = 0; i < MAX_ORDER; i++)
//	{
//		Order o = orders[i];
//		if (o.isSell == false)
//			continue;
//		if (o.stock != mStock)
//			continue;
//		if (o.quantity == 0)
//			continue;
//		if (o.price < min)
//			min = o.price;
//	}
//	return min;
//}
//
//int findIndexOfLowNumber2(int mStock, int mPrice) {
//	for (int i = 1; i < MAX_ORDER; i++)
//	{
//		Order o = orders[i];
//		if (o.isSell == true)
//			continue;
//		if (o.stock != mStock)
//			continue;
//		if (o.quantity == 0)
//			continue;
//		return i;
//	}
//	return -1;
//}
//
//int findIndexOfLowNumber(int mStock, int mPrice) {
//	for (int i = 1; i < MAX_ORDER; i++)
//	{
//		Order o = orders[i];
//		if (o.isSell == true)
//			continue;
//		if (o.stock != mStock)
//			continue;
//		if (o.quantity == 0)
//			continue;
//		if (o.price != mPrice)
//			continue;
//		return i;
//	}
//	return -1;
//}
//
//int buy(int mNumber, int mStock, int mQuantity, int mPrice) {
//	// 매수 주문정보 저장
//	buyOrders[bwp++] = { mNumber, mStock, mQuantity, mPrice };
//	
//	// 거래 체결
//	while (1) {
//		// 최저가격 매도주문 탐색
//		int min = findLowPrice(orders[bIdx].stock);
//
//		// 현 매수주문과 체결 가능한지 체크
//		if (min > orders[bIdx].price) {
//			break;
//		}
//
//		// 최저번호 매도주문 탐색
//		int sIdx = findIndexOfLowNumber(orders[bIdx].stock, min, false);
//
//		// 체결
//		conclude(bIdx, sIdx, false);
//	}
//	// 채결 후 주문 수량 반환
//	return buyOrders[bIdx].quantity;
//}
//
//void cancel(int mNumber) {
//	orders[mNumber].quantity = 0;
//}
//
//int bestProfit(int mStock) {
//	return maxProfit[mStock];
//}
//
//void conclude(int mBuyIdx, int mSellIdx, bool isSell) {
//	Order buy = orders[mBuyIdx];
//	Order sell = orders[mSellIdx];
//
//	if (buy.quantity < sell.quantity) {
//		sell.quantity -= buy.quantity;
//		buy.quantity = 0;
//	}
//	else if (buy.quantity == sell.quantity) {
//		buy.quantity = 0;
//		sell.quantity = 0;
//	}
//	else { // bOrder.quantity > sOrder.quantity
//		buy.quantity -= buy.quantity;
//		sell.quantity = 0;
//	}
//	
//	// 체결 가격 정하기
//	int price = -1;
//	int stock = buy.stock;
//	if (isSell) {
//		price = buy.price;
//	}
//	else {
//		price = sell.price;
//	}
//
//	if (price < minPrice[stock]) {
//		minPrice[stock] = price;
//	}
//
//	if (price - minPrice[stock] > maxProfit[stock]) {
//		maxProfit[stock] = price - minPrice[stock];
//	}
//	orders[mBuyIdx] = buy;
//	orders[mSellIdx] = sell;
//}
//
//int buy(int mNumber, int mStock, int mQuantity, int mPrice) {
//	//1. 입력을 받아 저장 및 추가한다.
//	int bIdx = mNumber;
//	orders[bIdx] = { mStock, mQuantity, mPrice, false };
//
//	while (1) {
//		if (orders[bIdx].quantity == 0)
//			break;
//		int min = findLowPrice(orders[bIdx].stock);
//		if (min > orders[bIdx].price) {
//			break;
//		}
//		int sIdx = findIndexOfLowNumber(orders[bIdx].stock, min);
//	}
//	// 3. 처리한 주문에 남은 주문수량을 반환한다.
//	return orders[bIdx].quantity;
//}
//
//int sell(int mNumber, int mStock, int mQuantity, int mPrice) {
//	// 매도 주문정보 저장
//	Order o = { mStock, mQuantity, mPrice, true };
//	sellOrders[swp++] = 0;
//	int sIdx = swp - 1;
//
//	// 거래 체결
//	while (1) {
//		if (sellOrders[sIdx].quantity == 0)
//			break;
//
//		// 최고가격 매수주문 탐색
//		int max = findHighPrice(o.stock);
//
//		// 현 매수주문과 체결 가능한지 체크
//		if (max < o.price)
//			break;
//
//		// 최저번호 매수주문 탐색
//		int bIdx = findIndexOfLowNumber2(o.stock, max);
//
//		// 체결
//		conclude(bIdx, sIdx, true);
//	}
//	// 체결 후 주문 수량 반환
//	return orders[sIdx].quantity;
//}
//
//void cancel(int mNumber) {
//	orders[mNumber].quantity = 0;
//}
//
//int bestProfit(int mStock) {
//	return maxProfit[mStock];
//}

#define MAX_ORDER   200001
#define MAX_STOCK   6
#define TYPE_BUY    true
#define TYPE_SELL   false
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


void init() {

    for (int i = 1; i < MAX_STOCK; i++) {
        minPrice[i] = 1000000;
        maxProfit[i] = 0;

    }



    for (int oIdx = 1; oIdx < MAX_ORDER; oIdx++) {

        orders[oIdx].quantity = INVALID_ORDER;

    }

}



int findBuyOrder(int mStock, int mPrice) {

    int max = 0;

    int ret = -1;



    for (int oIdx = 1; oIdx < MAX_ORDER; oIdx++) {

        Order o = orders[oIdx];

        if (o.type == TYPE_SELL)

            continue;



        if (o.stock  != mStock)

            continue;



        if (o.quantity == INVALID_ORDER)

            continue;



        if (o.price < mPrice)

            continue;



        if (o.price > max) {

            max = o.price;

            ret = oIdx;

        }

    }



    return ret;

}



int findSellOrder(int mStock, int mPrice) {

    int min = 9999999;

    int ret = -1;

    for (int oIdx = 1; oIdx < MAX_ORDER; oIdx++) {

        Order o = orders[oIdx];

        if (o.type == TYPE_BUY)

            continue;



        if (o.stock  != mStock)

            continue;



        if (o.quantity == INVALID_ORDER)

            continue;



        if (o.price > mPrice)

            continue;



        if (o.price < min) {

            min = o.price;

            ret = oIdx;

        }

    }



    return ret;

}



void conclude(int mBID, int mSID, bool mType)

{

    Order buy = orders[mBID];

    Order sell = orders[mSID];



    if (buy.quantity < sell.quantity) {

        sell.quantity = sell.quantity - buy.quantity;

        buy.quantity = INVALID_ORDER;

    }
    else if (buy.quantity == sell.quantity) {

        buy.quantity = INVALID_ORDER;

        sell.quantity = INVALID_ORDER;

    }
    else {

        buy.quantity = buy.quantity - sell.quantity;

        sell.quantity = INVALID_ORDER;

    }



    int price = -1;

    int stock = buy.stock;



    if (mType == TYPE_BUY) {

        price = sell.price;

    }
    else {

        price = buy.price;

    }



    if (price < minPrice[stock]) {

        minPrice[stock] = price;

    }



    if (price - minPrice[stock] > maxProfit[stock]) {

        maxProfit[stock] = price - minPrice[stock];

    }



    orders[mBID] = buy;

    orders[mSID] = sell;

}



int buy(int mNumber, int mStock, int mQuantity, int mPrice) {

    int bIdx = mNumber;

    orders[bIdx] = { mStock, mQuantity, mPrice, TYPE_BUY };



    while (1) {

        if (orders[bIdx].quantity == INVALID_ORDER)

            break;



        int sIdx = findSellOrder(mStock, mPrice);

        if (sIdx == -1)

            break;



        conclude(bIdx, sIdx, TYPE_BUY);

    }



    return orders[bIdx].quantity;

}



int sell(int mNumber, int mStock, int mQuantity, int mPrice) {

    int sIdx = mNumber;

    orders[sIdx] = { mStock, mQuantity, mPrice, TYPE_SELL };



    while (1) {

        if (orders[sIdx].quantity == INVALID_ORDER)

            break;



        int bIdx = findBuyOrder(mStock, mPrice);

        if (bIdx == -1)

            break;



        conclude(bIdx, sIdx, TYPE_SELL);

    }



    return orders[sIdx].quantity;

}



void cancel(int mNumber) {

    orders[mNumber].quantity = INVALID_ORDER;

}



int bestProfit(int mStock) {

    return maxProfit[mStock];

}
#include < queue >



using namespace std;



#define MAX_ORDER         200001

#define MAX_STOCK         6

#define TYPE_BUY         true

#define TYPE_SELL         false

#define INVALID_ORDER     0



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

    bool operator()(const Data& o1, const Data& o2)

    {

        if (o2.value > o1.value)

            return true;

        else if (o2.value < o1.value)

            return false;

        else

        {

            if (o2.index < o1.index)

                return true;

            else

                return false;

        }

    }

};



struct sellComparator {

    bool operator()(const Data& o1, const Data& o2)

    {

        if (o2.value < o1.value)

            return true;

        else if (o2.value > o1.value)

            return false;

        else

        {

            if (o2.index < o1.index)

                return true;

            else

                return false;

        }

    }

};

priority_queue <Data, vector <Data >, buyComparator > buyQueue[MAX_STOCK];

priority_queue <Data, vector <Data >, sellComparator > sellQueue[MAX_STOCK];



void init() {

    for (int i = 1; i < MAX_STOCK; i + +) {

        minPrice[i] = 1000000;

        maxProfit[i] = 0;



        while (buyQueue[i].empty() = = false)

            buyQueue[i].pop();

        while (sellQueue[i].empty() = = false)

            sellQueue[i].pop();

    }



    for (int oIdx = 1; oIdx < MAX_ORDER; oIdx + +) {

        orders[oIdx].quantity = INVALID_ORDER;

    }

}



int findBuyOrder(int mStock, int mPrice) {

    int stock = mStock;

    int price = mPrice;



    while (buyQueue[stock].empty() = = false) {

        Data d = buyQueue[stock].top();

        buyQueue[stock].pop();



        int bIdx = d.index;

        if (orders[bIdx].quantity = = INVALID_ORDER)

            continue;



        if (d.value > = price)

            return bIdx;



        buyQueue[stock].push(d);

        break;

    }



    return -1;

}



int findSellOrder(int mStock, int mPrice) {

    int stock = mStock;

    int price = mPrice;



    while (sellQueue[stock].empty() = = false) {

        Data d = sellQueue[stock].top();

        sellQueue[stock].pop();



        int sIdx = d.index;

        if (orders[sIdx].quantity = = INVALID_ORDER)

            continue;



        if (d.value < = price)

            return sIdx;



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

        sell.quantity = sell.quantity - buy.quantity;

        buy.quantity = 0;

    }

    else if (buy.quantity = = sell.quantity) {

        buy.quantity = 0;

        sell.quantity = 0;

    }

    else {

        buy.quantity = buy.quantity - sell.quantity;

        sell.quantity = 0;

    }



    int price = -1;

    int stock = buy.stock;



    if (mType = = TYPE_BUY) {

        price = sell.price;

    }

    else {

        price = buy.price;

    }



    if (price < minPrice[stock]) {

        minPrice[stock] = price;

    }



    if (price - minPrice[stock] > maxProfit[stock]) {

        maxProfit[stock] = price - minPrice[stock];

    }



    orders[mBID] = buy;

    orders[mSID] = sell;

}



int buy(int mNumber, int mStock, int mQuantity, int mPrice) {

    int bIdx = mNumber;

    orders[bIdx] = { mStock, mQuantity, mPrice, TYPE_BUY };



    while (1) {

        if (orders[bIdx].quantity == INVALID_ORDER)

            break;



        int sIdx = findSellOrder(mStock, mPrice);

        if (sIdx = = -1)

            break;



        conclude(bIdx, sIdx, TYPE_BUY);



        if (orders[sIdx].quantity != INVALID_ORDER)

            sellQueue[mStock].push({ orders[sIdx].price, sIdx });

    }



    if (orders[bIdx].quantity  != INVALID_ORDER)

        buyQueue[mStock].push({ mPrice, mNumber });



    return orders[bIdx].quantity;

}



int sell(int mNumber, int mStock, int mQuantity, int mPrice) {

    int sIdx = mNumber;

    orders[sIdx] = { mStock, mQuantity, mPrice, TYPE_SELL };



    while (1) {

        if (orders[sIdx].quantity = = INVALID_ORDER)

            break;



        int bIdx = findBuyOrder(mStock, mPrice);

        if (bIdx = = -1)

            break;



        conclude(bIdx, sIdx, TYPE_SELL);



        if (orders[bIdx].quantity  != INVALID_ORDER)

            buyQueue[mStock].push({ orders[bIdx].price, bIdx });

    }



    if (orders[sIdx].quantity  != INVALID_ORDER)

        sellQueue[mStock].push({ mPrice, mNumber });



    return orders[sIdx].quantity;

}



void cancel(int mNumber) {

    orders[mNumber].quantity = INVALID_ORDER;

}



int bestProfit(int mStock) {

    return maxProfit[mStock];

}



```
