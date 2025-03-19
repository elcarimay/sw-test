```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>





extern void init(int L, int N);

extern int draw(int mID, int mRow, int mCol, int mHeight, int mWidth);

extern int getRectCount(int mID);

extern int countGroup();



#define CMD_INIT 1

#define CMD_DRAW 2

#define CMD_RECT 3

#define CMD_CNT 4



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

        else if (query == CMD_DRAW)

        {

            int mID, mRow, mCol, mHeight, mWidth;

            scanf("%d %d %d %d %d", &mID, &mRow, &mCol, &mHeight, &mWidth);

            int ret = draw(mID, mRow, mCol, mHeight, mWidth);

            scanf("%d", &ans);

            if (ans != ret)

            {

                ok = false;

            }

        }

        else if (query == CMD_RECT)

        {

            int mID;

            scanf("%d", &mID);

            int ret = getRectCount(mID);

            scanf("%d", &ans);

            if (ans != ret)

            {

                ok = false;

            }

        }

        else if (query == CMD_CNT)

        {

            int ret = countGroup();

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
