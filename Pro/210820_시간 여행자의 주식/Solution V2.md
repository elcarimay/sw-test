```cpp
# define MAX_ORDER 100000
# define SELL true
# define BUY false
#define INVALID_VALUE 0

struct Order
{
	int number;
	int stock;
	int quantity;
	int price;
};

Order buyOrders[MAX_ORDER];
Order sellOrders[MAX_ORDER];
int bwp, swp;

//Order orders[MAX_ORDER * 2 + 1];

int getSellOrder()
{
	//1. 가장 낮은 가격?
	int minPrice = 1000000;
	for (int sIdx = 0; sIdx < swp; sIdx++)
	{
		//수량체크 필요
		if (sellOrders[sIdx].quantity == 0) continue;

		int price = sellOrders[sIdx].price;
		if (price < minPrice)	minPrice = price;
	}

	// 2. '1.' 주문 번호가 가장 낮은 주문?
	int idx = -1;
	int minNumber = 300000;
	for (int sIdx = 0; sIdx < swp; sIdx++)
	{
		//수량체크 필요
		if (sellOrders[sIdx].quantity == 0) continue;

		int price = sellOrders[sIdx].price;
		if (price != minPrice) continue;

		int number = sellOrders[sIdx].number;
		if (number < minNumber)
		{
			minNumber = number;
			idx = sIdx;
		}
	}
	return idx;
}

// 500원		450원 500원 550원 600원(15)
//							600원(13)
//							600원(20)

int getBuyOrder()
{
	//1. 가장 높은 가격?
	int maxPrice = 0;
	for (int bIdx = 0; bIdx < bwp; bIdx++)
	{
		//수량체크 필요
		if (buyOrders[bIdx].quantity == 0) continue;

		int price = buyOrders[bIdx].price;
		if (price > maxPrice)	maxPrice = price;
	}

	// 2. '1.' 주문 번호가 가장 낮은 주문?
	int idx = -1;
	int minNumber = 300000;
	for (int bIdx = 0; bIdx < bwp; bIdx++)
	{
		//수량체크 필요
		if (buyOrders[bIdx].quantity == 0) continue;

		int price = buyOrders[bIdx].price;
		if (price != maxPrice) continue;

		int number = buyOrders[bIdx].number;
		if (number < minNumber)
		{
			minNumber = number;
			idx = bIdx;
		}
	}
	return idx;
}

void conclude(int mBID, int mSID, bool type) {
	int bIdx = mBID;
	int sIdx = mSID;
	Order buy = buyOrders[bIdx];
	Order sell = buyOrders[sIdx];

	// 체결이 진행되면서 체결수량 갱신
	if (buy.quantity < sell.quantity)
	{	// 3			5
		// => 0			2(5-3)
		sell.quantity -= buy.quantity;
		buy.quantity = 0;

	}
	else if (buy.quantity > sell.quantity)
	{	//5				3
		//2				0
		buy.quantity -= sell.quantity;
		sell.quantity = 0;
	}
	else
	{	//5				5
		//0				0
		buy.quantity = 0;
		sell.quantity = 0;
	}
	
	
	// 체결 덮어씌워주기
	buyOrders[bIdx] = buy;
	sellOrders[sIdx] = sell;

	// 체결 가격 정하기
	int price = -1;
	if (type == SELL)
	{
		price = buy.price;
	}
	else if (type == BUY)
	{
		price = sell.price;
	}

	return;
}

int findIndex(bool isSell) {

}

void init() {
	bwp = 0; // 매수주문갯수와 저장위치를 나타냄.
	swp = 0;

	

}

int buy(int mNumber, int mStock, int mQuantity, int mPrice) {
	
	// 매수주문 생성
	Order o = { mNumber, mStock, mQuantity, mPrice };
	buyOrders[bwp] = o;
	bwp++;
	// buyOrders[bwp++] = { mNumber, mStock, mQuantity, mPrice }; 숏코드
	
	// 체결 진행
	int bIdx = bwp - 1;
	while (1) {
		if (buyOrders[bIdx].quantity == 0) break;

		// 체결이 가능한가?
		int sIdx = getSellOrder();
		if (sellOrders[sIdx].price > buyOrders[bIdx].price) break;

		// 체결
		conclude(bIdx, sIdx, BUY);
		
	}

	// 현재 매수 주문의 남은 수량 반환
	return buyOrders[bIdx].quantity;
}

int sell(int mNumber, int mStock, int mQuantity, int mPrice) {
	// 매도 주문 생성
	Order o = { mNumber, mStock, mQuantity, mPrice };
	sellOrders[swp] = o;
	swp++;
	// buyOrders[bwp++] = { mNumber, mStock, mQuantity, mPrice }; 숏코드

	// 체결 진행
	int sIdx = swp - 1;
	while (1) {
		// 현재 매도 주문으로 체결이 가능한 상태인가?
		if (sellOrders[sIdx].quantity == 0) break;

		// 매수주문 찾기
		int bIdx = getBuyOrder();

		// 찾은 매수 주문으로 체결이 가능한가?
		if (sellOrders[sIdx].price > buyOrders[bIdx].price) break;

		// 체결
		conclude(bIdx, sIdx, SELL);

	}
	 
	// 현재 매수 주문의 남은 수량 반환
	return sellOrders[sIdx].quantity;
}

void cancel(int mNumber) {

	int oIdx = mNumber;
	orders[oIdx].quantity = INVALID_VALUE;

	// 지금 들어온 주문 번호가 어디 주문인지?
	int idx = findIndex(SELL);
	// sIdx: 0 ~ swp;
	if (idx == -1)
		// bIdx: 0 ~ bwp;
		idx = findIndex(BUY);
}

int bestProfit(int mStock) {
	return 0;
}

# define MAX_NUMBER 200001
# define MAX_STOCK 6 // 1 ~ 5
# define SELL true
# define BUY false
#define INVALID_VALUE 0

struct Order
{
	int stock;
	int quantity;
	int price;
};

Order orders[MAX_NUMBER];
int owp;
int maxProfit[MAX_STOCK]; // 0+1 ~ 4+1
int minPrice[MAX_STOCK];
int maxProfit[MAX_STOCK];


int getSellOrder()
{
	//1. 가장 낮은 가격?
	int minPrice = 1000000;
	for (int sIdx = 0; sIdx < MAX_NUMBER; sIdx++)
	{
		//수량체크 필요
		if (orders[sIdx].quantity == 0) continue;

		int price = orders[sIdx].price;
		if (price < minPrice)	minPrice = price;
	}

	// 2. '1.' 주문 번호가 가장 낮은 주문?
	int idx = -1;
	int minNumber = 300000;
	for (int sIdx = 0; sIdx < MAX_NUMBER; sIdx++)
	{
		//수량체크 필요
		if (orders[sIdx].quantity == 0) continue;

		int price = orders[sIdx].price;
		if (price != minPrice) continue;

		int number = sIdx;
		if (number < minNumber)
		{
			minNumber = number;
			idx = sIdx;
		}
	}
	return idx;
}

// 500원		450원 500원 550원 600원(15)
//							600원(13)
//							600원(20)

int getBuyOrder()
{
	//1. 가장 높은 가격?
	int maxPrice = 0;
	for (int bIdx = 0; bIdx < MAX_NUMBER; bIdx++)
	{
		//수량체크 필요
		if (orders[bIdx].quantity == 0) continue;

		int price = orders[bIdx].price;
		if (price > maxPrice)	maxPrice = price;
	}

	// 2. '1.' 주문 번호가 가장 낮은 주문?
	int idx = -1;
	int minNumber = 300000;
	for (int bIdx = 0; bIdx < MAX_NUMBER; bIdx++)
	{
		//수량체크 필요
		if (orders[bIdx].quantity == 0) continue;

		int price = orders[bIdx].price;
		if (price != maxPrice) continue;

		int number = bIdx;
		if (number < minNumber)
		{
			minNumber = number;
			idx = bIdx;
		}
	}
	return idx;
}

void conclude(int mBID, int mSID, bool type) {
	int bIdx = mBID;
	int sIdx = mSID;
	Order buy = orders[bIdx];
	Order sell = orders[sIdx];

	// 체결이 진행되면서 체결수량 갱신
	if (buy.quantity < sell.quantity)
	{	// 3			5
		// => 0			2(5-3)
		sell.quantity -= buy.quantity;
		buy.quantity = 0;

	}
	else if (buy.quantity > sell.quantity)
	{	//5				3
		//2				0
		buy.quantity -= sell.quantity;
		sell.quantity = 0;
	}
	else
	{	//5				5
		//0				0
		buy.quantity = 0;
		sell.quantity = 0;
	}


	// 체결 덮어씌워주기
	orders[bIdx] = buy;
	orders[sIdx] = sell;

	// 체결 가격 정하기
	int price = -1;
	if (type == SELL)
	{
		price = buy.price;
	}
	else if (type == BUY)
	{
		price = sell.price;
	}

	// 최솟값, 최대이익 매번 갱신
	int stock = orders[bIdx].stock;
	if (price < minPrice[stock]) {
		minPrice[stock] = price;
	}

	if (price - minPrice[stock] > maxProfit[stock]) {
		maxProfit[stock] = price - minPrice[stock];
	}

	return;
}

int findIndex(bool isSell) {

}

void init() {
	owp = 0;
	for (int oIdx = 0; oIdx < MAX_NUMBER; oIdx++)
	{
		orders[oIdx].quantity = 0;
	}
	for (int stock = 0; stock < MAX_STOCK; stock++)
	{
		minPrice[stock] = 1000000;
		maxProfit[stock] = -1000000;
	}
}

int buy(int mNumber, int mStock, int mQuantity, int mPrice) {

	// 매수주문 생성
	int oIdx = mNumber;
	Order o = { mStock, mQuantity, mPrice };
	orders[oIdx] = o;


	// 체결 진행
	int bIdx = oIdx;
	while (1) {
		if (orders[bIdx].quantity == 0) break;

		// 체결이 가능한가?
		int sIdx = getSellOrder();
		if (orders[sIdx].price > orders[bIdx].price) break;

		// 체결
		conclude(bIdx, sIdx, BUY);

	}

	// 현재 매수 주문의 남은 수량 반환
	return orders[oIdx].quantity;
}

int sell(int mNumber, int mStock, int mQuantity, int mPrice) {
	// 매도 주문 생성
	int oIdx = mNumber;
	Order o = { mStock, mQuantity, mPrice };
	orders[oIdx] = o;
	
	// 체결 진행
	int sIdx = oIdx;
	while (1) {
		// 현재 매도 주문으로 체결이 가능한 상태인가?
		if (orders[sIdx].quantity == 0) break;

		// 매수주문 찾기
		int bIdx = getBuyOrder();

		// 찾은 매수 주문으로 체결이 가능한가?
		if (orders[sIdx].price > orders[bIdx].price) break;

		// 체결
		conclude(bIdx, sIdx, SELL);

	}

	// 현재 매수 주문의 남은 수량 반환
	return orders[oIdx].quantity;
}

void cancel(int mNumber) {
	int oIdx = mNumber;
	orders[oIdx].quantity = INVALID_VALUE;
}




int bestProfit(int mStock) {
	int stock = mStock;
	return maxProfit[stock];
}


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
