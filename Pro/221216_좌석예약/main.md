```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

struct Result {
    int id;
    int num;
};

extern void init(int N);
extern Result reserveSeats(int mID, int mNum);
extern Result cancelReservation(int mID);

/////////////////////////////////////////////////////////////////////////

#define CMD_INIT 0
#define CMD_RESERVE 1
#define CMD_CANCEL 2

static bool run()
{
    int q;
    scanf("%d", &q);

    int cmd, param1, param2;
    int ans1, ans2;
    bool okay = false;
    Result res;

    for (int i = 0; i < q; ++i) {
        scanf("%d", &cmd);
        switch (cmd) {
        case CMD_INIT:
            scanf("%d", &param1);
            init(param1);
            okay = true;
            break;

        case CMD_RESERVE:
            scanf("%d %d", &param1, &param2);
            res = reserveSeats(param1, param2);
            scanf("%d %d", &ans1, &ans2);
            if (res.id != ans1 || res.num != ans2)
                okay = false;
            break;

        case CMD_CANCEL:
            scanf("%d", &param1);
            res = cancelReservation(param1);
            scanf("%d %d", &ans1, &ans2);
            if (res.id != ans1 || res.num != ans2)
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

    int T, MARK;
    scanf("%d %d", &T, &MARK);

    for (int tc = 1; tc <= T; tc++) {
        int score = run() ? MARK : 0;
        printf("#%d %d\n", tc, score);
    }
    printf("Performance: %d ms\n", (clock() - start));
    return 0;
}
```
