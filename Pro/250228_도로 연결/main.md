```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



extern void init(int L, int N);

extern int build(int mDir, int mRow, int mCol, int mLength);

extern int checkRoute(int mSRow, int mSCol, int mERow, int mECol);



#define CMD_INIT 1

#define CMD_BUILD 2

#define CMD_CHECK 3



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

            int L, N;

            scanf("%d %d", &L, &N);

            init(L, N);

            ok = true;

        }

        else if (query == CMD_BUILD)

        {

            int mDir, mRow, mCol, mLength;

            scanf("%d %d %d %d", &mDir, &mRow, &mCol, &mLength);

            int ret = build(mDir, mRow, mCol, mLength);

            scanf("%d", &ans);

            if (ans != ret)

            {

                ok = false;

            }

        }

        else if (query == CMD_CHECK)

        {

            int mSRow, mSCol, mERow, mECol;

            scanf("%d %d %d %d", &mSRow, &mSCol, &mERow, &mECol);

            int ret = checkRoute(mSRow, mSCol, mERow, mECol);

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

    for (int tc = 1; tc <= T; tc++)

    {

        int score = run() ? MARK : 0;

        printf("#%d %d\n", tc, score);

    }
    printf("Performance: %d ms\n", clock() - start);
    return 0;

}


```
