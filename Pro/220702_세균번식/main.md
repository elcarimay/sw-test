```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



#define MAX_N 100



extern void init(int N, int mDish[MAX_N][MAX_N]);

extern int dropMedicine(int mTarget, int mRow, int mCol, int mEnergy);

extern int cleanBacteria(int mRow, int mCol);



#define CMD_INIT 100

#define CMD_DROP 200

#define CMD_CLEAN 300



static bool run()

{

    int query_num;

    scanf("%d", &query_num);



    int ret, ans;

    bool ok = false;

    static int dish[MAX_N][MAX_N];



    for (int q = 0; q < query_num; q++)

    {

        int query;

        scanf("%d", &query);



        if (query == CMD_INIT)

        {

            int N;

            scanf("%d", &N);

            for (int i = 0; i < N; i++)

            {

                for (int j = 0; j < N; j++)

                {

                    scanf("%d", &dish[i][j]);

                }

            }

            init(N, dish);

            ok = true;

        }

        else if (query == CMD_DROP)

        {

            int mTarget;

            int mRow, mCol;

            int mEnergy;

            scanf("%d %d %d %d", &mTarget, &mRow, &mCol, &mEnergy);

            ret = dropMedicine(mTarget, mRow, mCol, mEnergy);

            scanf("%d", &ans);

            if (ans != ret)

            {

                ok = false;

            }

        }

        else if (query == CMD_CLEAN)

        {

            int mRow, mCol;

            scanf("%d %d", &mRow, &mCol);

            ret = cleanBacteria(mRow, mCol);

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
