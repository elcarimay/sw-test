```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



#define CMD_INIT 100

#define CMD_SELECT_SEAT 200



extern void init(int W, int H);

extern int selectSeat(int mSeatNum);



static int W, H;



static bool run()

{

	int queryCnt, cmd, mSeatNum;

	int ans, res;

	bool okay = false;



	scanf("%d", &queryCnt);

	for (int i = 0; i < queryCnt; i++)

	{

		scanf("%d", &cmd);

		switch (cmd)

		{

		case CMD_INIT:

			scanf("%d%d", &W, &H);

			init(W, H);

			okay = true;

			break;

		case CMD_SELECT_SEAT:

			scanf("%d", &mSeatNum);

			res = selectSeat(mSeatNum);

			scanf("%d", &ans);

			if (ans != res)

			{

				okay = false;

			}

			break;

		}

	}



	return okay;

}

#include<time.h>

int main()

{
	clock_t start = clock();
	setbuf(stdout, NULL);

	freopen("sample_input.txt", "r", stdin);



	int T, MARK;

	scanf("%d%d", &T, &MARK);



	for (int tc = 1; tc <= T; tc++)

	{

		int score = run() ? MARK : 0;

		printf("#%d %d\n", tc, score);

	}

	printf("\nPerformance: %d ms\n", (clock() - start));

	return 0;

}
```
