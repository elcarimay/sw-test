```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



extern void init(int N);

extern void addRoad(int K, int mSpotA[], int mSpotB[], int mDis[]);

extern void addBikeRent(int mSpot);

extern int getMinMoney(int mStartSpot, int mEndSpot, int mMaxTime);



#define CMD_INIT	0

#define CMD_ROAD	1

#define CMD_BIKE	2

#define CMD_MONEY	3



int spotA[30], spotB[30], dis[30];

static bool run()

{

	int Q, N, K;

	int maxTime;

	int ret, ans;



	bool ok = false;



	scanf("%d", &Q);

	for (int q = 0; q < Q; q++)

	{

		int cmd;

		scanf("%d", &cmd);

		if (cmd == CMD_INIT)

		{

			scanf("%d", &N);

			init(N);

			ok = true;

		}

		else if (cmd == CMD_ROAD)

		{

			scanf("%d", &K);

			for (int i = 0; i < K; i++) scanf("%d %d %d", &spotA[i], &spotB[i], &dis[i]);

			addRoad(K, spotA, spotB, dis);

		}

		else if (cmd == CMD_BIKE)

		{

			scanf("%d", &spotA[0]);

			addBikeRent(spotA[0]);

		}

		else if (cmd == CMD_MONEY)

		{

			scanf("%d %d %d", &spotA[0], &spotB[0], &maxTime);

			ret = getMinMoney(spotA[0], spotB[0], maxTime);

			scanf("%d", &ans);

			if (ans != ret) {

				ok = false;

			}

		}

		else ok = false;

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
