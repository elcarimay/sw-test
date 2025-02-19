```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



extern void init(int N, int U, int uX[], int uY[],

	int R, int rX[], int rY[]);

extern int order(int mTimeStamp, int uID);

extern int checkWaitingRiders(int mTimeStamp);



#define CMD_INIT 100

#define CMD_ORDER 200

#define CMD_CHECK_WAITING_RIDERS 300



#define MAX_USER 500

#define MAX_RIDER 2000



static int ux[MAX_USER];

static int uy[MAX_USER];

static int rx[MAX_RIDER];

static int ry[MAX_RIDER];



static bool run()

{

	int query_num;

	scanf("%d", &query_num);



	int N, U, R;

	int mTimeStamp, uID;

	int ret, ans;

	bool ok = false;

	for (int q = 0; q < query_num; q++)

	{

		int query;

		scanf("%d", &query);



		if (query == CMD_INIT)

		{

			scanf("%d %d %d", &N, &U, &R);

			for (int i = 0; i < U; i++)

				scanf("%d", &ux[i]);

			for (int i = 0; i < U; i++)

				scanf("%d", &uy[i]);

			for (int i = 0; i < R; i++)

				scanf("%d", &rx[i]);

			for (int i = 0; i < R; i++)

				scanf("%d", &ry[i]);

			init(N, U, ux, uy, R, rx, ry);

			ok = true;

		}

		else if (query == CMD_ORDER)

		{

			scanf("%d %d", &mTimeStamp, &uID);

			ret = order(mTimeStamp, uID);

			scanf("%d", &ans);

			if (ret != ans)

			{

				ok = false;

			}

		}

		else if (query == CMD_CHECK_WAITING_RIDERS)

		{

			scanf("%d", &mTimeStamp);

			ret = checkWaitingRiders(mTimeStamp);

			scanf("%d", &ans);

			if (ret != ans)

			{

				ok = false;

			}

		}

	}

	return ok;

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
	printf("Performance: %d ms\n", clock() - start);
	return 0;

}
```
