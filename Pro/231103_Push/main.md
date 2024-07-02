```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



#define MAX_N 30



void init(int N, int mMap[MAX_N][MAX_N]);

int push(int mRockR, int mRockC, int mDir, int mGoalR, int mGoalC);



/////////////////////////////////////////////////////////////////////////



#define INIT    100

#define PUSH    200


#include <time.h>

static bool run()

{
    

    int cmd, ans, ret;

    int N, r, c, dir, r2, c2;

    int Q = 0;

    bool okay = false;

    int region[MAX_N][MAX_N];



    scanf("%d", &Q);



    for (int q = 0; q < Q; ++q)

    {

        scanf("%d", &cmd);



        switch (cmd)

        {

        case INIT:

            scanf("%d", &N);



            for (int i = 0; i < N; i++)

                for (int j = 0; j < N; j++)

                    scanf("%d", &region[i][j]);



            init(N, region);

            okay = true;

            break;



        case PUSH:

            scanf("%d %d %d %d %d", &r, &c, &dir, &r2, &c2);

            ret = push(r, c, dir, r2, c2);

            scanf("%d", &ans);

            if (ret != ans)

                okay = false;

            break;



        default:

            okay = false;

        }

    }



    return okay;

}



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

        printf("#%d %d (%d ms)\n", tc, score, (clock() - start) / (CLOCKS_PER_SEC / 1000));

    }
    int result = (clock() - start) / (CLOCKS_PER_SEC / 1000);
    printf("\n>> Result: %d ms\n", result);


    return 0;

}
```
