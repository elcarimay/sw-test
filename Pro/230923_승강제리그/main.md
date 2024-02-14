```cpp
#if 1
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



void init(int N, int L, int mAbility[]);

int move();

int trade();



#define MAX_N 39990



#define CMD_INIT 100

#define CMD_MOVE 200

#define CMD_TRADE 300



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

            int N;

            int L;

            int mAbility[MAX_N];

            scanf("%d %d", &N, &L);

            for (int i = 0; i < N; i++) {

                scanf("%d", &mAbility[i]);

            }

            init(N, L, mAbility);

            ok = true;

        }

        else if (query == CMD_MOVE)

        {

            int ret = move();

            scanf("%d", &ans);

            if (ans != ret)

            {

                ok = false;

            }

        }

        else if (query == CMD_TRADE)

        {

            int ret = trade();

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
#endif
```
