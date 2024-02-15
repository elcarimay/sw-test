```cpp
#if 1
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



#define CMD_INIT 100

#define CMD_STOCK 200

#define CMD_SHIP 300

#define CMD_GET_HEIGHT 400



extern void init(int N);

extern int stock(int mLoc, int mBox);

extern int ship(int mLoc, int mBox);

extern int getHeight(int mLoc);



static bool run()

{

	int Q;

	int N, mLoc, mBox;


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

			init(N);

			okay = true;

			break;

		case CMD_STOCK:

			scanf("%d %d", &mLoc, &mBox);

			ret = stock(mLoc, mBox);

			scanf("%d", &ans);

			if (ret != ans)

				okay = false;

			break;

		case CMD_SHIP:

			scanf("%d %d", &mLoc, &mBox);

			ret = ship(mLoc, mBox);

			scanf("%d", &ans);

			if (ret != ans)

				okay = false;

			break;

		case CMD_GET_HEIGHT:

			scanf("%d", &mLoc);

			ret = getHeight(mLoc);

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

	printf("Performance = %d ms\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));

	return 0;

}
#endif
```
