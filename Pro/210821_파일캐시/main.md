```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

extern void init(int N);
extern int access(int fileId, int fileSize);
extern int usage();

/////////////////////////////////////////////////////////////////////////

#define CMD_INIT 1
#define CMD_ACCESS 2
#define CMD_USAGE 3

static bool run() {
    int q;
    scanf("%d", &q);

    int n, fileId, fileSize;
    int cmd, ans, ret = 0;
    bool okay = false;

    for (int i = 0; i < q; ++i) {
        scanf("%d", &cmd);
        switch (cmd) {
        case CMD_INIT:
            scanf("%d", &n);
            init(n);
            okay = true;
            break;
        case CMD_ACCESS:
            scanf("%d %d %d", &fileId, &fileSize, &ans);
            ret = access(fileId, fileSize);
            if (ans != ret)
                okay = false;
            break;
        case CMD_USAGE:
            ret = usage();
            scanf("%d", &ans);
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
    printf("Performance: %d ms\n", clock() - start);
    return 0;
}
```
