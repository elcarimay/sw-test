```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>
#include <time.h>


extern void init(int N, int px[], int py[]);
extern void addRestaurant(int pID, int x, int y);
extern void removeRestaurant(int pID);
extern void order(int uID, int pID);
extern void beFriend(int uID1, int uID2);
extern int recommend(int uID);

#define INIT 100
#define ADD_RESTAURANT 200
#define REMOVE_RESTAURANT 300
#define ORDER 400
#define BE_FRIEND 500
#define RECOMMEND 600

static int px[30];
static int py[30];

static bool run()
{
    int query_num;
    scanf("%d", &query_num);

    int n = 0;
    bool ok = false;

    for (int q = 0; q < query_num; q++)
    {
        int query;
        scanf("%d", &query);

        if (query == INIT)
        {
            scanf("%d", &n);
            for (int i = 0; i < n; i++)
                scanf("%d", &px[i]);
            for (int i = 0; i < n; i++)
                scanf("%d", &py[i]);

            init(n, px, py);

            ok = true;
        }
        else if (query == ADD_RESTAURANT)
        {
            int pID, x, y;
            scanf("%d%d%d", &pID, &x, &y);

            addRestaurant(pID, x, y);
        }
        else if (query == REMOVE_RESTAURANT)
        {
            int pID;
            scanf("%d", &pID);

            removeRestaurant(pID);
        }
        else if (query == ORDER)
        {
            int uID, pID;
            scanf("%d%d", &uID, &pID);

            order(uID, pID);
        }
        else if (query == BE_FRIEND)
        {
            int uID1, uID2;
            scanf("%d%d", &uID1, &uID2);

            beFriend(uID1, uID2);
        }
        else if (query == RECOMMEND)
        {
            int uID, ans;
            scanf("%d %d", &uID, &ans);
            int ret = recommend(uID);
            if (ret != ans)
            {
                ok = false;
            }
        }
    }

    return ok;
}
int main()
{
    clock_t start = clock();
    setbuf(stdout, NULL);
    freopen("sample_input.txt", "r", stdin);

    int T, MARK;
    scanf("%d%d", &T, &MARK);

    for (int tc = 1; tc <= T; tc++)
    {
        int score = run() ? MARK : 0;
        printf("#%d %d\n", tc, score);
    }

    printf("Performance: %d ms\n", clock() - start);

    return 0;
}
```
