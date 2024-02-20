```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



extern void init(int N, int M);

extern int writeWord(int mId, int mLen);

extern int eraseWord(int mId);



#define CMD_INIT 1

#define CMD_WRITE 2

#define CMD_ERASE 3



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

            int N, M;

            scanf("%d %d", &N, &M);

            init(N, M);

            ok = true;

        }

        else if (query == CMD_WRITE)

        {

            int mId, mLen;

            scanf("%d %d", &mId, &mLen);

            int ret = writeWord(mId, mLen);

            scanf("%d", &ans);

            if (ans != ret)

            {

                ok = false;

            }

        }

        else if (query == CMD_ERASE)

        {

            int mId;

            scanf("%d", &mId);

            int ret = eraseWord(mId);

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

    printf("Performance = %d ms\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));

    return 0;

}
```
