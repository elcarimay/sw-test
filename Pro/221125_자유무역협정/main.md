```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

#define MAXN   (500)
#define MAXL   (11)

#define CMD_INIT   (100)
#define CMD_START_PROJECT (200)
#define CMD_FINISH_PROJECT (300)
#define CMD_ALLY   (400)
#define CMD_CONFLICT  (500)

extern void init(int mNumA, char mCompanyListA[MAXN][MAXL], int mNumB, char mCompanyListB[MAXN][MAXL]);
extern void startProject(char mCompanyA[MAXL], char mCompanyB[MAXL]);
extern void finishProject(char mCompanyA[MAXL], char mCompanyB[MAXL]);
extern void ally(char mCompany1[MAXL], char mCompany2[MAXL]);
extern int conflict(char mCompany1[MAXL], char mCompany2[MAXL]);

int tc;

static bool run()
{
    int Q;
    int mNumA, mNumB;

    static char mCompanyListA[MAXN][MAXL];
    static char mCompanyListB[MAXN][MAXL];

    char mCompanyA[MAXL], mCompanyB[MAXL];
    char mCompany1[MAXL], mCompany2[MAXL];

    int ret = -1, ans;

    scanf("%d", &Q);

    bool okay = false;

    for (int q = 0; q < Q; ++q)
    {
        if (tc == 2)
            int kk = 0;

        int cmd;
        scanf("%d", &cmd);
        switch (cmd)
        {
        case CMD_INIT:
            scanf("%d", &mNumA);
            for (int i = 0; i < mNumA; ++i)
                scanf("%s", mCompanyListA[i]);
            scanf("%d", &mNumB);
            for (int i = 0; i < mNumB; ++i)
                scanf("%s", mCompanyListB[i]);
            init(mNumA, mCompanyListA, mNumB, mCompanyListB);
            okay = true;
            break;
        case CMD_START_PROJECT:
            scanf("%s %s", mCompanyA, mCompanyB);
            startProject(mCompanyA, mCompanyB);
            break;
        case CMD_FINISH_PROJECT:
            scanf("%s %s", mCompanyA, mCompanyB);
            finishProject(mCompanyA, mCompanyB);
            break;
        case CMD_ALLY:
            scanf("%s %s", mCompany1, mCompany2);
            ally(mCompany1, mCompany2);
            break;
        case CMD_CONFLICT:
            scanf("%s %s", mCompany1, mCompany2);
            ret = conflict(mCompany1, mCompany2);
            scanf("%d", &ans);
            if (ret != ans)
                okay = false;
            break;
        default:
            okay = false;
            break;
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

    int TC, MARK;

    scanf("%d %d", &TC, &MARK);

    for (tc = 1; tc <= TC; ++tc)
    {
        int score = run() ? MARK : 0;
        printf("#%d %d\n", tc, score);
    }
    printf("RESULT : %dms\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));

    return 0;
}


```
