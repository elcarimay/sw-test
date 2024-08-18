```cpp
#if 1 // 92 ms
#define CAPITAL                         (0)
#define MAX_ORDER                       (10000)
#define MAX_CITY                        (200)
#define MAX_SIZE                        (4 * MAX_ORDER)
#define Servant_returned_to_the_capital (0)
#define Grain_is_expected_to_arrive     (1)
#define Grain_arrived                   (2)
#define Servant_arrived                 (3)

struct City {
    int quantity;
    int projectionQuantity;
    int servantCount;
};

City city[MAX_CITY];

struct CollectionEvent {
    int eventType;
    int eventTime;
    int cID;
    int quantity;
    int priority;
};

struct PriorityQueue {
    CollectionEvent heap[MAX_SIZE];
    int heapSize = 0;

    void heapInit(void)
    {
        heapSize = 0;
    }

    void heapPush(int eventType, int eventTime, int cID, int quantity)
    {
        heap[heapSize].eventType = eventType;
        heap[heapSize].eventTime = eventTime;
        heap[heapSize].cID = cID;
        heap[heapSize].quantity = quantity;
        heap[heapSize].priority = eventTime * 10 + eventType;

        int current = heapSize;

        while (current > 0 && heap[current].priority < heap[(current - 1) / 2].priority) {
            CollectionEvent temp = heap[(current - 1) / 2];
            heap[(current - 1) / 2] = heap[current];
            heap[current] = temp;
            current = (current - 1) / 2;
        }
        heapSize = heapSize + 1;
    }

    CollectionEvent heapPop()
    {
        CollectionEvent value = heap[0];
        heapSize = heapSize - 1;
        heap[0] = heap[heapSize];
        int current = 0;

        while (current * 2 + 1 < heapSize) {
            int child;
            if (current * 2 + 2 == heapSize)
                child = current * 2 + 1;
            else
                child = heap[current * 2 + 1].priority < heap[current * 2 + 2].priority ? current * 2 + 1 : current * 2 + 2;

            if (heap[current].priority < heap[child].priority)
                break;

            CollectionEvent temp = heap[current];
            heap[current] = heap[child];
            heap[child] = temp;
            current = child;
        }
        return value;
    }
};

PriorityQueue eventQueue;

int cityCount;
int abs(int a) { return a < 0 ? -a : a; }
int max(int a, int b) { return a > b ? a : b; }

void init(int N, int M)
{
    eventQueue.heapInit();
    cityCount = N;

    for (int i = 0; i < N; i++) {
        city[i].quantity = 0;
        city[i].projectionQuantity = 0;
        city[i].servantCount = 0;
    }

    city[CAPITAL].servantCount = M;
}

void destroy() { }

int getRichCity() {
    int cID = -1;
    int maxQty = 0;

    for (int i = 1; i < cityCount; i++) {
        if (city[i].servantCount > 0 || city[i].projectionQuantity <= maxQty)
            continue;
        cID = i;
        maxQty = city[i].projectionQuantity;
    }
    return cID;
}

void sendServant(int tStamp) {
    int cID = -1;

    while (city[CAPITAL].servantCount > 0 && (cID = getRichCity()) != -1) {
        eventQueue.heapPush(Servant_arrived, tStamp + cID, cID, -1);
        city[CAPITAL].servantCount -= 1;
        city[cID].servantCount += 1;
    }
}

void handleEvent(int targetTime) {
    while (eventQueue.heapSize > 0 && eventQueue.heap[0].eventTime <= targetTime) {
        CollectionEvent ev = eventQueue.heapPop();

        switch (ev.eventType) {
        case Grain_arrived:
            city[ev.cID].quantity += ev.quantity;
            break;
        case Grain_is_expected_to_arrive:
            city[ev.cID].projectionQuantity += ev.quantity;
            break;
        case Servant_arrived:
            eventQueue.heapPush(Servant_returned_to_the_capital, ev.eventTime + ev.cID, ev.cID, city[ev.cID].quantity);
            city[ev.cID].projectionQuantity -= city[ev.cID].quantity;
            city[ev.cID].quantity = 0;
            break;
        case Servant_returned_to_the_capital:
            city[CAPITAL].quantity += ev.quantity;
            city[CAPITAL].servantCount += 1;
            city[ev.cID].servantCount -= 1;
            break;
        default:
            break;
        }
    }
}

void update(int tStamp) {
    while (eventQueue.heapSize > 0 && eventQueue.heap[0].eventTime <= tStamp) {
        int targetTime = eventQueue.heap[0].eventTime;
        handleEvent(targetTime);
        sendServant(targetTime);
    }
}

int check(int tStamp)
{
    update(tStamp);
    return city[CAPITAL].quantity;
}

int order(int tStamp, int mCityA, int mCityB, int mTax)
{
    int arrivedTime = tStamp + abs(mCityA - mCityB);
    int projectionTime = max(tStamp, arrivedTime - mCityB);
    eventQueue.heapPush(Grain_arrived, arrivedTime, mCityB, mTax);
    eventQueue.heapPush(Grain_is_expected_to_arrive, projectionTime, mCityB, mTax);
    return check(tStamp);
}
#endif // 0 // 92 ms
```
