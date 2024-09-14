```cpp
#if 1
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

extern void init();
extern int buy(int bId, int mProduct, int mPrice, int mQuantity);
extern int cancel(int bId);
extern int sell(int sId, int mProduct, int mPrice, int mQuantity);
extern int refund(int sId);

/////////////////////////////////////////////////////////////////////////

#define CMD_INIT 1
#define CMD_BUY 2
#define CMD_CANCEL 3
#define CMD_SELL 4
#define CMD_REFUND 5

static bool run() {
    int q;
    scanf("%d", &q);

    int id, product, price, quantity;
    int cmd, ans, ret = 0;
    bool okay = false;

    for (int i = 0; i < q; ++i) {
        scanf("%d", &cmd);
        switch (cmd) {
        case CMD_INIT:
            init();
            okay = true;
            break;
        case CMD_BUY:
            scanf("%d %d %d %d %d", &id, &product, &price, &quantity, &ans);
            ret = buy(id, product, price, quantity);
            if (ans != ret)
                okay = false;
            break;
        case CMD_CANCEL:
            scanf("%d %d", &id, &ans);
            ret = cancel(id);
            if (ans != ret)
                okay = false;
            break;
        case CMD_SELL:
            scanf("%d %d %d %d %d", &id, &product, &price, &quantity, &ans);
            ret = sell(id, product, price, quantity);
            if (ans != ret)
                okay = false;
            break;
        case CMD_REFUND:
            scanf("%d %d", &id, &ans);
            ret = refund(id);
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
    printf("Performance: %d ms\n", (clock() - start));
    return 0;
}

#endif // 1

```
