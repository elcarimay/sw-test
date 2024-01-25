```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>
#include <time.h>

void init(int N, int mBrands[]);
void connect(int mHotelA, int mHotelB, int mDistance);
int merge(int mHotelA, int mHotelB);
int move(int mStart, int mBrandA, int mBrandB);

#define MAX_N 5000
#define CMD_INIT 100
#define CMD_CONNECT 200
#define CMD_MERGE 300
#define CMD_MOVE 400

static bool run()
{
    int query_num;
    scanf("%d", &query_num);
    int ans;
    bool ok = false;
    for (int q = 0; q < query_num; q++)
    {
        int query;
        scanf("%d", &query);
        if (query == CMD_INIT)
        {
            int N;
            int mBrands[MAX_N];
            scanf("%d", &N);
            for (int i = 0; i < N; i++) {
                scanf("%d", &mBrands[i]);
            }
            init(N, mBrands);
            ok = true;
        }
        else if (query == CMD_CONNECT)
        {
            int mHotelA, mHotelB, mDistance;
            scanf("%d %d %d", &mHotelA, &mHotelB, &mDistance);
            connect(mHotelA, mHotelB, mDistance);
        }
        else if (query == CMD_MERGE)
        {
            int mHotelA, mHotelB;
            scanf("%d %d", &mHotelA, &mHotelB);
            int ret = merge(mHotelA, mHotelB);
            scanf("%d", &ans);
            if (ans != ret)
            {
                ok = false;
            }
        }
        else if (query == CMD_MOVE)
        {
            int mStart, mBrandA, mBrandB;
            scanf("%d %d %d", &mStart, &mBrandA, &mBrandB);
            int ret = move(mStart, mBrandA, mBrandB);
            scanf("%d", &ans);
            if (ans != ret)
            {
                ok = false;
            }
        }
    }
    return ok;
}

int main()
{
    clock_t start = clock();
    setbuf(stdout, NULL);
    freopen("sample_input.txt", "r", stdin);
    int T, MARK;
    scanf("%d %d", &T, &MARK);
    for (int tc = 1; tc <= T; tc++)
    {
        clock_t tc_start = clock();
        int score = run() ? MARK : 0;
        int tc_result = (clock() - tc_start) / (CLOCKS_PER_SEC / 1000);
        printf("#%d %d (Result: %d ms)\n", tc, score, tc_result);
    }
    int result = (clock() - start) / (CLOCKS_PER_SEC / 1000);
    printf("\n>> Result: %d ms\n", result);
    return 0;
}
```
