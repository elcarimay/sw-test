```cpp
#include<unordered_map>
#include<queue>
using namespace std;
using pii = pair<int, int>;

struct Buy { int pid, price, total, remain; };

struct Product {
    int total;
    priority_queue<pii, vector<pii>, greater<>> pq; // {price, bid}
};

unordered_map<int, Buy> B;
unordered_map<int, Product> P;
unordered_map<int, vector<pii>> S;           // {bid, cnt}

void init() {
    B.clear();
    P.clear();
    S.clear();
}

int buy(int BID, int product, int price, int quantity) {
    B[BID] = { product, price, quantity, quantity };
    auto& p = P[product];
    p.total += quantity;
    p.pq.push({ price, BID });
    return p.total;
}

int cancel(int BID) {
    auto it = B.find(BID);
    if (it == B.end()) return -1;

    auto& b = it->second;
    if (b.total != b.remain) return -1;

    auto& p = P[it->second.pid];
    p.total -= it->second.total;
    B.erase(it);

    return p.total;
}

int sell(int SID, int product, int price, int quantity) {
    auto& p = P[product];
    if (p.total < quantity) return -1;
    p.total -= quantity;
    int ret = 0;
    auto& pq = p.pq;
    auto& s = S[SID];
    while (quantity) {
        int buyPrice = pq.top().first;
        int bid = pq.top().second;

        auto bit = B.find(bid);
        if (bit == B.end() || bit->second.remain == 0) {
            pq.pop();
            continue;
        }

        auto& b = bit->second;
        int cnt = min(b.remain, quantity);
        quantity -= cnt;
        b.remain -= cnt;
        ret += (price - buyPrice) * cnt;
        if (b.remain == 0) pq.pop();
        if (cnt) s.push_back({ bid, cnt });
    }
    return ret;
}

int refund(int SID) {
    auto it = S.find(SID);
    if (it == S.end()) return -1;

    int ret = 0;
    for (pii d : it->second) {
        int bid = d.first, cnt = d.second;
        auto& b = B[bid];
        auto& p = P[b.pid];
        if (!b.remain) p.pq.push({ b.price, bid });

        ret += cnt;
        p.total += cnt;
        b.remain += cnt;
    }
    S.erase(it);
    return ret;
}
```
