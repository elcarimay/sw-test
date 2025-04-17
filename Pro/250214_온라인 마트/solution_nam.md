```cpp
#if 1
#include <vector>
#include <queue>
#include <unordered_map>
using namespace std;

#define MAX_ITEMS 50000
#define MAX_CATEGORIES 6
#define MAX_COMPANIES 6

struct RESULT { int cnt, IDs[5]; };

struct Item {
    int mID, mCategory, mCompany, mPrice;
    bool isClosed;
} items[MAX_ITEMS];
int itemCnt;
unordered_map<int, int> itemMap;

struct Data {
    int mID, mPrice;
    bool operator<(const Data& other) const {
        return (mPrice > other.mPrice) || (mPrice == other.mPrice && mID > other.mID);
    }
};

priority_queue<Data> itemPQ[MAX_CATEGORIES][MAX_COMPANIES];
int offsetDC[MAX_CATEGORIES][MAX_COMPANIES];
int activeCnt[MAX_CATEGORIES][MAX_COMPANIES];

/////////////////////////////////////////////////////////////////////
void init() {
    itemCnt = 0, itemMap.clear();

    for (int i = 1; i < MAX_CATEGORIES; i++) for (int j = 1; j < MAX_COMPANIES; j++)
        itemPQ[i][j] = {}, offsetDC[i][j] = 0, activeCnt[i][j] = 0;
}

void cleanPQ(int category, int company) { // 우선순위 큐에서 유효하지 않은 항목 제거
    auto& pq = itemPQ[category][company];
    while (!pq.empty()) {
        auto top = pq.top();
        int idx = itemMap[top.mID];
        if (items[idx].isClosed || items[idx].mPrice != top.mPrice) pq.pop();
        else break;
    }
}

int sell(int mID, int mCategory, int mCompany, int mPrice) {
    int idx = itemMap[mID] = itemCnt++;
    int storedPrice = mPrice + offsetDC[mCategory][mCompany]; // 오프셋을 적용한 가격 저장
    items[idx] = { mID, mCategory, mCompany, storedPrice, false };
    itemPQ[mCategory][mCompany].push({ mID, storedPrice }); // 해당 리스트와 우선순위 큐에 추가
    activeCnt[mCategory][mCompany]++; // 활성 카운트 증가
    return activeCnt[mCategory][mCompany];
}

int closeSale(int mID) {
    if (itemMap.find(mID) == itemMap.end()) return -1;
    int idx = itemMap[mID];
    Item& item = items[idx];
    if (item.isClosed) return -1;
    int realPrice = item.mPrice - offsetDC[item.mCategory][item.mCompany];
    item.isClosed = true;
    activeCnt[item.mCategory][item.mCompany]--;
    return realPrice;
}

int discount(int mCategory, int mCompany, int mAmount) {
    if (activeCnt[mCategory][mCompany] == 0) return 0;
    offsetDC[mCategory][mCompany] += mAmount; // lazy discount offset 업데이트
    while (true) { // 할인 적용 후 가격이 0 이하인 상품 판매 종료 처리
        cleanPQ(mCategory, mCompany);
        if (itemPQ[mCategory][mCompany].empty()) break;
        Data top = itemPQ[mCategory][mCompany].top();
        int effectivePrice = top.mPrice - offsetDC[mCategory][mCompany];
        if (effectivePrice <= 0) {
            int idx = itemMap[top.mID];
            items[idx].isClosed = true;
            activeCnt[mCategory][mCompany]--;
            itemPQ[mCategory][mCompany].pop();
        }
        else break;
    }
    return activeCnt[mCategory][mCompany];
}

struct Node { // show 함수에서 사용할 노드 구조체
    int category, company;
    Data data;
    bool operator<(const Node& other) const {
        int priceA = data.mPrice - offsetDC[category][company];
        int priceB = other.data.mPrice - offsetDC[other.category][other.company];
        return (priceA > priceB) || (priceA == priceB && data.mID > other.data.mID);
    }
};

RESULT show(int mHow, int mCode) {
    RESULT result = { 0, {0, 0, 0, 0, 0} };
    vector<pair<int, int>> targets;
    if (mHow == 0) {
        for (int c = 1; c < MAX_CATEGORIES; c++) for (int p = 1; p < MAX_COMPANIES; p++)
            targets.push_back({ c, p });
    }
    else if (mHow == 1) for (int p = 1; p < MAX_COMPANIES; p++)
        targets.push_back({ mCode, p });
    else if (mHow == 2) for (int c = 1; c < MAX_CATEGORIES; c++)
        targets.push_back({ c, mCode });
    
    priority_queue<Node> mergedPQ; // 각 카테고리/회사별 우선순위 큐에서 최상위 항목 가져오기
    for (auto& target : targets) {
        int c = target.first, p = target.second;
        cleanPQ(c, p);
        if (!itemPQ[c][p].empty()) mergedPQ.push({ c, p, itemPQ[c][p].top() });
    }

    vector<Node> popped;
    while (result.cnt < 5 && !mergedPQ.empty()) {
        Node curr = mergedPQ.top(); mergedPQ.pop();
        int c = curr.category, p = curr.company;
        result.IDs[result.cnt++] = curr.data.mID;
        itemPQ[c][p].pop(); // 해당 PQ에서 항목 제거 후 임시 저장
        popped.push_back(curr);
        cleanPQ(c, p); // 다음 항목 가져오기
        if (!itemPQ[c][p].empty()) mergedPQ.push({ c, p, itemPQ[c][p].top() });
    }
    for (auto& node : popped) // 원래 상태로 복원
        itemPQ[node.category][node.company].push(node.data);
    return result;
}
#endif // 1
```
```
