```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



#define CMD_INIT 1

#define CMD_CARRY 2

#define CMD_GATHER 3

#define CMD_SUM 4



extern void init(int N, int mParent[], int mDistance[], int mQuantity[]);

extern int carry(int mFrom, int mTo, int mQuantity);

extern int gather(int mID, int mQuantity);

extern int sum(int mID);



/////////////////////////////////////////////////////////////////////////

/////////////////////////////////////////////////////////////////////////



static int initParent[10000];

static int initDistance[10000];

static int initQuantity[10000];



static bool run()

{

	int numQuery;

	int N, mFrom, mTo, mQuantity, mID;

	int userAns, ans;



	bool isCorrect = false;



	scanf("%d", &numQuery);



	for (int q = 0; q < numQuery; q++)

	{

		int cmd;

		scanf("%d", &cmd);



		switch (cmd)

		{

		case CMD_INIT:

			scanf("%d", &N);

			for (int i = 0; i < N; i++)

				scanf("%d", &initParent[i]);

			for (int i = 0; i < N; i++)

				scanf("%d", &initDistance[i]);

			for (int i = 0; i < N; i++)

				scanf("%d", &initQuantity[i]);

			init(N, initParent, initDistance, initQuantity);

			isCorrect = true;

			break;

		case CMD_CARRY:

			scanf("%d %d %d", &mFrom, &mTo, &mQuantity);

			userAns = carry(mFrom, mTo, mQuantity);

			scanf("%d", &ans);

			if (userAns != ans)

			{

				isCorrect = false;

			}

			break;

		case CMD_GATHER:

			scanf("%d %d", &mID, &mQuantity);

			userAns = gather(mID, mQuantity);

			scanf("%d", &ans);

			if (userAns != ans)

			{

				isCorrect = false;

			}

			break;

		case CMD_SUM:

			scanf("%d", &mID);

			userAns = sum(mID);

			scanf("%d", &ans);

			if (userAns != ans)

			{

				isCorrect = false;

			}

			break;

		default:

			isCorrect = false;

			break;

		}

	}

	return isCorrect;

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
