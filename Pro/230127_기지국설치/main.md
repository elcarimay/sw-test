```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

extern void init(int N, int mId[], int mLocation[]);
extern int add(int mId, int mLocation);
extern int remove(int mStart, int mEnd);
extern int install(int M);

/////////////////////////////////////////////////////////////////////////

#define CMD_INIT 1
#define CMD_ADD 2
#define CMD_REMOVE 3
#define CMD_INSTALL 4

static bool run() {
    int q;
    scanf("%d", &q);

    int n, mid, mloc, mstart, mend, m;
    int midArr[100], mlocArr[100];
    int cmd, ans, ret = 0;
    bool okay = false;

    for (int i = 0; i < q; ++i) {

        if (i == 38)
            int kk = 0;

        scanf("%d", &cmd);
        switch (cmd) {
        case CMD_INIT:
            scanf("%d", &n);
            for (int j = 0; j < n; ++j) {
                scanf("%d %d", &midArr[j], &mlocArr[j]);
            }
            init(n, midArr, mlocArr);
            okay = true;
            break;
        case CMD_ADD:
            scanf("%d %d %d", &mid, &mloc, &ans);
            ret = add(mid, mloc);
            if (ans != ret)
                okay = false;
            break;
        case CMD_REMOVE:
            scanf("%d %d %d", &mstart, &mend, &ans);
            ret = remove(mstart, mend);
            if (ans != ret)
                okay = false;
            break;
        case CMD_INSTALL:
            scanf("%d %d", &m, &ans);
            ret = install(m);
            if (ans != ret)
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

int main() {
    clock_t start = clock();
    setbuf(stdout, NULL);
    freopen("sample_input.txt", "r", stdin);

    int T, MARK;
    scanf("%d %d", &T, &MARK);

    for (int tc = 1; tc <= T; tc++) {
        int score = run() ? MARK : 0;
        printf("#%d %d\n", tc, score);
    }
    printf("RESULT : %dms\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));
    return 0;
}
```
