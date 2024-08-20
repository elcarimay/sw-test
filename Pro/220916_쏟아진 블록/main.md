```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



#define MAXN (1000)



extern void countBlock(int N, int mBoard[MAXN][MAXN], int mTetromino[5], int mPentomino[12]);



static int mBoard[MAXN][MAXN];

static int mTetromino[5];

static int mPentomino[12];



bool run()

{

	int N;


	scanf("%d", &N);



	for (int i = 0; i < N; ++i)

		for (int j = 0; j < N; ++j)

			scanf("%d", &mBoard[i][j]);



	for (int i = 0; i < 5; i++)

		mTetromino[i] = 0;



	for (int i = 0; i < 12; i++)

		mPentomino[i] = 0;


	bool okay = true;



	countBlock(N, mBoard, mTetromino, mPentomino);



	int ans;

	for (int i = 0; i < 5; ++i)

	{

		scanf("%d", &ans);

		if (mTetromino[i] != ans)

			okay = false;

	}


	for (int i = 0; i < 12; ++i)

	{

		scanf("%d", &ans);

		if (mPentomino[i] != ans)

			okay = false;

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



	for (int testcase = 1; testcase <= TC; ++testcase)

	{

		int score = run() ? MARK : 0;

		printf("#%d %d\n", testcase, score);

	}

	printf("\nPerformance: %d ms\n", (clock() - start));

	return 0;

}
```
