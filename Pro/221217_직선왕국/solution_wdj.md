```cpp
#if 1 // 78 ms
#define MAX_CITY  200
#define MAX_ADMIN  50
#define MAX_HEAP  20000

// 절대값 함수 Math Library 사용 못하므로 제작
int abs(int a, int b) { return a >= b ? a - b : b - a;}
// Maximum 함수 Math Library 사용 못하므로 제작
int max(int a, int b) { return a >= b ? a : b; }
// Minimum 함수 Math Library 사용 못하므로 제작
int min(int a, int b) { return a <= b ? a : b; }

// 도시 정보를 찾는 Structure 최대 도시 숫자가 200 밖에 되지 않으므로 일단 할당해놓고 최대 이상은 사용하지 않는 방식으로 처리
struct CityInfo {
    int index;          // 도시 번호
    int tax;            // 현재 쌓여있는 세금의 양
    int taxFromCapital; // 수도에서 지금 사람을 보냈을 때, 가져올 수 있는 세금의 양
    int numAdmin;       // 현재 해당 도시에 존재하는 관리의 숫자
                        // 수도의 경우는 움직일 수 있는 관리의 수, 그 외의 도시는 관리가 파견되었는지 아닌지 여부 확인용
    int priority;
};

CityInfo city[MAX_CITY];

// 관리가 파견될 도시의 우선순위를 결정하는 Queue
// 1. 가져올 수 있는 세금이 많은 도시에 우선적으로 파견한다.
// 2. 가져올 수 있는 세금이 같다면 가까운 도시에 우선적으로 파견한다. 
// Maximum Priority Queue

struct CityQueue {
    CityInfo heap[MAX_CITY];
    int heapSize = 0;
    void heapInit(void){ heapSize = 0; }
    void heapPush(CityInfo city){
        heap[heapSize].index = city.index;
        heap[heapSize].tax = city.tax;
        heap[heapSize].taxFromCapital = city.taxFromCapital;
        heap[heapSize].numAdmin = city.numAdmin;
        heap[heapSize].priority = MAX_CITY * heap[heapSize].taxFromCapital - heap[heapSize].index;
        int current = heapSize;
        while (current > 0 && heap[current].priority > heap[(current - 1) / 2].priority) {
            CityInfo temp = heap[(current - 1) / 2];
            heap[(current - 1) / 2] = heap[current];
            heap[current] = temp;
            current = (current - 1) / 2;
        }
        heapSize = heapSize + 1;
    }
    CityInfo heapPop(){
        CityInfo value = heap[0];
        heapSize = heapSize - 1;
        heap[0] = heap[heapSize];
        int current = 0;
        while (current * 2 + 1 < heapSize) {
            int child;
            if (current * 2 + 2 == heapSize)
                child = current * 2 + 1;
            else
                child = heap[current * 2 + 1].priority > heap[current * 2 + 2].priority ? current * 2 + 1 : current * 2 + 2;
            if (heap[current].priority > heap[child].priority) break;
            CityInfo temp = heap[current];
            heap[current] = heap[child];
            heap[child] = temp;
            current = child;
        }
        return value;
    }
};
CityQueue cityPriority;

// Event Time Table 만들기 위한 Structure
// Event List를 만들어 놓고 Priority Queue를 통해서 시간 순서대로 처리한다.
// Event 0: 시간 time에 도시 city에 세금이 tax만큼 변경: 필요한 인자
//          Type = 0, City = 변경된 도시, Time = 변경 시간, Tax = 세금 변경 양, Option = 출발지 
// Event 1: 시간 time에 도시 city에 관리가 도착
//          Type = 1, City = 도착한 도시, Time = 도착 시간, Tax = -1, Option = -1
// Event 2: 시간 time에 수도에서 출발하였을 때 도시 city에서 가져올 수 있는 세금이 tax만큼 변경
//          Type = 2, City = 변경된 도시, Time = 변경 시간, Tax = 세금 변경 양, Option = -1
struct Event {
    int type, time, city, tax, option, priority;
};

// Event Time Table Minimum Priority Queue
struct PriorityQueue {
    Event heap[MAX_HEAP];
    int heapSize = 0;
    void heapInit(void){ heapSize = 0; }
    void heapPush(int type, int time, int city, int tax, int option){
        heap[heapSize].type = type;
        heap[heapSize].city = city;
        heap[heapSize].time = time;
        heap[heapSize].tax = tax;
        heap[heapSize].option = option;
        heap[heapSize].priority = time * 10 + type;
        int current = heapSize;
        while (current > 0 && heap[current].priority < heap[(current - 1) / 2].priority) {
            Event temp = heap[(current - 1) / 2];
            heap[(current - 1) / 2] = heap[current];
            heap[current] = temp;
            current = (current - 1) / 2;
        }
        heapSize = heapSize + 1;
    }
    Event heapPop(){
        Event value = heap[0];
        heapSize = heapSize - 1;
        heap[0] = heap[heapSize];
        int current = 0;
        while (current * 2 + 1 < heapSize) {
            int child;
            if (current * 2 + 2 == heapSize)
                child = current * 2 + 1;
            else
                child = heap[current * 2 + 1].priority < heap[current * 2 + 2].priority ? current * 2 + 1 : current * 2 + 2;
            if (heap[current].priority < heap[child].priority) break;
            Event temp = heap[current];
            heap[current] = heap[child];
            heap[child] = temp;
            current = child;
        }
        return value;
    }
};
PriorityQueue eventQueue;

// 초기 상태
// 도시는 N개 존재하며 창고는 모두 비었다.
// 관리는 수도에 M명 존재하고, 나머지 도시에는 모두 관리가 없다.
void init(int N, int M){
    for (int i = 0; i < N; i++) {
        city[i].index = i;
        city[i].tax = 0;
        city[i].taxFromCapital = 0;
        city[i].numAdmin = 0;
    }
    city[0].numAdmin = M;
    cityPriority.heapInit();
    eventQueue.heapInit();
}

// 프로그램 종료시 호출 빈 함수
void destroy() { }

void checkAdmin(int time) {
    while (cityPriority.heapSize > 0 && city[0].numAdmin > 0) {
        CityInfo target = cityPriority.heapPop();
        if (target.numAdmin == 0 && target.taxFromCapital == city[target.index].taxFromCapital) {
            eventQueue.heapPush(1, time + target.index, target.index, -1, -1);
            city[0].numAdmin -= 1;
            city[target.index].numAdmin += 1;
        }
    }
}

// 최신 TimeStamp까지 EventQueue에 들어있는 Event의 결과를 처리한다.
void update(int tStamp) {
    while (eventQueue.heapSize > 0 && eventQueue.heap[0].time <= tStamp) {
        Event event = eventQueue.heapPop();
        switch (event.type) {
        case 0:
            city[event.city].tax += event.tax;
            if (event.city == 0) {
                city[0].numAdmin += 1;
                city[event.option].numAdmin -= 1;
                if (city[event.option].taxFromCapital > 0)
                    cityPriority.heapPush(city[event.option]);
            }
            break;
        case 1:
            eventQueue.heapPush(0, event.time + event.city, 0, city[event.city].tax, event.city);
            city[event.city].taxFromCapital -= city[event.city].tax;
            city[event.city].tax = 0;
            break;
        case 2:
            city[event.city].taxFromCapital += event.tax;
            if (city[event.city].numAdmin == 0)
                cityPriority.heapPush(city[event.city]);
            break;
        }
        if (eventQueue.heapSize == 0 || event.time < eventQueue.heap[0].time)
            checkAdmin(event.time);
    }
}

// 명령까지 추가한 다음에
// tStamp까지 Event를 업데이트하고 그 시점에서 Capital에 있는 세금을 계산한다.
int order(int tStamp, int mCityA, int mCityB, int mTax){
    // Time tStamp + abs(mCityA, mCityB)에 mCityB에 Tax 변경
    eventQueue.heapPush(0, tStamp + abs(mCityA, mCityB), mCityB, mTax, mCityA);
    // max(tStamp, tStamp + abs(mCityA, mCityB) - mCityB)에 mCityB에서 수도에서 보는 Tax 증가
    eventQueue.heapPush(2, max(tStamp, tStamp + abs(mCityA, mCityB) - mCityB), mCityB, mTax, -1);
    update(tStamp);
    return city[0].tax;
}

// tStamp까지 Event를 업데이트하고 그 시점에서 Capital에 있는 세금을 계산한다.
int check(int tStamp) {
    update(tStamp);
    return city[0].tax;
}
#endif // 1 // 78 ms
```
