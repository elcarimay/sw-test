```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

extern void init();
extern void loginID(char mID[10]);
extern int closeIDs(char mStr[10]);
extern void connectCnt(int mCnt);
extern int waitOrder(char mID[10]);

/////////////////////////////////////////////////////////////////////////

#define CMD_INIT 0
#define CMD_LOGIN 1
#define CMD_CLOSE 2
#define CMD_CONNECT 3
#define CMD_ORDER 4

static bool run() {
    int q;
    scanf("%d", &q);

    char str[10];
    int cmd, ans, ret;
    bool okay = false;

    for (int i = 0; i < q; ++i) {
        scanf("%d", &cmd);
        switch (cmd) {
        case CMD_INIT:
            init();
            okay = true;
            break;

        case CMD_LOGIN:
            scanf("%s", str);
            loginID(str);
            break;

        case CMD_CLOSE:
            scanf("%d %s", &ans, str);
            ret = closeIDs(str);
            if (ans != ret)
                okay = false;
            break;

        case CMD_CONNECT:
            scanf("%d", &ans);
            connectCnt(ans);
            break;

        case CMD_ORDER:
            scanf("%d %s", &ans, str);
            ret = waitOrder(str);
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
