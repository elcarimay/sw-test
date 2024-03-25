```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

#define CMD_INIT 1
#define CMD_ADD 2
#define CMD_MIN_TRAVEL_TIME 3
#define CMD_MIN_PRICE 4

extern void init(int N);
extern void add(int mStartAirport, int mEndAirport, int mStartTime, int mTravelTime, int mPrice);
extern int minTravelTime(int mStartAirport, int mEndAirport, int mStartTime);
extern int minPrice(int mStartAirport, int mEndAirport);

/////////////////////////////////////////////////////////////////////////
/////////////////////////////////////////////////////////////////////////
int tc;
static bool run()
{
    int numQuery;
    int N, mStartAirport, mEndAirport, mStartTime, mTravelTime, mPrice;
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
            init(N);
            isCorrect = true;
            break;
        case CMD_ADD:
            scanf("%d %d %d %d %d", &mStartAirport, &mEndAirport, &mStartTime, &mTravelTime, &mPrice);
            add(mStartAirport, mEndAirport, mStartTime, mTravelTime, mPrice);
            break;
        case CMD_MIN_TRAVEL_TIME:
            scanf("%d %d %d", &mStartAirport, &mEndAirport, &mStartTime);
            userAns = minTravelTime(mStartAirport, mEndAirport, mStartTime);
            scanf("%d", &ans);
            if (userAns != ans)
            {
                isCorrect = false;
            }
            break;
        case CMD_MIN_PRICE:
            scanf("%d %d", &mStartAirport, &mEndAirport);
            userAns = minPrice(mStartAirport, mEndAirport);
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

    for (tc = 1; tc <= T; tc++)
    {
        int score = run() ? MARK : 0;
        printf("#%d %d\n", tc, score);
    }

    printf("RESULT : %dms\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));
    return 0;
}
```
