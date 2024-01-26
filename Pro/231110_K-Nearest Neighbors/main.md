```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



#define CMD_INIT      (100)

#define CMD_ADD_SAMPLE    (200)

#define CMD_DELETE_SAMPLE  (300)

#define CMD_PREDICT      (400)



extern void init(int K, int L);

extern void addSample(int mID, int mX, int mY, int mC);

extern void deleteSample(int mID);

extern int predict(int mX, int mY);



static bool run()

{

    int Q;

    int K, L;

    int mID, mX, mY, mC;



    int ret = -1, ans;



    scanf("%d", &Q);



    bool okay = false;



    for (int q = 0; q < Q; ++q)

    {

        int cmd;

        scanf("%d", &cmd);

        switch (cmd)

        {

        case CMD_INIT:

            scanf("%d %d", &K, &L);

            init(K, L);

            okay = true;

            break;

        case CMD_ADD_SAMPLE:

            scanf("%d %d %d %d", &mID, &mX, &mY, &mC);

            addSample(mID, mX, mY, mC);

            break;

        case CMD_DELETE_SAMPLE:

            scanf("%d", &mID);

            deleteSample(mID);

            break;

        case CMD_PREDICT:

            scanf("%d %d", &mX, &mY);

            ret = predict(mX, mY);

            scanf("%d", &ans);

            if (ret != ans)

                okay = false;

            break;

        default:

            okay = false;

            break;

        }

    }



    return okay;

}

#include <time.h>

int main()

{
    clock_t start = clock();
    setbuf(stdout, NULL);

    freopen("sample_input.txt", "r", stdin);



    int TC, MARK;



    scanf("%d %d", &TC, &MARK);

    for (int tc = 1; tc <= TC; ++tc)

    {

        int score = run() ? MARK : 0;

        printf("#%d %d ", tc, score);
        printf("(%d ms)\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));
    }

    printf("\nResult: %d ms\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));

    return 0;

}
```
