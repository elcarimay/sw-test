```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



#define CMD_INIT			(100)

#define CMD_ORDER			(200)

#define CMD_CANCEL			(300)

#define CMD_GET_STATUS		(400)



extern void init(int N, int mCookingTimeList[]);

extern int order(int mTime, int mID, int M, int mDishes[]);

extern int cancel(int mTime, int mID);

extern int getStatus(int mTime, int mID);



#define MAX_NUM_DISHES		(10)



static bool run()

{

	int Q, N, M;

	int mTime, mID;



	int mCookingTimeList[MAX_NUM_DISHES];

	int mDishes[MAX_NUM_DISHES];



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

			scanf("%d", &N);

			for (int i = 0; i < N; ++i)

				scanf("%d", &mCookingTimeList[i]);

			init(N, mCookingTimeList);

			okay = true;

			break;

		case CMD_ORDER:

			scanf("%d %d %d", &mTime, &mID, &M);

			for (int i = 0; i < M; ++i)

				scanf("%d", &mDishes[i]);

			ret = order(mTime, mID, M, mDishes);

			scanf("%d", &ans);

			if (ret != ans)

				okay = false;

			break;

		case CMD_CANCEL:

			scanf("%d %d", &mTime, &mID);

			ret = cancel(mTime, mID);

			scanf("%d", &ans);

			if (ret != ans)

				okay = false;

			break;

		case CMD_GET_STATUS:

			scanf("%d %d", &mTime, &mID);

			ret = getStatus(mTime, mID);

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

	time_t start = clock();

	setbuf(stdout, NULL);

	freopen("sample_input.txt", "r", stdin);



	int TC, MARK;



	scanf("%d %d", &TC, &MARK);

	for (int tc = 1; tc <= TC; ++tc)

	{

		int score = run() ? MARK : 0;

		printf("#%d %d\n", tc, score);

	}

	printf("\nEXECUTE TIME : %d ms", clock() - start);

	return 0;

}
```
