```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

extern void init(int N);
extern int newCivilization(int r, int c, int mID);
extern int removeCivilization(int mID);
extern int getCivilization(int r, int c);
extern int getCivilizationArea(int mID);
extern int mergeCivilization(int mID1, int mID2);

/////////////////////////////////////////////////////////////////////////
/////////////////////////////////////////////////////////////////////////

#define INIT     100
#define NEW_CIVILIZATION  200
#define REMOVE_CIVILIZATION  300
#define GET_CIVILIZATION  400
#define GET_CIVILIZATION_AREA 500
#define MERGE_CIVILIZATION  600

static bool run()
{
    int Q, N, r, c, cmd;
    int mID, mID1, mID2;

    int ans, ret = 0;

    bool okay = false;

    scanf("%d", &Q);

    for (int i = 0; i <= Q; ++i)
    {
        scanf("%d", &cmd);
        switch (cmd)
        {
        case INIT:
            scanf("%d", &N);
            init(N);
            okay = true;
            break;
        case NEW_CIVILIZATION:
            scanf("%d %d %d", &r, &c, &mID);
            if (okay)
                ret = newCivilization(r, c, mID);
            scanf("%d", &ans);
            if (ans != ret)
                okay = false;
            break;
        case REMOVE_CIVILIZATION:
            scanf("%d", &mID);
            if (okay)
                ret = removeCivilization(mID);
            scanf("%d", &ans);
            if (ans != ret)
                okay = false;
            break;
        case GET_CIVILIZATION:
            scanf("%d %d", &r, &c);
            if (okay)
                ret = getCivilization(r, c);
            scanf("%d", &ans);
            if (ans != ret)
                okay = false;
            break;
        case GET_CIVILIZATION_AREA:
            scanf("%d", &mID);
            if (okay)
                ret = getCivilizationArea(mID);
            scanf("%d", &ans);
            if (ans != ret)
                okay = false;
            break;
        case MERGE_CIVILIZATION:
            scanf("%d %d", &mID1, &mID2);
            if (okay)
                ret = mergeCivilization(mID1, mID2);
            scanf("%d", &ans);
            if (ans != ret)
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
    freopen("sample_input.txt", "r", stdin);

    int T, MARK;
    scanf("%d %d", &T, &MARK);

    for (int tc = 1; tc <= T; tc++)
    {
        int score = run() ? MARK : 0;
        printf("#%d %d\n", tc, score);
    }
    printf("Performance: %d ms\n", clock() - start);
    return 0;
}

```
