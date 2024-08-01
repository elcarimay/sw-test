```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



#define CMD_INIT 100

#define CMD_JOIN 200

#define CMD_PLAY_ROUND 300

#define CMD_LEAVE 400



#define MAXN 10000

#define MAXM 1500

#define MAXL 10



extern void init(int N, char mWordList[][MAXL + 1], char mSubjectList[][MAXL + 1]);

extern void join(int mID, int M, int mCardList[]);

extern int playRound(char mBeginStr[], char mSubject[]);

extern int leave(int mID);



static char mWordList[MAXN][MAXL + 1];

static char mSubjectList[MAXN][MAXL + 1];

static int mCardList[MAXM];



static bool run()

{

	int Q, N, M;

	char mBeginStr[3];

	char mSubject[MAXL + 1];



	int mID;



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

				scanf("%s", mWordList[i]);

			for (int i = 0; i < N; ++i)

				scanf("%s", mSubjectList[i]);

			init(N, mWordList, mSubjectList);

			okay = true;

			break;

		case CMD_JOIN:

			scanf("%d %d", &mID, &M);

			for (int i = 0; i < M; ++i)

				scanf("%d", &mCardList[i]);

			join(mID, M, mCardList);

			break;

		case CMD_PLAY_ROUND:

			scanf("%s %s", mBeginStr, mSubject);

			ret = playRound(mBeginStr, mSubject);

			scanf("%d", &ans);

			if (ret != ans)

				okay = false;

			break;

		case CMD_LEAVE:

			scanf("%d", &mID);

			ret = leave(mID);

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

	printf("\nPerformance: %d ms\n", clock() - start);

	return 0;

}


```
