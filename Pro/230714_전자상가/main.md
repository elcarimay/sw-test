```cpp
#if 1
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>
#include <string.h>

struct Result
{
    int mPrice;
    int mPerformance;
};

extern void init(int mCharge);
extern int stock(int mType, int mPrice, int mPerformance, int mPosition);
extern Result order(int mBudget);

/////////////////////////////////////////////////////////////////////////

#define INIT    0
#define STOCK    1
#define ORDER    2

static bool run()
{
    int cmd, ans, ans2, ret;
    int in, in2, in3, in4;
    int Q = 0;
    bool okay = false;
    Result Ret;

    scanf("%d", &Q);

    for (int q = 0; q < Q; ++q)
    {
        scanf("%d", &cmd);
        if (q == 13) {
            int debug = 1;
        }
        switch (cmd)
        {
        case INIT:
            scanf("%d", &in);
            init(in);
            okay = true;
            break;

        case STOCK:
            scanf("%d %d %d %d", &in, &in2, &in3, &in4);
            ret = stock(in, in2, in3, in4);
            scanf("%d", &ans);
            if (ret != ans)
                okay = false;
            break;

        case ORDER:
            scanf("%d", &in);
            Ret = order(in);
            scanf("%d %d", &ans, &ans2);
            if (Ret.mPrice != ans || Ret.mPerformance != ans2)
                okay = false;
            break;

        default:
            okay = false;
        }
    }

    return okay;
}
#include <time.h>
int main()
{
    clock_t start = clock();
    setbuf(stdout, NULL);
    freopen("TS전자상가.txt", "r", stdin);

    int T, MARK;
    scanf("%d %d", &T, &MARK);

    for (int tc = 1; tc <= T; tc++)
    {
        int score = run() ? MARK : 0;
        printf("#%d %d (%d ms)\n", tc, score, (clock() - start) / (CLOCKS_PER_SEC / 1000));
    }

    printf("\nPerformance : %d ms\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));
    return 0;
}
#endif
```
