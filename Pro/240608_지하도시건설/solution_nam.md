```cpp
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

int H, W, arr[MAX_W];
vector<int> adj[10001], v[MAX_H];

void init(int mH, int mW) {
    H = mH, W = mW; // 세로, 가로
    for (int i = 0; i < H; i++) v[i].clear();
    for (int i = 0; i < W; i++) arr[i] = H;
    for (int i = 0; i < 10001; i++) box[i] = {}, adj[i].clear();
}

int dropBox(int mId, int mLen, int mExitA, int mExitB, int mCol) {
    box[mId] = { mLen, mExitA, mExitB, mCol }; // 좌우 출구는 0, mLen-1에 위치
    auto& b = box[mId];

    int row = H;
    for (int i = mCol; i < mCol + mLen; i++) row = min(row, arr[i]);
    row--;
    for (int i = 0; i < v[row].size(); i++) { // 좌우
        auto& nx = box[v[row][i]];
        if ((nx.col + nx.len == b.col) || (b.col + b.len == nx.col))
            adj[v[row][i]].push_back(mId), adj[mId].push_back(v[row][i]);
    }
    if (row < H - 1)
        for (int i = 0; i < v[row + 1].size(); i++) { // 상하
            auto& nx = box[v[row + 1][i]];
            if (nx.col <= b.col + b.ea && b.col + b.ea < nx.col + nx.len) adj[mId].push_back(v[row + 1][i]);
            else if (nx.col <= b.col + b.eb && b.col + b.eb < nx.col + nx.len) adj[mId].push_back(v[row + 1][i]);
            if (b.col <= nx.col + nx.ea && nx.col + nx.ea < b.col + b.len) adj[v[row + 1][i]].push_back(mId);
            else if (b.col <= nx.col + nx.eb && nx.col + nx.eb < b.col + b.len) adj[v[row + 1][i]].push_back(mId);
        }
    v[row].push_back(mId);
    for (int i = mCol; i < mCol + mLen; i++) arr[i] = row;
    return row;
}

int head, tail, que[10001], visit[10001];
int explore(int mIdA, int mIdB) {
    memset(visit, 0, sizeof(visit));
    head = tail = 0, que[tail++] = mIdA, visit[mIdA] = 1;
    while (head < tail) {
        int cur = que[head++];
        if (cur == mIdB) return visit[cur] - 1;
        for (int i = 0; i < adj[cur].size(); i++) {
            if (!visit[adj[cur][i]]) {
                que[tail++] = adj[cur][i];
                visit[adj[cur][i]] = visit[cur] + 1;
            }
        }
    }
    return -1;
}
```
