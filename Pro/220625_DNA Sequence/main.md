```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



#define CMD_INIT (100)

#define CMD_ADD_SEQ (200)

#define CMD_SEARCH_SEQ (300)

#define CMD_ERASE_SEQ (400)

#define CMD_CHANGE_BASE (500)



#define MAXL (61)



extern void init();

extern int addSeq(int mID, int mLen, char mSeq[]);

extern int searchSeq(int mLen, char mBegin[]);

extern int eraseSeq(int mID);

extern int changeBase(int mID, int mPos, char mBase);



static bool run()

{

	int Q;


	int mID, mLen, mPos;

	char mSeq[MAXL], mBegin[MAXL];

	char mBase[2];


	int ret = -1, ans;


	scanf("%d", &Q);


	bool okay = false;



	for (int q = 0; q <= Q; ++q)

	{

		int cmd;


		scanf("%d", &cmd);



		switch (cmd)

		{

		case CMD_INIT:

			init();

			okay = true;

			break;

		case CMD_ADD_SEQ:

			scanf("%d %d %s", &mID, &mLen, mSeq);

			if (okay)

				ret = addSeq(mID, mLen, mSeq);

			scanf("%d", &ans);

			if (ans != ret)

				okay = false;

			break;

		case CMD_SEARCH_SEQ:

			scanf("%d %s", &mLen, mBegin);

			if (okay)

				ret = searchSeq(mLen, mBegin);

			scanf("%d", &ans);

			if (ans != ret)

				okay = false;

			break;

		case CMD_ERASE_SEQ:

			scanf("%d", &mID);

			if (okay)

				ret = eraseSeq(mID);

			scanf("%d", &ans);

			if (ans != ret)

				okay = false;

			break;

		case CMD_CHANGE_BASE:

			scanf("%d %d %s", &mID, &mPos, mBase);

			if (okay)

				ret = changeBase(mID, mPos, mBase[0]);

			scanf("%d", &ans);

			if (ans != ret)

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

	printf("Performance: %d ms\n", (clock() - start));

	return 0;

}
```
