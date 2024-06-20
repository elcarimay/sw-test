```cpp
#if 1
#include <queue>
#include <vector>
#include <cstdio>
#include <string.h>
#include <algorithm>
using namespace std;

#define MAX_H 1'001
#define MAX_W 200'001
#define INF 987654321

struct Box
{
    int len, ea, eb, col, row;
}box[10001];

int H, W;

struct Data
{
    int to, cost;
    bool operator<(const Data& r)const {
        return cost > r.cost;
    }
};

vector<Data> adj[10001];

struct Info
{
    int id, col, len;
};
vector<Info> v[MAX_H];

int currentRow;
void init(int mH, int mW){
    H = mH, W = mW; // 세로, 가로
    currentRow = H - 1;
    for (int i = 0; i < H; i++) v[i].clear();
    for (int i = 0; i < 10001; i++) box[i] = {}, adj[i].clear();
}

int connect[2]; int head, tail;
int dropBox(int mId, int mLen, int mExitA, int mExitB, int mCol){
    box[mId] = { mLen, mExitA, mExitB, mCol }; // 좌우 출구는 0, mLen-1에 위치
    auto& b = box[mId]; bool flag = 1; head = tail = 0;
    while (1) {
        for (int i = 0;i < v[currentRow].size();i++) {
            auto& nx = v[currentRow][i];
            if (nx.col <= b.col + b.len - 1 && b.col + b.len - 1 < nx.col + nx.len) { // 오른쪽이 걸릴때
                currentRow--; flag = 0; break;
            }
            if (nx.col <= b.col && b.col < nx.col + nx.len) { // 왼쪽이 걸릴때
                currentRow--; flag = 0; break;
            }
            if (b.col < nx.col && nx.col + nx.len - 1 < b.col + b.len - 1) { // 기존꺼를 완전히 포함할때
                currentRow--; flag = 0; break;
            }
            if (nx.col < b.col && b.col + b.len - 1 < nx.col + nx.len - 1) { // 기존꺼에 완전히 포함될때
                currentRow--; flag = 0; break;
            }
        }
        if (!flag) flag = 1;
        else break;
    }
    while (head == tail){
        for (int i = 0;i < v[currentRow + 1].size();i++) {
            auto& nx = v[currentRow + 1][i];
            if (nx.col <= b.col + b.len - 1 && b.col + b.len - 1 < nx.col + nx.len) { // 오른쪽이 걸릴때
                connect[tail++] = nx.id; continue;
            }
            if (nx.col <= b.col && b.col < nx.col + nx.len) { // 왼쪽이 걸릴때
                connect[tail++] = nx.id; continue;
            }
            if (b.col < nx.col && nx.col + nx.len - 1 < b.col + b.len - 1) { // 기존꺼를 완전히 포함할때
                connect[tail++] = nx.id; continue;
            }
            if (nx.col < b.col && b.col + b.len - 1 < nx.col + nx.len - 1) { // 기존꺼에 완전히 포함될때
                connect[tail++] = nx.id; continue;
            }
        }
        if (head == tail && currentRow == H - 1) break;
        if (head == tail) currentRow++;
    } 
    
    for (int i = 0;i < tail;i++) { // 하부
        int id = connect[i];  bool f1 = 0, f2= 0, f3 = 0, f4 = 0; // f1: 신규 ea, f2: 신규: eb, f3: 기존: ea, f4:기존 eb
        if (box[id].col <= b.col + b.ea && b.col + b.ea < box[id].col + box[id].len) {
            f1 = 1;
            if (b.col + b.ea == box[id].ea) f3 = 1;
            else if (b.col + b.ea == box[id].eb) f4 = 1;
        }
        if (box[id].col <= b.col + b.eb && b.col + b.eb < box[id].col + box[id].len) {
            f2 = 1;
            if (b.col + b.eb == box[id].ea) f3 = 1;
            else if (b.col + b.eb == box[id].eb) f4 = 1;
        }
        if (b.col <= box[id].col + box[id].ea && box[id].col + box[id].ea < b.col + b.len) {
            f3 = 1;
            if (box[id].col + box[id].ea == b.ea) f1 = 1;
            else if (box[id].col + box[id].ea == b.eb) f2 = 1;
        }
        if (b.col <= box[id].col + box[id].eb && box[id].col + box[id].eb < b.col + b.len) {
            f4 = 1;
            if (box[id].col + box[id].eb == b.ea) f1 = 1;
            else if (box[id].col + box[id].eb == b.eb) f2 = 1;
        }
        if(f1 || f2) adj[mId].push_back({ id, 1 });
        if(f3 || f4) adj[id].push_back({ mId, 1 });
    }
    for (int i = 0;i < v[currentRow].size();i++) {
        auto& nx = v[currentRow][i];
        if (nx.col + nx.len == b.col) {
            adj[nx.id].push_back({ mId, 1 });
            adj[mId].push_back({ nx.id, 1 });
        }
        else if (b.col + b.len == nx.col) {
            adj[nx.id].push_back({ mId, 1 });
            adj[mId].push_back({ nx.id, 1 });
        }
    }
    v[currentRow].push_back({ mId, b.col, b.len });
    return currentRow;
}

int cost[10001];
int dijkstra(int s, int e) {
    fill(cost + 1, cost + 10000, INF);
    cost[s] = 0; priority_queue<Data> PQ;
    PQ.push({s, 0});
    while (!PQ.empty()) {
        auto cur = PQ.top(); PQ.pop();
        if (cur.cost > cost[cur.to]) continue;
        if (cur.to == e) return cost[e];
        for (auto nx : adj[cur.to]) {
            int nextCost = nx.cost + cost[cur.to];
            if (cost[nx.to] > nextCost) {
                cost[nx.to] = nextCost;
                PQ.push({nx.to, cost[nx.to]});
            }
        }
    }
    return -1;
}

int explore(int mIdA, int mIdB){
    return dijkstra(mIdA, mIdB);
}
#endif
```
