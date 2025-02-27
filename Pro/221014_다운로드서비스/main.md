```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

struct Result {
    int finish;
    int param;
};

extern void init(int mCapa);
extern void addHub(int mTime, int mParentID, int mID);
extern int removeHub(int mTime, int mID);
extern void requestDL(int mTime, int mParentID, int mpcID, int mSize);
extern Result checkPC(int mTime, int mpcID);

/////////////////////////////////////////////////////////////////////////

#define CMD_INIT 0
#define CMD_ADD  1
#define CMD_REMOVE 2
#define CMD_REQUEST 3
#define CMD_CHECK 4

static bool run() {
    int q;
    scanf("%d", &q);

    Result res;
    int cmd, time, id1, id2, param1, param2;
    int ans, ret;
    bool okay = false;

    for (int i = 0; i < q; ++i) {
        scanf("%d", &cmd);
        switch (cmd) {
        case CMD_INIT:
            scanf("%d", &param1);
            init(param1);
            okay = true;
            break;

        case CMD_ADD:
            scanf("%d %d %d", &time, &id1, &id2);
            addHub(time, id1, id2);
            break;

        case CMD_REMOVE:
            scanf("%d %d %d", &time, &id1, &ans);
            ret = removeHub(time, id1);
            if (ans != ret)
                okay = false;
            break;

        case CMD_REQUEST:
            scanf("%d %d %d %d", &time, &id1, &id2, &param1);
            requestDL(time, id1, id2, param1);
            break;

        case CMD_CHECK:
            scanf("%d %d %d %d", &time, &id1, &param1, &param2);
            res = checkPC(time, id1);
            if (res.finish != param1 || res.param != param2)
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
