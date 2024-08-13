```cpp
#if 1 // 86 ms
/*
* union-find
*
* @file: [H2253] [Pro] 좌석배정
* @brief: 샘플 답안
* @copyright: All rights reserved (c) 2022 Samsung Electronics, Inc.
*/

#define MAX_N 30000
#define MIN(a,b) ((a)<(b)?(a):(b))
#define MAX(a,b) ((a)>(b)?(a):(b))
#define ABS(a) ((a)>0?(a):(-(a)))

int W, H;

int seat[MAX_N + 1];
int parent[8][MAX_N + 1];
int rank[8][MAX_N + 1];
int candidate[8][MAX_N + 1];

int dx[8] = { 1, 1, 0, -1, -1, -1, 0, 1 };
int dy[8] = { 0, 1, 1, 1, 0, -1, -1, -1 };

void init(int W, int H)
{
    ::W = W;
    ::H = H;

    for (int i = 1; i <= W * H; i++)
    {
        seat[i] = 0;
        for (int d = 0; d < 8; d++)
        {
            parent[d][i] = i;
            rank[d][i] = 0;
            candidate[d][i] = i;
        }
    }
}

int calcDist(int a, int b)
{
    a--; b--;
    int dx = (a % W) - (b % W);
    int dy = (a / W) - (b / W);
    return MAX(ABS(dx), ABS(dy));
}

int find(int d, int mSeatNum)
{
    int& p = parent[d][mSeatNum];
    if (p == mSeatNum)
        return mSeatNum;
    return p = find(d, p); // 경로압축
}

void unionSeat(int d, int a, int b) // d방향의 a, b 그룹을 합침
{
    a = find(d, a);
    b = find(d, b);

    if (a == b)
        return;

    int aRank = rank[d][a];
    int aCand = candidate[d][a];
    int bRank = rank[d][b];
    int bCand = candidate[d][b];

    int cand = d < 4 ? MAX(aCand, bCand) : MIN(aCand, bCand);

    if (aRank < bRank)
    {
        parent[d][a] = b;
        candidate[d][b] = cand;
    }
    else if (aRank > bRank)
    {
        parent[d][b] = a;
        candidate[d][a] = cand;
    }
    else
    {
        parent[d][b] = a;
        candidate[d][a] = cand;
        rank[d][a]++;
    }
}

void unionSeatAllDir(int mSeatNum)
{
    int x = (mSeatNum - 1) % W;
    int y = (mSeatNum - 1) / W;

    for (int d = 0; d < 8; d++)     // 모든 방향별로 다음그룹과 합치는 과정
    {
        int nx = x + dx[d];
        int ny = y + dy[d];

        if (nx < 0 || nx >= W)
            continue;
        if (ny < 0 || ny >= H)
            continue;

        int nextSeatNum = nx + ny * W + 1;
        unionSeat(d, mSeatNum, nextSeatNum);
    }
}

int selectSeat(int mSeatNum)
{
    int ans = 0, dist = 100000;
    for (int d = 0; d < 8; d++)
    {
        int p = find(d, mSeatNum);      // root 노드 번호
        int cand = candidate[d][p];
        if (seat[cand] != 0)
            continue;

        int ndist = calcDist(mSeatNum, cand);
        if (dist > ndist)
        {
            ans = cand;
            dist = ndist;
        }
    }

    if (ans == 0)
        return 0;

    unionSeatAllDir(ans);
    seat[ans] = mSeatNum;
    return ans;
}
#endif
```
