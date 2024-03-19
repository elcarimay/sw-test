```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>;



extern void init(int N);

extern void buildTower(int mRow, int mCol, int mColor);

extern void removeTower(int mRow, int mCol);

extern int countTower(int mRow, int mCol, int mColor, int mDis);

extern int getClosest(int mRow, int mCol, int mColor);



/////////////////////////////////////////////////////////////////////////



#define CMD_INIT 0

#define CMD_BUILD 1

#define CMD_REMOVE 2

#define CMD_COUNT 3

#define CMD_GET 4



static bool run()

{

	int cmd, n, row, col, color, dis, ret;

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



		case CMD_BUILD:

			scanf("%d %d %d", &row, &col, &color);

			buildTower(row, col, color);

			break;



		case CMD_REMOVE:

			scanf("%d %d", &row, &col);

			removeTower(row, col);

			break;



		case CMD_COUNT:

			scanf("%d %d %d %d", &row, &col, &color, &dis);

			ret = countTower(row, col, color, dis);

			scanf("%d", &ans);

			if (ret != ans) {

				okay = false;

			}

			break;



		case CMD_GET:

			scanf("%d %d %d", &row, &col, &color);

			ret = getClosest(row, col, color);

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

	printf("\nPerformance : %d ms\n", (clock() - start)/(CLOCKS_PER_SEC / 1000));

	return 0;

}
```
