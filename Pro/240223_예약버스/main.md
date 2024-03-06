```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>
#include <iostream>
using namespace std;
//#include &lt;stdio.h&gt;



extern void init(int N, int K, int mRoadAs[], int mRoadBs[], int mLens[]);

extern void addRoad(int mRoadA, int mRoadB, int mLen);

extern int findPath(int mStart, int mEnd, int M, int mStops[]);



#define MAX_K 1000

#define MAX_M 5



#define CMD_INIT 1

#define CMD_ADD 2

#define CMD_FIND 3



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

            int N, K;

            int mRoadAs[MAX_K];

            int mRoadBs[MAX_K];

            int mLens[MAX_K];

            scanf("%d %d", &N, &K);

            for (int i = 0; i < K; i++) scanf("%d", &mRoadAs[i]);

            for (int i = 0; i < K; i++) scanf("%d", &mRoadBs[i]);

            for (int i = 0; i < K; i++) scanf("%d", &mLens[i]);

            init(N, K, mRoadAs, mRoadBs, mLens);

            ok = true;

        }

        else if (query == CMD_ADD)

        {

            int mRoadA, mRoadB, mLen;

            scanf("%d %d %d", &mRoadA, &mRoadB, &mLen);

            addRoad(mRoadA, mRoadB, mLen);

        }

        else if (query == CMD_FIND)

        {

            int mStart, mEnd, M;

            int mStops[MAX_M];

            scanf("%d %d %d", &mStart, &mEnd, &M);

            for (int i = 0; i< M; i++) scanf("%d", &mStops[i]);

            int ret = findPath(mStart, mEnd, M, mStops);

            scanf("%d", &ans);

            if (ans != ret)

            {

                ok = false;

            }

        }

    }

    return ok;

}

#include <time.h>

int main()

{
    clock_t start = clock();
    setbuf(stdout, NULL);

    freopen("sample_input.txt", "r", stdin);

    int T, MARK;

    scanf("%d %d", &T, &MARK);

    for (int tc = 1; tc < T; tc++)

    {

        int score = run() ? MARK : 0;

        printf("#%d %d\n", tc, score);

    }
    printf("Preformance = %d\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));
    return 0;

}
```
