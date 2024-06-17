```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

#define MAXL (11)
#define MAXM (101)

#define CMD_INIT    (100)
#define CMD_ASSIGN    (200)
#define CMD_COMPUTE    (300)
#define CMD_GET_COEFFICIENT  (400)
#define CMD_CALC_POLYNOMIAL  (500)

extern void init();
extern void assign(char mName[], char mPolynomial[]);
extern void compute(char mNameR[], char mNameA[], char mNameB[], int mOp);
extern int getCoefficient(char mName[], int mDegreeX, int mDegreeY);
extern int calcPolynomial(char mName[], int mX, int mY);

static bool run()
{
    int Q;

    char mName[MAXL], mNameA[MAXL], mNameB[MAXL], mNameR[MAXL];
    char mPolynomial[MAXM];

    int mOp, mDegreeX, mDegreeY, mX, mY;

    int ret = -1, ans;

    scanf("%d", &Q);

    bool okay = false;

    for (int q = 0; q < Q; ++q)
    {
        int cmd;
        scanf("%d", &cmd);

        switch (cmd)
        {
        case CMD_INIT:
            init();
            okay = true;
            break;
        case CMD_ASSIGN:
            scanf("%s %s", mName, mPolynomial);
            assign(mName, mPolynomial);
            break;
        case CMD_COMPUTE:
            scanf("%s %s %s %d", mNameR, mNameA, mNameB, &mOp);
            compute(mNameR, mNameA, mNameB, mOp);
            break;
        case CMD_GET_COEFFICIENT:
            scanf("%s %d %d", mName, &mDegreeX, &mDegreeY);
            ret = getCoefficient(mName, mDegreeX, mDegreeY);
            scanf("%d", &ans);
            if (ret != ans)
                okay = false;
            break;
        case CMD_CALC_POLYNOMIAL:
            scanf("%s %d %d", mName, &mX, &mY);
            ret = calcPolynomial(mName, mX, mY);
            scanf("%d", &ans);
            if (ret != ans)
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

    int TC, MARK;

    scanf("%d %d", &TC, &MARK);

    for (int tc = 1; tc <= TC; ++tc)
    {
        int score = run() ? MARK : 0;
        printf("#%d %d\n", tc, score);
    }

    printf("RESULT : %dms\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));
    return 0;
}
```
