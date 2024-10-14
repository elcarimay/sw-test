```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

extern void init(int N);
extern int addBar(int mID, int mLength, int mRow, int mCol, int mDir);
extern int removeBar(int mID);

#define CMD_INIT 100
#define CMD_ADD 200
#define CMD_REMOVE 300

static bool run()
{
    int query_num;
    scanf("%d", &query_num);

    int ret, ans;
    bool ok = false;

    for (int q = 0; q < query_num; q++)
    {
        int query;
        scanf("%d", &query);

        if (query == CMD_INIT)
        {
            int N;
            scanf("%d", &N);
            init(N);
            ok = true;
        }
        else if (query == CMD_ADD)
        {
            int mID;
            int mRow, mCol;
            int mLength, mDir;
            scanf("%d %d %d %d %d", &mID, &mLength, &mRow, &mCol, &mDir);
            ret = addBar(mID, mLength, mRow, mCol, mDir);
            scanf("%d", &ans);
            if (ans != ret)
            {
                ok = false;
            }
        }
        else if (query == CMD_REMOVE)
        {
            int mID;
            scanf("%d", &mID);
            ret = removeBar(mID);
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
    printf("Performance: %d ms\n", (clock() - start));
    return 0;
}
```
