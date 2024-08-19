```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

#define CMD_INIT 1
#define CMD_MIN_DAMAGE 2

extern void init(int R, int C, int M, int mStructureR[30000], int mStructureC[30000]);
extern int minDamage(int mStartR, int mStartC, int mEndR, int mEndC);

/////////////////////////////////////////////////////////////////////////
/////////////////////////////////////////////////////////////////////////

static int mStructureR[30000];
static int mStructureC[30000];

static bool run()
{
    int numQuery;
    int R, C, M, mStartR, mStartC, mEndR, mEndC;
    int userAns, ans;

    bool isCorrect = false;

    scanf("%d", &numQuery);

    for (int i = 0; i < numQuery; ++i)
    {
        int cmd;
        scanf("%d", &cmd);

        switch (cmd)
        {
        case CMD_INIT:
            scanf("%d %d %d", &R, &C, &M);
            for (int j = 0; j < M; j++)
                scanf("%d", &mStructureR[j]);
            for (int j = 0; j < M; j++)
                scanf("%d", &mStructureC[j]);
            init(R, C, M, mStructureR, mStructureC);
            isCorrect = true;
            break;
        case CMD_MIN_DAMAGE:
            scanf("%d %d %d %d", &mStartR, &mStartC, &mEndR, &mEndC);
            userAns = minDamage(mStartR, mStartC, mEndR, mEndC);
            scanf("%d", &ans);
            if (userAns != ans)
            {
                isCorrect = false;
            }
            break;
        default:
            isCorrect = false;
            break;
        }
    }
    return isCorrect;
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
    printf("\nPerformance: %d ms\n", (clock() - start));
    return 0;
}
```
