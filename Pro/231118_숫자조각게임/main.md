```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

#define MAX_ROW 40
#define MAX_COL 30

struct Result {
    int row;
    int col;
};

void init(int mRows, int mCols, int mCells[MAX_ROW][MAX_COL]);
Result putPuzzle(int mPuzzle[3][3]);
void clearPuzzles();


#define CMD_INIT 1
#define CMD_PUT 2
#define CMD_CLR 3


static bool run()
{
    int query_num;
    scanf("%d", &query_num);

    bool ok = false;

    for (int q = 0; q < query_num; q++)
    {
        int query;
        scanf("%d", &query);
        if (query == CMD_INIT)
        {
            int mRows, mCols;
            int mCells[MAX_ROW][MAX_COL];
            scanf("%d %d", &mRows, &mCols);
            for (int i = 0; i < mRows; i++) {
                for (int j = 0; j < mCols; j++) {
                    scanf("%d", &mCells[i][j]);
                }
            }
            init(mRows, mCols, mCells);
            ok = true;
        }
        else if (query == CMD_PUT)
        {
            char strPuzzle[10];
            int mPuzzle[3][3];
            int ans_row, ans_col;
            scanf("%s", strPuzzle);
            int cnt = 0;
            for (int i = 0; i < 3; i++) {
                for (int j = 0; j < 3; j++) {
                    mPuzzle[i][j] = strPuzzle[cnt] - '0';
                    cnt++;
                }
            }
            Result ret = putPuzzle(mPuzzle);
            scanf("%d %d", &ans_row, &ans_col);
            if (ans_row != ret.row || ans_col != ret.col)
            {
                ok = false;
            }
        }
        else if (query == CMD_CLR)
        {
            clearPuzzles();
        }
    }
    return ok;
}

#include <time.h>

int main()
{
    clock_t start = clock();
    setbuf(stdout, NULL);
#ifndef ONLINE_JUDGE
    freopen("sample_input.txt", "r", stdin);
#endif
    
    int T, MARK;
    scanf("%d %d", &T, &MARK);
    for (int tc = 1; tc <= T; tc++)
    {
        int score = run() ? MARK : 0;
        printf("#%d %d (%d ms)\n", tc, score, (clock() - start) / (CLOCKS_PER_SEC / 1000));
    }
    printf("\nPerformance: %d ms\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));
    return 0;
}
```
