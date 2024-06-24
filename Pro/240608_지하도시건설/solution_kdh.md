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
};

vector<Data> adj[10001];

struct Info
{
    int id, col, len;
};
vector<Info> v[MAX_H];

int currentRow;
void init(int mH, int mW) {
    H = mH, W = mW; // 세로, 가로
    currentRow = H - 1;
    for (int i = 0; i < H; i++) v[i].clear();
    for (int i = 0; i < 10001; i++) box[i] = {}, adj[i].clear();
}

int connect[2]; int head, tail;
int dropBox(int mId, int mLen, int mExitA, int mExitB, int mCol) {
    box[mId] = { mLen, mExitA, mExitB, mCol }; // 좌우 출구는 0, mLen-1에 위치
    auto& b = box[mId]; bool flag = 1; head = tail = 0;
    while (1) {
        for (int i = 0; i < v[currentRow].size(); i++) {
            auto& old = v[currentRow][i];
            if (!(old.col + old.len - 1 < b.col || b.col + b.len - 1 < old.col)) {
                currentRow--; flag = 0; break;
            }
        }
        if (!flag) flag = 1;
        else break;
    }
    while (head == tail) {
        head = tail = 0;
        for (int i = 0; i < v[currentRow + 1].size(); i++) {
            auto& old = v[currentRow + 1][i];
            if (!(old.col + old.len - 1 < b.col || b.col + b.len - 1 < old.col)) {
                connect[tail++] = old.id; continue;
            }
        }
        if (head == tail && currentRow == H - 1) break;
        if (head == tail) currentRow++;
    }

    for (int i = 0; i < tail; i++) { // 상하
        int id = connect[i];
        if (box[id].col <= b.col + b.ea && b.col + b.ea < box[id].col + box[id].len) adj[mId].push_back({ id, 1 });
        else if (box[id].col <= b.col + b.eb && b.col + b.eb < box[id].col + box[id].len) adj[mId].push_back({ id, 1 });
        if (b.col <= box[id].col + box[id].ea && box[id].col + box[id].ea < b.col + b.len) adj[id].push_back({ mId, 1 });
        else if (b.col <= box[id].col + box[id].eb && box[id].col + box[id].eb < b.col + b.len) adj[id].push_back({ mId, 1 });
    }
    for (int i = 0; i < v[currentRow].size(); i++) { // 좌우
        auto& nx = v[currentRow][i];
        if (nx.col + nx.len == b.col)
            adj[nx.id].push_back({ mId, 1 }), adj[mId].push_back({ nx.id, 1 });
        else if (b.col + b.len == nx.col)
            adj[nx.id].push_back({ mId, 1 }), adj[mId].push_back({ nx.id, 1 });
    }
    v[currentRow].push_back({ mId, b.col, b.len });
    return currentRow;
}

int que[10001], visit[10001];
int explore(int mIdA, int mIdB) {
    memset(visit, 0, sizeof(visit));
    head = tail = 0, que[tail++] = mIdA, visit[mIdA] = 1;
    while (head < tail) {
        int cur = que[head++];
        if (cur == mIdB) return visit[cur] - 1;
        for (int i = 0; i < adj[cur].size(); i++) {
            if (!visit[adj[cur][i].to]) {
                que[tail++] = adj[cur][i].to;
                visit[adj[cur][i].to] = visit[cur] + 1;
            }
        }
    }
    return -1;
}
#endif
```
