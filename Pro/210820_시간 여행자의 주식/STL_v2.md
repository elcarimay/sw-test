```cpp
#include <queue>

using namespace std;

////////////////////////////////////////////////////////////////////////////////

const int MAX_ORDERS = 200003;
const int MAX_STOCKS = 6;
const int MAX_PRICE = 1000000;

enum TYPE
{
	TYPE_BUY,
	TYPE_SELL
};

/*
매수 주문은 주식을 사기 위한 주문, 매도 주문은 주식을 팔기 위한 주문을 의미한다.
주문에는 주문 번호, 주식 종목, 주문 수량, 희망 가격이 포함된다.
주문은 주문 번호로 구별된다.
*/
class Order
{
public:
	int orderid;
	int stockid;
	int quantity;
	int price;
	int version;
	int initquantity;
	bool bcancel;

	Order()
	{
		orderid = 0;
		stockid = 0;
		quantity = 0;
		price = 0;
		version = 0;
		initquantity = quantity;
		bcancel = false;
	}

	Order(int oid, int sid, int qty, int p)
	{
		orderid = oid;
		stockid = sid;
		quantity = qty;
		price = p;
		version = 0;
		initquantity = quantity;
		bcancel = false;
	}

	bool isPossibleToDeal(int sellprice)
	{
		return (price >= sellprice);
	}

	bool isCurrentVersion(int ver)
	{
		return (version == ver);
	}

	void tredeOrder(int dealqty)
	{
		quantity -= dealqty;
	}

	void updateVersion()
	{
		++version;
	}

	void cancelOrder()
	{
		bcancel = true;
	}
};

class Data
{
public:
	int price;
	int orderid;
	int version;
	int type;

	Data()
	{
		price = 0;
		orderid = 0;
		version = 0;
		type = TYPE_BUY;
	}

	Data(int p, int oid, int v, int ty)
	{
		price = p;
		orderid = oid;
		version = v;
		type = ty;
	}

	Data(const Order &o, int ty)
	{
		price = o.price;
		orderid = o.orderid;
		version = o.version;
		type = ty;
	}

	bool operator<(const Data &d) const
	{
		// 거래를 체결할 미체결 매수 주문은 매수 희망 가격이 높은 주문, 가격이 동일하다면
		// 주문 번호가 낮은 주문 순으로 선택한다.
		if (type == TYPE_BUY)
		{
			return (price < d.price
				|| price == d.price && orderid > d.orderid);
		}
		
		// 거래를 체결할 미체결 매도 주문은 매도 희망 가격이 낮은 주문, 가격이 동일하다면
		// 주문 번호가 낮은 주문 순으로 선택한다.
		return (price > d.price
			|| price == d.price && orderid > d.orderid);
	}
};

class Stock
{
public:
	int stockid;
	int maxprofit;
	int minprice;
	priority_queue<Data> sellorders;
	priority_queue<Data> buyorders;

	Stock()
	{
		stockid = 0;
		maxprofit = 0;
		minprice = MAX_PRICE;
		sellorders = priority_queue<Data>();
		buyorders = priority_queue<Data>();
	}

	Stock(int sid)
	{
		stockid = sid;
		maxprofit = 0;
		minprice = MAX_PRICE;
		sellorders = priority_queue<Data>();
		buyorders = priority_queue<Data>();
	}
};


////////////////////////////////////////////////////////////////////////////////

int numOfOrders = 0;

Order orders[MAX_ORDERS];
Stock stocks[MAX_STOCKS];


////////////////////////////////////////////////////////////////////////////////
void init()
{
	for (register int i = 1; i <= numOfOrders; ++i)
	{
		orders[i] = Order(i, 0, 0, 0);
	}

	numOfOrders = 0;

	for (register int i = 1; i < MAX_STOCKS; ++i)
	{
		stocks[i] = Stock(i);
	}
}

int buy(int mNumber, int mStock, int mQuantity, int mPrice)
{
	/*
	주문 번호가 mNumber인 매수 주문을 처리한다.
	mNumber 주문의 주식 종목은
	mStock, 주문 수량은 mQuantity, 매수 희망 가격은 mPrice이다.
	
	첫 주문 시 mNumber는 1이고, 이후 buy 혹은 sell 함수가 호출될 때마다 1씩 증가한다.
	*/
	orders[mNumber] = Order(mNumber, mStock, mQuantity, mPrice);
	Order &buyorder = orders[mNumber];
	Stock &stock = stocks[mStock];
	int rtn = -1;
	++numOfOrders;

	priority_queue<Data> &notsellorders = stock.sellorders;
	while (!notsellorders.empty())
	{
		Data d = notsellorders.top();
		notsellorders.pop();

		// 두 주문의 거래가 체결되기 위해선
		//매수 주문의 희망 가격이 매도 주문의 희망 가격 이상이어야 한다
		Order &sellorder = orders[d.orderid];
		if (!sellorder.isCurrentVersion(d.version))
		{
			continue;
		}

		if (!buyorder.isPossibleToDeal(sellorder.price))
		{
			notsellorders.push(d);
			break;
		}

		// 거래 시 체결되는 주문 수량은 두 주문의 남은 주문 수량 중 최솟값이며,
		// 체결 가격은 매도 주문의 희망 가격이다.
		int dealqty = min(buyorder.quantity, sellorder.quantity);
		int dealprice = sellorder.price;

		buyorder.tredeOrder(dealqty);
		sellorder.tredeOrder(dealqty);
		sellorder.updateVersion();

		if (sellorder.quantity > 0)
		{
			stock.sellorders.push(Data(sellorder, TYPE_SELL));
		}

		// 먼저 선택된 주문과의 거래가
		// 나중에 선택된 주문과의 거래보다 이른 시점에 체결된다.
		if (dealprice < stock.minprice)
		{
			stock.minprice = dealprice;
		}

		int diff = dealprice - stock.minprice;
		if (diff > stock.maxprofit)
		{
			stock.maxprofit = diff;
		}		

		// 미체결 매수 주문에 남은 mNumber 주문의 주문 수량을 반환한다.
		// mNumber 주문이 미체결 매수 주문에 남지 않는 경우, 0을 반환한다.
		rtn = buyorder.quantity;

		if (buyorder.quantity == 0)
		{
			break;
		}		
	}

	// mNumber 주문과 미체결 매도 주문들 사이의 거래를 체결한 후,
	// 남은 주문 수량을 미체결 매수 주문에 남긴다.
	if (buyorder.quantity > 0)
	{
		stock.buyorders.push(Data(buyorder, TYPE_BUY));
		rtn = buyorder.quantity;
	}

	return rtn;
}

int sell(int mNumber, int mStock, int mQuantity, int mPrice)
{
	/*
	매도 주문이 새로 들어오면 미체결 매수 주문들과 거래를 체결한다.
	
	주문 번호가 mNumber인 매도 주문을 처리한다.
	mNumber 주문의 주식 종목은 mStock, 주문 수량은 mQuantity, 매도 희망 가격은 mPrice이다.
	
	첫 주문 시 mNumber는 1이고, 이후 buy 혹은 sell 함수가 호출될 때마다 1씩 증가한다.
	*/
	orders[mNumber] = Order(mNumber, mStock, mQuantity, mPrice);
	Order &sellorder = orders[mNumber];
	Stock &stock = stocks[mStock];
	int rtn = -1;
	++numOfOrders;

	priority_queue<Data> &notbuyorders = stock.buyorders;
	while (!notbuyorders.empty())
	{
		Data d = notbuyorders.top();
		notbuyorders.pop();

		// 두 주문의 거래가 체결되기 위해선 매수 주문의 희망 가격이 매도 주문의 희망 가격 이상이어야 한다
		Order &buyorder = orders[d.orderid];
		if (!buyorder.isCurrentVersion(d.version))
		{
			continue;
		}

		if (!buyorder.isPossibleToDeal(sellorder.price))
		{
			notbuyorders.push(d);
			break;
		}

		// 거래 시 체결되는 주문 수량은 두 주문의 남은 주문 수량 중 최솟값이며,
		// 체결 가격은 매수 주문의 희망 가격이다.
		int dealqty = min(sellorder.quantity, buyorder.quantity);
		int dealprice = buyorder.price;

		sellorder.tredeOrder(dealqty);
		buyorder.tredeOrder(dealqty);
		buyorder.updateVersion();

		if (buyorder.quantity > 0)
		{
			stock.buyorders.push(Data(buyorder, TYPE_BUY));
		}

		// 먼저 선택된 주문과의 거래가 나중에 선택된 주문과의 거래보다 이른 시점에 체결된다.
		if (dealprice < stock.minprice)
		{
			stock.minprice = dealprice;
		}

		int diff = dealprice - stock.minprice;
		if (diff > stock.maxprofit)
		{
			stock.maxprofit = diff;
		}		

		// 미체결 매수 주문에 남은 mNumber 주문의 주문 수량을 반환한다.
		// mNumber 주문이 미체결 매도 주문에 남지 않는 경우, 0을 반환한다.
		rtn = sellorder.quantity;

		if (sellorder.quantity == 0)
		{
			break;
		}
	}

	// mNumber 주문과 미체결 매도 주문들 사이의 거래를 체결한 후,
	// 남은 주문 수량을 미체결 매수 주문에 남긴다.
	if (sellorder.quantity > 0)
	{
		stock.sellorders.push(Data(sellorder, TYPE_SELL));
		rtn = sellorder.quantity;
	}

	return rtn;
}

void cancel(int mNumber)
{
	/*
	프로그램은 미체결 주문을 취소할 수 있어야 한다.
	*/
	Order &order = orders[mNumber];

	order.cancelOrder();
	order.updateVersion();
}

int bestProfit(int mStock)
{
	/*
	(mStock 주식의 임의의 시점(B)에서의 체결 가격 – mStock 주식의 임의의 시점(A)에서의 체결 가격)의 최댓값
	단, 시점(B)는 함수 호출 시점 이전이어야 하며, 시점(A)는 시점(B) 이전이어야 한다.

	(mStock 주식의 임의의 시점(B)에서의 체결 가격 – mStock 주식의 임의의 시점(A)에서의 체결 가격) 의
	최댓값을 반환한다.
	단, 시점(B)는 bestProfit 함수 호출 시점 이전이어야 하며, 시점(A)는 시점(B) 이전이어야 한다.
	‘시점(B) 이전’은 ‘시점(B)’를 포함한다.
	
	buy 혹은 sell 함수가 한 번만 호출되었을 때에도, 여러 번의 거래 체결이 생길 수 있음에 유의하라.
	이 경우, 어떤 거래 체결이 더 이른 시점에 발생하는 지는 각 함수의 설명을 참조하라.
	
	mStock 주식의 거래 체결이 한 번 이상 있었음이 보장된다.
	*/
	Stock &stock = stocks[mStock];
	int rtn = stock.maxprofit;

	return rtn;
}

```
