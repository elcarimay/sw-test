```cpp
#include <vector>
#include <unordered_map>
using namespace std;
using pii = pair<int, int>;

struct Data {
    int mLength, mRow, mCol, mDir;
};

unordered_map<int, Data> m;
int map[201][201];
bool visit[201][201];
int N;
void init(int N){
    ::N = N;
    for (int i = 0; i < N; i++) for (int j = 0; j < N; j++)
        map[i][j] = 0;
    m.clear();
}

int dr[] = { -1, 1, 0, 0 }, dc[] = { 0,0, -1, 1 };

pii que[200 * 200 + 3];
int head, tail;
void bfs(int r, int c) {
    head = tail = 0;
    que[tail++] = { r,c };
    while (head < tail) {
        pii cur = que[head++];
        for (int i = 0; i < 4; i++) {
            int nr = cur.first + dr[i];
            int nc = cur.second + dc[i];
            if (nr < 0 || nr >= N || nc < 0 || nc >= N) continue;
            if (map[nr][nc] || visit[nr][nc]) continue;
            que[tail++] = { nr, nc };
            visit[nr][nc] = true;
        }
    }
}

int addBar(int mID, int mLength, int mRow, int mCol, int mDir){
    m[mID] = { mLength, mRow, mCol, mDir };
    for (int i = 0; i < mLength; i++) {
        int nr = mRow - 1 + dr[mDir] * i;
        int nc = mCol - 1 + dc[mDir] * i;
        map[nr][nc] = mID;
    }
    int cnt = 0;
    for (int i = 0; i < N; i++) for (int j = 0; j < N; j++)
        visit[i][j] = false;
    for (int i = 0; i < N; i++) for (int j = 0; j < N; j++) {
        if (map[i][j] || visit[i][j]) continue;
        bfs(i, j);
        cnt++;
    }
    return cnt;
}

int removeBar(int mID){
    Data tmp = m[mID];
    int mLength = tmp.mLength;
    int mRow = tmp.mRow;
    int mCol = tmp.mCol;
    int mDir = tmp.mDir;
    for (int i = 0; i < mLength; i++) {
        int nr = mRow - 1 + dr[mDir] * i;
        int nc = mCol - 1 + dc[mDir] * i;
        map[nr][nc] -= mID;
    }
    int cnt = 0;
    for (int i = 0; i < N; i++) for (int j = 0; j < N; j++)
        visit[i][j] = false;
    for (int i = 0; i < N; i++) for (int j = 0; j < N; j++) {
        if (map[i][j] || visit[i][j]) continue;
        bfs(i, j);
        cnt++;
    }
    return cnt;
}
```
