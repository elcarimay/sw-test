```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

#define CMD_INIT 100
#define CMD_DESTROY 200
#define CMD_ORDER 300
#define CMD_CHECK 400

extern void init(int N, int M);
extern void destroy();
extern int order(int tStamp, int mCityA, int mCityB, int mTax);
extern int check(int tStamp);

static int run()
{
    int C;
    int isOK = 0;
    int cmd, ret, chk;

    int mN, mM;
    int mTStamp;

    int mCityA, mCityB, mTax;

    scanf("%d", &C);

    for (int c = 0; c < C; ++c)
    {
        scanf("%d", &cmd);
        switch (cmd)
        {
        case CMD_INIT:
            scanf("%d %d ", &mN, &mM);
            init(mN, mM);
            isOK = 1;
            break;

        case CMD_ORDER:
            scanf("%d %d %d %d", &mTStamp, &mCityA, &mCityB, &mTax);
            ret = order(mTStamp, mCityA, mCityB, mTax);
            scanf("%d", &chk);
            if (ret != chk)
                isOK = 0;
            break;

        case CMD_CHECK:
            scanf("%d", &mTStamp);
            ret = check(mTStamp);
            scanf("%d", &chk);
            if (ret != chk)
                isOK = 0;
            break;

        default:
            isOK = 0;
            break;
        }
    }
    destroy();
    return isOK;
}

#include <time.h>

int main()
{
    clock_t start = clock();
    setbuf(stdout, NULL);
    freopen("sample_input.txt", "r", stdin);

    int T, MARK;
    scanf("%d %d", &T, &MARK);

    for (int tc = 1; tc <= T; tc++)
    {
        if (run()) printf("#%d %d\n", tc, MARK);
        else printf("#%d %d\n", tc, 0);
    }

    printf("\nPerformance: %d ms\n", (clock() - start));
    return 0;
}
```
