```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif
 
#include <stdio.h>
 
extern void init();
extern int add(int mId, int mStart, int mEnd);
extern int remove(int mId);
extern int announce(int mDuration, int M);
 
/////////////////////////////////////////////////////////////////////////
 
#define CMD_INIT 1
#define CMD_ADD 2
#define CMD_REMOVE 3
#define CMD_ANNOUNCE 4
 
static bool run() {
    int q;
    scanf("%d", &q);
 
    int mid, mstart, mend, mduration, m;
    int cmd, ans, ret = 0;
    bool okay = false;
 
    for (int i = 0; i < q; ++i) {
        scanf("%d", &cmd);
        switch (cmd) {
        case CMD_INIT:
            init();
            okay = true;
            break;
        case CMD_ADD:
            scanf("%d %d %d %d", &mid, &mstart, &mend, &ans);
            ret = add(mid, mstart, mend);
            if (ans != ret)
                okay = false;
            break;
        case CMD_REMOVE:
            scanf("%d %d", &mid, &ans);
            ret = remove(mid);
            if (ans != ret)
                okay = false;
            break;
        case CMD_ANNOUNCE:
            scanf("%d %d %d", &mduration, &m, &ans);
            ret = announce(mduration, m);
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
 
int main() {
    setbuf(stdout, NULL);
    freopen("input.txt", "r", stdin);
 
    int T, MARK;
    scanf("%d %d", &T, &MARK);
 
    for (int tc = 1; tc <= T; tc++) {
        int score = run() ? MARK : 0;
        printf("#%d %d\n", tc, score);
    }
 
    return 0;
}
#endif
```
