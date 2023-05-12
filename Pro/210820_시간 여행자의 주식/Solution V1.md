```cpp
#define MAX_STOCK 5
#define MAX_ORDER 200000

struct orderInfo
{
    int price;
    int quantity;
};
struct Data
{
    int idx;
    int price;
    unsigned long long value;
};

struct Order
{
    int minPrice[MAX_STOCK + 1];
    int maxProfit[MAX_STOCK + 1];
    orderInfo order[MAX_ORDER + 1];
    void init()
    {
        for (int i = 0; i < MAX_STOCK + 1; i++)
        {
            minPrice[i] = 10000000;
            maxProfit[i] = 0;
        }
    }
    void insertOrderInfo(int mNumber, int quantity, int mPrice)
    {
        order[mNumber].quantity = quantity;
        order[mNumber].price = mPrice;
    }
}stockOrder;

struct minPQ
{
    Data heap[MAX_ORDER * 2];
    int heapSize = 0;
    void heapInit(void)
    {
        heapSize = 0;
    }
    void heapPush(int idx, int price, unsigned long long value)
    {
        heap[heapSize].idx = idx;
        heap[heapSize].price = price;
        heap[heapSize].value = value;
        int current = heapSize;
        while (current > 0 && heap[current].value < heap[(current - 1) / 2].value)
        {
            Data temp = heap[(current - 1) / 2];
            heap[(current - 1) / 2] = heap[current];
            heap[current] = temp;
            current = (current - 1) / 2;
        }
        heapSize = heapSize + 1;
    }

    Data heapPop()
    {
        Data ret = heap[0];
        heapSize = heapSize - 1;
        heap[0] = heap[heapSize];
        int current = 0;
        while (current * 2 + 1 < heapSize)
        {
            int child;
            if (current * 2 + 2 == heapSize)
            {
                child = current * 2 + 1;
            }
            else
            {
                child = heap[current * 2 + 1].value < heap[current * 2 + 2].value ? current * 2 + 1 : current * 2 + 2;
            }
            if (heap[current].value < heap[child].value)
            {
                break;
            }
            Data temp = heap[current];
            heap[current] = heap[child];
            heap[child] = temp;
            current = child;
        }
        return ret;
    }
}stock_sell[MAX_STOCK + 1];

struct maxPQ
{
    Data heap[MAX_ORDER * 2];
    int heapSize = 0;
    void heapInit(void)
    {
        heapSize = 0;
    }
    void heapPush(int idx, int price, unsigned long long value)
    {
        heap[heapSize].idx = idx;
        heap[heapSize].price = price;
        heap[heapSize].value = value;
        int current = heapSize;
        while (current > 0 && heap[current].value > heap[(current - 1) / 2].value)
        {
            Data temp = heap[(current - 1) / 2];
            heap[(current - 1) / 2] = heap[current];
            heap[current] = temp;
            current = (current - 1) / 2;
        }
        heapSize = heapSize + 1;
    }

    Data heapPop()
    {
        Data ret;
        ret = heap[0];
        heapSize = heapSize - 1;
        heap[0] = heap[heapSize];
        int current = 0;
        while (current * 2 + 1 < heapSize)
        {
            int child;
            if (current * 2 + 2 == heapSize)
            {
                child = current * 2 + 1;
            }
            else
            {
                child = heap[current * 2 + 1].value > heap[current * 2 + 2].value ? current * 2 + 1 : current * 2 + 2;
            }
            if (heap[current].value > heap[child].value)
            {
                break;
            }
            Data temp = heap[current];
            heap[current] = heap[child];
            heap[child] = temp;
            current = child;
        }
        return ret;
    }
}stock_buy[MAX_STOCK + 1];

void init()
{
    stockOrder.init();
    for (int i = 0; i < MAX_STOCK + 1; i++)
    {
        stock_buy[i].heapInit();
        stock_sell[i].heapInit();
    }
}

int buy(int mNumber, int mStock, int mQuantity, int mPrice)
{
    stockOrder.insertOrderInfo(mNumber, mQuantity, mPrice);
    while (stockOrder.order[mNumber].quantity > 0 && stock_sell[mStock].heapSize > 0 && stock_sell[mStock].heap[0].price <= mPrice)
    {
        Data data = stock_sell[mStock].heapPop();
        int idx = data.idx;
        int buyPrice = data.price;
        if (stockOrder.order[idx].quantity == 0) continue;

        if (stockOrder.order[idx].quantity <= stockOrder.order[mNumber].quantity)
        {
            stockOrder.order[mNumber].quantity -= stockOrder.order[idx].quantity;
            stockOrder.order[idx].quantity = 0;
        }
        else
        {
            stockOrder.order[idx].quantity -= stockOrder.order[mNumber].quantity;
            stockOrder.order[mNumber].quantity = 0;
            stock_sell[mStock].heapPush(idx, buyPrice, data.value);
        }
        if (stockOrder.minPrice[mStock] > buyPrice) stockOrder.minPrice[mStock] = buyPrice;
        stockOrder.maxProfit[mStock] = stockOrder.maxProfit[mStock] > (buyPrice - stockOrder.minPrice[mStock]) ? stockOrder.maxProfit[mStock] : (buyPrice - stockOrder.minPrice[mStock]);
    }
    if (stockOrder.order[mNumber].quantity > 0)
    {
        unsigned long long value = (unsigned long long)stockOrder.order[mNumber].price * 1000000 + (MAX_ORDER - mNumber);
        stock_buy[mStock].heapPush(mNumber, mPrice, value);
    }
    return stockOrder.order[mNumber].quantity;
}

int sell(int mNumber, int mStock, int mQuantity, int mPrice)
{
    stockOrder.insertOrderInfo(mNumber, mQuantity, mPrice);
    while (stockOrder.order[mNumber].quantity > 0 && stock_buy[mStock].heapSize > 0 && stock_buy[mStock].heap[0].price >= mPrice)
    {
        Data data = stock_buy[mStock].heapPop();
        int idx = data.idx;
        int sellPrice = data.price;
        if (stockOrder.order[idx].quantity == 0) continue;

        if (stockOrder.order[idx].quantity <= stockOrder.order[mNumber].quantity)
        {
            stockOrder.order[mNumber].quantity -= stockOrder.order[idx].quantity;
            stockOrder.order[idx].quantity = 0;
        }
        else
        {
            stockOrder.order[idx].quantity -= stockOrder.order[mNumber].quantity;
            stockOrder.order[mNumber].quantity = 0;
            stock_buy[mStock].heapPush(idx, sellPrice, data.value);
        }
        if (stockOrder.minPrice[mStock] > sellPrice) stockOrder.minPrice[mStock] = sellPrice;
        stockOrder.maxProfit[mStock] = stockOrder.maxProfit[mStock] > (sellPrice - stockOrder.minPrice[mStock]) ? stockOrder.maxProfit[mStock] : (sellPrice - stockOrder.minPrice[mStock]);
    }
    if (stockOrder.order[mNumber].quantity > 0)
    {
        unsigned long long value = (unsigned long long)stockOrder.order[mNumber].price * 1000000 + mNumber;
        stock_sell[mStock].heapPush(mNumber, mPrice, value);
    }
    return stockOrder.order[mNumber].quantity;
}

void cancel(int mNumber)
{
    stockOrder.order[mNumber].quantity = 0;
}

int bestProfit(int mStock)
{
    return stockOrder.maxProfit[mStock];
}

```
