```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

extern void init(int N, int mId[], int mDuration[], int mCapacity[]);
extern int add(int tStamp, int mId, int mNum, int mPriority);
extern void search(int tStamp, int mCount, int mId[], int mWait[]);

/////////////////////////////////////////////////////////////////////////

#define CMD_INIT 1
#define CMD_ADD 2
#define CMD_SEARCH 3

#define MAX_N 100

static int rideId[MAX_N];
static int rideDuration[MAX_N];
static int rideCapa[MAX_N];

static bool run() {
    int q;
    scanf("%d", &q);

    int n, num, pri, time, idx, cnt;
    int cmd, ans, ret = 0;
    int mId[10];
    int mWait[10];
    bool okay = false;

    for (int i = 0; i < q; ++i) {
        scanf("%d", &cmd);
        switch (cmd) {
        case CMD_INIT:
            scanf("%d", &n);
            for (int j = 0; j < n; ++j) {
                scanf("%d %d %d", &rideId[j], &rideDuration[j], &rideCapa[j]);
            }
            init(n, rideId, rideDuration, rideCapa);
            okay = true;
            break;
        case CMD_ADD:
            scanf("%d %d %d %d %d", &time, &idx, &num, &pri, &ans);
            if (okay) {
                ret = add(time, rideId[idx], num, pri);
            }
            if (ret != ans)
                okay = false;
            break;
        case CMD_SEARCH:
            scanf("%d %d", &time, &cnt);
            if (okay) {
                search(time, cnt, mId, mWait);
            }
            for (int j = 0; j < cnt; ++j) {
                scanf("%d %d", &idx, &num);
                if (num != mWait[j] || rideId[idx] != mId[j]) {
                    okay = false;
                }
            }
            break;
        default:
            okay = false;
            break;
        }
    }

    return okay;
}
#include <time.h>
int main() {
    clock_t start = clock();
    setbuf(stdout, NULL);
    freopen("sample_input-copy.txt", "r", stdin);

    int T, MARK;
    scanf("%d %d", &T, &MARK);

    for (int tc = 1; tc <= T; tc++) {
        int score = run() ? MARK : 0;
        printf("#%d %d\n", tc, score);
    }
    printf("Performance: %d ms\n", clock() - start);
    return 0;
}
```
