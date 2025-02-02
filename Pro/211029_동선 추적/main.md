```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

extern void init();
extern void addPlace(int pID, int r, int c);
extern void removePlace(int pID);
extern void contactTracing(int uID, int visitNum, int moveInfo[], int visitList[]);
extern void disinfectPlaces(int uID);

#define INIT    100
#define ADD_PLACE   200
#define REMOVE_PLACE  300
#define CONTACT_TRACING  400
#define DISINFECT_PLACES 500

static int run()
{
    int queryCnt;
    scanf("%d", &queryCnt);

    int ret = 0;
    int cmd;
    int pID, uID, r, c, visitNum;
    int moveInfo[100];
    int visitList[100];
    int ans;

    for (int q = 1; q <= queryCnt; q++)
    {
        if (q == 4642) {
            q = q;
        }
        scanf("%d", &cmd);

        switch (cmd) {
        case INIT:
            init();
            ret = 1;
            break;
        case ADD_PLACE:
            scanf("%d%d%d", &pID, &r, &c);
            addPlace(pID, r, c);
            break;
        case REMOVE_PLACE:
            scanf("%d", &pID);
            removePlace(pID);
            break;
        case CONTACT_TRACING:
            scanf("%d%d", &uID, &visitNum);
            for (int i = 0; i < visitNum; i++)
                scanf("%d", &moveInfo[i]);
            contactTracing(uID, visitNum, moveInfo, visitList);
            for (int i = 0; i < visitNum; i++) {
                scanf("%d", &ans);
                if (visitList[i] != ans)
                    ret = 0;
            }
            break;
        case DISINFECT_PLACES:
            scanf("%d", &uID);
            disinfectPlaces(uID);
            break;
        default:
            ret = 0;
            break;
        }
    }

    return ret;
}
#include <time.h>
int main()
{
    clock_t start = clock();
    setbuf(stdout, NULL);
    freopen("sample_input.txt", "r", stdin);

    int tc, MARK;
    scanf("%d%d", &tc, &MARK);

    for (int t = 1; t <= tc; t++)
    {
        int score = run() ? MARK : 0;
        printf("#%d %d\n", t, score);
    }
    printf("Performance: %d ms\n", clock() - start);
    return 0;
}
```
