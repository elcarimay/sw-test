```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



extern void init(int);

extern void addObject(int, int, int, int, int);

extern int group(int, int, int, int, int);

extern int ungroup(int, int);

extern int moveObject(int, int, int, int);



#define CMD_INIT (100)

#define CMD_ADD  (200)

#define CMD_GROUP  (300)

#define CMD_UNGROUP  (400)

#define CMD_MOVE (500)



static int N;



static bool run()

{

    int query_num;

    scanf("%d", &query_num);



    int ret, ans;

    bool ok = false;



    for (int q = 0; q < query_num; q++)

    {

        int query, mID, y1, x1, y2, x2;

        scanf("%d", &query);



        if (query == CMD_INIT)

        {

            scanf("%d", &N);

            init(N);

            ok = true;

        }

        else if (query == CMD_ADD)

        {

            scanf("%d%d%d%d%d", &mID, &y1, &x1, &y2, &x2);

            addObject(mID, y1, x1, y2, x2);

        }

        else if (query == CMD_GROUP)

        {

            scanf("%d%d%d%d%d", &mID, &y1, &x1, &y2, &x2);

            ret = group(mID, y1, x1, y2, x2);

            scanf("%d", &ans);



            if (ret != ans)

                ok = false;

        }

        else if (query == CMD_UNGROUP)

        {

            scanf("%d%d", &y1, &x1);

            ret = ungroup(y1, x1);

            scanf("%d", &ans);



            if (ret != ans)

                ok = false;



        }

        else if (query == CMD_MOVE)

        {

            scanf("%d%d%d%d", &y1, &x1, &y2, &x2);

            ret = moveObject(y1, x1, y2, x2);

            scanf("%d", &ans);



            if (ret != ans)

                ok = false;

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
