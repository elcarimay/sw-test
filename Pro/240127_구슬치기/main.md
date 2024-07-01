```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



extern void init(int N);

extern void addDiagonal(int mARow, int mACol, int mBRow, int mBCol);

extern void addHole(int mRow, int mCol, int mID);

extern void eraseHole(int mRow, int mCol);

extern int hitMarble(int mRow, int mCol, int mK);



/////////////////////////////////////////////////////////////////////////



#define CMD_INIT	0

#define CMD_DIAG	1

#define CMD_ADD		2

#define CMD_ERASE	3

#define CMD_HIT		4



static bool run()

{

	int cmd, n, row, col, id, row1, col1, mk, ret;

	int ans;



	int Q = 0;

	bool okay = false;



	scanf("%d", &Q);

	for (int q = 0; q < Q; ++q)

	{

		scanf("%d", &cmd);

		switch (cmd)

		{

		case CMD_INIT:

			scanf("%d", &n);

			init(n);

			okay = true;

			break;



		case CMD_DIAG:

			scanf("%d %d %d %d", &row, &col, &row1, &col1);

			addDiagonal(row, col, row1, col1);

			break;



		case CMD_ADD:

			scanf("%d %d %d", &row, &col, &id);

			addHole(row, col, id);

			break;

		case CMD_ERASE:

			scanf("%d %d", &row, &col);

			eraseHole(row, col);

			break;



		case CMD_HIT:

			scanf("%d %d %d", &row, &col, &mk);

			ret = hitMarble(row, col, mk);

			scanf("%d", &ans);

			if (ret != ans) {

				okay = false;

			}

			break;



		default:

			okay = false;

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



	for (int tc = 1; tc <= T; tc++)

	{

		int score = run() ? MARK : 0;

		printf("#%d %d\n", tc, score);

	}

	printf("Performance = %d ms\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));

	return 0;

}
```
