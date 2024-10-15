```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

#define CMD_INIT 1
#define CMD_INFLUENCER 2
#define CMD_ADD_PURCHASING_POWER 3
#define CMD_ADD_FRIENDSHIP 4

extern void init(int N, int mPurchasingPower[20000], int M, int mFriend1[20000], int mFriend2[20000]);
extern int influencer(int mRank);
extern int addPurchasingPower(int mID, int mPower);
extern int addFriendship(int mID1, int mID2);

/////////////////////////////////////////////////////////////////////////
/////////////////////////////////////////////////////////////////////////

static int mPurchasingPower[20000];
static int mFriend1[20000];
static int mFriend2[20000];

static bool run()
{
    int numQuery;
    int N, M, mRank, mID, mPower, mID1, mID2;
    int userAns, ans;

    bool isCorrect = false;

    scanf("%d", &numQuery);

    for (int q = 0; q < numQuery; q++)
    {
        int cmd;
        scanf("%d", &cmd);

        switch (cmd)
        {
        case CMD_INIT:
            scanf("%d", &N);
            for (int i = 0; i < N; i++)
                scanf("%d", &mPurchasingPower[i]);
            scanf("%d", &M);
            for (int i = 0; i < M; i++)
                scanf("%d", &mFriend1[i]);
            for (int i = 0; i < M; i++)
                scanf("%d", &mFriend2[i]);
            init(N, mPurchasingPower, M, mFriend1, mFriend2);
            isCorrect = true;
            break;
        case CMD_INFLUENCER:
            scanf("%d", &mRank);
            userAns = influencer(mRank);
            scanf("%d", &ans);
            if (userAns != ans)
            {
                isCorrect = false;
            }
            break;
        case CMD_ADD_PURCHASING_POWER:
            scanf("%d %d", &mID, &mPower);
            userAns = addPurchasingPower(mID, mPower);
            scanf("%d", &ans);
            if (userAns != ans)
            {
                isCorrect = false;
            }
            break;
        case CMD_ADD_FRIENDSHIP:
            scanf("%d %d", &mID1, &mID2);
            userAns = addFriendship(mID1, mID2);
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
    printf("Performance: %d ms\n", (clock() - start));
    return 0;
}
```
