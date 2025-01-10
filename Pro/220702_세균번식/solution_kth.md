```cpp
#include <vector>
#include <set>
#include <queue>
using namespace std;

#define MAXN 100

int N, (*emap)[MAXN], bmap[MAXN][MAXN];
bool visit[MAXN][MAXN];
int dr[] = { 0,-1,0,1 }, dc[] = { 1,0,-1,0 };
int cnt[3]; // cnt1: 파란세균, cnt2: 빨간세균

struct Data {
    int row, col, energy;
    bool operator<(const Data& r)const {
        if (energy != r.energy) return energy < r.energy;
        if (row != r.row) return row > r.row;
        return col > r.col;
    }
};

priority_queue<Data> pq;
struct Pos {
    int r, c;
};
Pos que[10003]; int head, tail;
void init(int N, int mDish[MAXN][MAXN]){
    ::N = N, emap = mDish;
    memset(cnt, 0, sizeof(cnt));
    for (int i = 0; i < N; i++) for (int j = 0; j < N; j++)
        visit[i][j] = 0, bmap[i][j] = 0;
}

int dropMedicine(int mTarget, int mRow, int mCol, int mEnergy){ // 1: 파란세균, 2: 빨간세균
    int r = --mRow, c = --mCol;
    if (bmap[r][c] != mTarget && bmap[r][c] != 0) return cnt[mTarget];
    if (bmap[r][c] == 0) cnt[mTarget]++, mEnergy -= emap[r][c], bmap[r][c] = mTarget;
    pq = {};
    memset(visit, 0, sizeof(visit));
    head = tail = 0;
    que[tail++] = { r, c };
    visit[r][c] = true;
    while (mEnergy > 0) {
        while (head < tail) {
            Pos cur = que[head++];
            for (int i = 0; i < 4; i++) {
                int nr = cur.r + dr[i], nc = cur.c + dc[i];
                if (nr < 0 || nr >= N || nc < 0 || nc >= N) continue;
                if (visit[nr][nc]) continue;
                visit[nr][nc] = true;
                if (bmap[nr][nc] == 0) pq.push({ nr, nc, emap[nr][nc] }); // 번식 후보지 등록
                if (bmap[nr][nc] == mTarget) que[tail++] = { nr, nc }; // 세균 활성화
            }
        }
        if (pq.empty()) break;
        Data cur = pq.top(); pq.pop();
        bmap[cur.row][cur.col] = mTarget; // 세균 생성
        que[tail++] = { cur.row, cur.col }; // 세균 활성화
        cnt[mTarget]++;
        mEnergy -= emap[cur.row][cur.col];
    }
    return cnt[mTarget];
}

int cleanBacteria(int mRow, int mCol){
    int r = --mRow, c = --mCol;
    if (bmap[r][c] == 0) return -1;
    int t = bmap[r][c];
    head = tail = 0;
    que[tail++] = { r, c };
    bmap[r][c] = 0;
    while (head < tail) {
        Pos cur = que[head++];
        for (int i = 0; i < 4; i++) {
            int nr = cur.r + dr[i], nc = cur.c + dc[i];
            if (nr < 0 || nr >= N || nc < 0 || nc >= N) continue;
            if (bmap[nr][nc] != t) continue;
            que[tail++] = { nr,nc };
            bmap[nr][nc] = 0;
        }
    }
    return cnt[t] -= tail;
}
```
