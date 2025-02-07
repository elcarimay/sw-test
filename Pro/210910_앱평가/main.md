```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

static int mstrcmp(const char str1[], const char str2[])
{
    int c = 0;
    while (str1[c] != 0 && str1[c] == str2[c])
        ++c;
    return str1[c] - str2[c];
}

static void mstrcpy(char dest[], const char src[])
{
    int i = 0;
    while (src[i] != '\0') { dest[i] = src[i]; i++; }
    dest[i] = src[i];
}

static const int CMD_INIT = 100;
static const int CMD_ADDRATING = 200;
static const int CMD_DELETERATING = 300;
static const int CMD_BANUSER = 400;
static const int CMD_SORTBYSCORE = 500;
static const int CMD_SORTBYNUMBER = 600;

static const int MAXL = 16;

struct RESULT {
    char mApp[5][MAXL];
};

extern void init(int, const char[][MAXL]);
extern void addRating(int, const char[MAXL], int);
extern void deleteRating(int, const char[MAXL]);
extern void banUser(int);
extern RESULT sortByScore();
extern RESULT sortByNumber();

static char applist[10000][MAXL];
static int mSeed;
static char anslist[10000][MAXL];

static int pseudoRand()
{
    mSeed = (mSeed * 214013 + 2531011) & 0xFFFFFFFF;
    return (mSeed >> 16) & 0x7FFF;
}

static void makeApp(int num)
{
    for (int i = 0; i < num; i++) {
        int len = 5 + pseudoRand() % 11;
        for (int j = 0; j < len; j++) {
            applist[i][j] = 'A' + pseudoRand() % 26;
        }
        applist[i][len] = 0;
    }
}

static int run(void)
{
    int ret = 0;
    int query_cnt, cmd;
    int appNum;
    int mUser, mApp, mScore;
    RESULT user;
    int ans;

    scanf("%d %d %d", &query_cnt, &mSeed, &appNum);
    makeApp(appNum);
    for (int i = 0; i < appNum; i++)
        mstrcpy(anslist[i], applist[i]);

    for (int q = 0; q < query_cnt; q++)
    {
        scanf("%d", &cmd);
        switch (cmd) {
        case CMD_INIT:
            init(appNum, applist);
            ret = 1;
            break;
        case CMD_ADDRATING:
            scanf("%d %d %d", &mUser, &mApp, &mScore);
            addRating(mUser, applist[mApp], mScore);
            break;
        case CMD_DELETERATING:
            scanf("%d %d", &mUser, &mApp);
            deleteRating(mUser, applist[mApp]);
            break;
        case CMD_BANUSER:
            scanf("%d", &mUser);
            banUser(mUser);
            break;
        case CMD_SORTBYSCORE:
            user = sortByScore();
            for (int i = 0; i < 5; i++) {
                scanf("%d", &ans);
                if (mstrcmp(user.mApp[i], anslist[ans]))
                    ret = 0;
            }
            break;
        case CMD_SORTBYNUMBER:
            user = sortByNumber();
            for (int i = 0; i < 5; i++) {
                scanf("%d", &ans);
                if (mstrcmp(user.mApp[i], anslist[ans]))
                    ret = 0;
            }
            break;
        default:
            ret = 0;
            break;
        }
    }

    return ret;
}

#include <time.h>

int main()
{
    clock_t start = clock();
    setbuf(stdout, NULL);
    freopen("sample_input.txt", "r", stdin);
    int tc, MARK;

    scanf("%d %d", &tc, &MARK);

    for (int t = 1; t <= tc; t++)
    {
        int score = run() ? MARK : 0;
        printf("#%d %d\n", t, score);
    }
    printf("Performance: %d ms\n", clock() - start);
    return 0;
}
```
