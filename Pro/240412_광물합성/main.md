```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif
 
#include <stdio.h>
 
struct Result {
    int mCost;
    int mContent;
};
 
extern void init(int mShipFee);
extern int gather(int mMineId, int mType, int mCost, int mContent);
extern Result mix(int mBudget);
 
/////////////////////////////////////////////////////////////////////////
 
#define INIT 0
#define GATHER 1
#define MIX    2
 
static bool run() {
    int cmd, ans, ans2, ret;
    Result sRet;
    int in, in2, in3, in4;
    int Q = 0;
    bool okay = false;
 
    scanf("%d", &Q);
    for (int q = 0; q < Q; ++q) {
        scanf("%d", &cmd);
        switch (cmd) {
        case INIT:
            scanf("%d", &in);
            init(in);
            okay = true;
            break;
        case GATHER:
            scanf("%d %d %d %d", &in, &in2, &in3, &in4);
            ret = gather(in, in2, in3, in4);
            scanf("%d", &ans);
            if (ret != ans)
                okay = false;
            break;
        case MIX:
            scanf("%d", &in);
            sRet = mix(in);
            scanf("%d %d", &ans, &ans2);
            if (sRet.mCost != ans || sRet.mContent != ans2)
                okay = false;
            break;
        default:
            okay = false;
        }
    }
 
    return okay;
}
 
int main() {
    int T, MARK;
 
    setbuf(stdout, NULL);
        freopen("input.txt", "r", stdin);
 
    scanf("%d %d", &T, &MARK);
    for (int tc = 1; tc <= T; tc++) {
        int score = run() ? MARK : 0;
        printf("#%d %d\n", tc, score);
    }
 
    return 0;
}
```
