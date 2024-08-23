```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

#define CMD_INIT   100
#define CMD_ADD    200
#define CMD_ERASE   300
#define CMD_CHANGEPITCH  400
#define CMD_GETSIMILARITY 500

#define MAXL 7
#define MAXM 100

struct Result
{
    char mTitle[MAXL];
    int  mScore;
};

extern void   init();
extern void   add(char mTitle[MAXL], int mSize, int mScale[MAXM], int mTempo[MAXM]);
extern void   erase(char mTitle[MAXL]);
extern void   changePitch(char mTitle[MAXL], int mDelta);
extern Result getSimilarity(int mSize, int mScale[MAXM], int mTempo[MAXM]);

/////////////////////////////////////////////////////////////////////////
/////////////////////////////////////////////////////////////////////////

static int mstrcmp_m(const char a[], const char b[])
{
    int i;
    for (i = 0; a[i] != '\0'; ++i)
        if (a[i] != b[i])
            return a[i] - b[i];
    return a[i] - b[i];
}

static bool run()
{
    int  Q, cmd, mSize, mDelta;
    char mTitle[MAXL];
    int  mScale[MAXM], mTempo[MAXM];

    Result ans, res;

    scanf("%d", &Q);

    bool okay = false;

    for (int q = 0; q < Q; ++q)
    {
        scanf("%d", &cmd);
        switch (cmd)
        {
        case CMD_INIT:
            init();
            okay = true;
            break;
        case CMD_ADD:
            scanf("%s %d", mTitle, &mSize);
            for (int i = 0; i < mSize; ++i)
                scanf("%d", &mScale[i]);
            for (int i = 0; i < mSize; ++i)
                scanf("%d", &mTempo[i]);
            if (okay)
                add(mTitle, mSize, mScale, mTempo);
            break;
        case CMD_ERASE:
            scanf("%s", mTitle);
            if (okay)
                erase(mTitle);
            break;
        case CMD_CHANGEPITCH:
            scanf("%s %d", mTitle, &mDelta);
            if (okay)
                changePitch(mTitle, mDelta);
            break;
        case CMD_GETSIMILARITY:
            scanf("%d", &mSize);
            for (int i = 0; i < mSize; ++i)
                scanf("%d", &mScale[i]);
            for (int i = 0; i < mSize; ++i)
                scanf("%d", &mTempo[i]);
            if (okay)
                res = getSimilarity(mSize, mScale, mTempo);
            scanf("%s %d", ans.mTitle, &ans.mScore);
            if (mstrcmp_m(res.mTitle, ans.mTitle) != 0 || res.mScore != ans.mScore)
                okay = false;
            break;
        }
    }

    return okay;
}

#include<time.h>

int main()
{
    clock_t start = clock();
    setbuf(stdout, NULL);
    freopen("sample_input.txt", "r", stdin);

    int T, MARK;
    scanf("%d %d", &T, &MARK);

    for (int tc = 1; tc <= T; tc++)
    {
        int score = run() ? MARK : 0;

        printf("#%d %d\n", tc, score);
    }
    printf("\nPerformance: %d ms\n", (clock() - start));
    return 0;
}
#endif // 1 // ms
```
