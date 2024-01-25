```cpp
#if 1
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



extern void init(int N, int K);

extern void registerUser(int mTime, int mUID, int mNum, int mGroupIDs[]);

extern int offerNews(int mTime, int mNewsID, int mDelay, int mGroupID);

extern void cancelNews(int mTime, int mNewsID);

extern int checkUser(int mTime, int mUID, int mRetIDs[]);



/////////////////////////////////////////////////////////////////////////



#define INIT 0

#define REGI 1

#define OFFER 2

#define CANCEL 3

#define CHECK 4



static int gids[30];

static int ansids[3];

static int retids[3];

static bool run()

{

    int N, K;

    int cmd, ans, ret;

    int time, num, uid, gid, nid, delay;



    int Q = 0;

    bool okay = false;



    scanf("%d", &Q);

    for (int q = 0; q < Q; ++q)

    {

        scanf("%d", &cmd);

        switch (cmd)

        {

        case INIT:

            scanf("%d %d", &N, &K);

            init(N, K);

            okay = true;

            break;



        case REGI:

            scanf("%d %d %d", &time, &uid, &num);

            for (int m = 0; m < num; m++) {

                scanf("%d", &gids[m]);

            }

            registerUser(time, uid, num, gids);

            break;



        case OFFER:

            scanf("%d %d %d %d %d", &time, &nid, &delay, &gid, &ans);

            ret = offerNews(time, nid, delay, gid);

            if (ans != ret) {

                okay = false;

            }

            break;



        case CANCEL:

            scanf("%d %d", &time, &nid);

            cancelNews(time, nid);

            break;



        case CHECK:

            scanf("%d %d %d", &time, &uid, &ans);

            ret = checkUser(time, uid, retids);



            num = ans;

            if (num > 3) num = 3;

            for (int m = 0; m < num; m++) {

                scanf("%d", &ansids[m]);

            }

            if (ans != ret) {

                okay = false;

            }

            else {

                for (int m = 0; m < num; m++) {

                    if (ansids[m] != retids[m]) {

                        okay = false;

                    }

                }

            }

            break;



        default:

            okay = false;

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



    int T, MARK;

    scanf("%d %d", &T, &MARK);



    for (int tc = 1; tc <= T; tc++)

    {

        int score = run() ? MARK : 0;

        printf("#%d %d\n", tc, score);

    }

    printf("Result : %d ms\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));

    return 0;

}
#endif
```
