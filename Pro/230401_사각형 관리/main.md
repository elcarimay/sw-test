```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



extern void init(int);

extern void addRect(int, int, int, int, int);

extern void selectAndMove(int, int, int, int);

extern int moveFront(int);

extern int selectAndErase(int, int);

extern int check(int, int);



#define CMD_INIT (100)

#define CMD_ADD  (200)

#define CMD_SELECT_AND_MOVE  (300)

#define CMD_MOVE_FRONT  (400)

#define CMD_SELECT_AND_ERASE (500)

#define CMD_CHECK (600)



static int N;



static bool run()

{

    int query_num;

    scanf("%d", &query_num);



    int ret, ans;

    bool ok = false;



    for (int q = 0; q < query_num; q++)

    {
        if (q == 153) {
            q = q;
        }
        int query, mID, y1, x1, y2, x2, h, w;

        scanf("%d", &query);



        if (query == CMD_INIT)

        {

            scanf("%d", &N);

            init(N);

            ok = true;

        }

        else if (query == CMD_ADD)

        {

            scanf("%d%d%d%d%d", &mID, &y1, &x1, &h, &w);



            addRect(mID, y1, x1, h, w);

        }

        else if (query == CMD_SELECT_AND_MOVE)

        {

            scanf("%d%d%d%d", &y1, &x1, &y2, &x2);

            selectAndMove(y1, x1, y2, x2);

        }

        else if (query == CMD_MOVE_FRONT)

        {

            scanf("%d", &mID);

            ret = moveFront(mID);

            scanf("%d", &ans);



            if (ret != ans)

                ok = false;



        }

        else if (query == CMD_SELECT_AND_ERASE)

        {

            scanf("%d%d", &y1, &x1);

            ret = selectAndErase(y1, x1);

            scanf("%d", &ans);



            if (ret != ans)

                ok = false;

        }

        else if (query == CMD_CHECK)

        {

            scanf("%d%d", &y1, &x1);

            ret = check(y1, x1);

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
