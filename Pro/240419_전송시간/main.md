```cpp
#if 1
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

extern void init(int N, int K, int mNodeA[], int mNodeB[], int mTime[]);
extern void addLine(int mNodeA, int mNodeB, int mTime);
extern void removeLine(int mNodeA, int mNodeB);
extern int checkTime(int mNodeA, int mNodeB);

#define CMD_INIT   0
#define CMD_ADD    1
#define CMD_REMOVE 2
#define CMD_CHECK  3

#define MAX_LINE 30000

static int nodeA[MAX_LINE];
static int nodeB[MAX_LINE];
static int Time[MAX_LINE];

static bool run()
{
    int Q, N, K, ans;
    scanf("%d", &Q);

    bool ok = false;

    for (int q = 0; q < Q; q++)
    {
        int cmd;
        scanf("%d", &cmd);
        if (cmd == CMD_INIT)
        {
            scanf("%d %d", &N, &K);
            for (int i = 0; i < K; i++) {
                scanf("%d %d %d", &nodeA[i], &nodeB[i], &Time[i]);
            }
            init(N, K, nodeA, nodeB, Time);
            ok = true;
        }
        else if (cmd == CMD_ADD)
        {
            scanf("%d %d %d", &nodeA[0], &nodeB[0], &Time[0]);
            addLine(nodeA[0], nodeB[0], Time[0]);
        }
        else if (cmd == CMD_REMOVE)
        {
            scanf("%d %d", &nodeA[0], &nodeB[0]);
            removeLine(nodeA[0], nodeB[0]);
        }
        else if (cmd == CMD_CHECK)
        {
            scanf("%d %d", &nodeA[0], &nodeB[0]);
            int ret = checkTime(nodeA[0], nodeB[0]);
            scanf("%d", &ans);
            if (ans != ret) {
                ok = false;
            }
        }
        else ok = false;
    }
    return ok;
}
#include <time.h>
int main()
{
    clock_t start = clock();
    setbuf(stdout, NULL);
    freopen("input-TS전송시간.txt", "r", stdin);

    int T, MARK;
    scanf("%d %d", &T, &MARK);

    for (int tc = 1; tc <= T; tc++)
    {
        int score = run() ? MARK : 0;
        printf("#%d %d (%d ms)\n", tc, score, (clock() - start) / (CLOCKS_PER_SEC / 1000));
    }
    printf("\nPerformance : %d ms\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));
    return 0;
}
#endif
```
