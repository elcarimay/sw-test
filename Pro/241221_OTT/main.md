```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



#define CMD_INIT        (100)

#define CMD_ADD         (200)

#define CMD_ERASE       (300)

#define CMD_WATCH       (400)

#define CMD_SUGGEST     (500)



struct RESULT

{

	int cnt;

	int IDs[5];

};



extern void init(int N);

extern int add(int mID, int mGenre, int mTotal);

extern int erase(int mID);

extern int watch(int uID, int mID, int mRating);

extern RESULT suggest(int uID);



static bool run()

{

	int Q, N;

	int mID, mGenre, mTotal, mRating, uID;



	int ret = -1, cnt, ans;



	RESULT res;



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

		case CMD_ADD:

			scanf("%d %d %d", &mID, &mGenre, &mTotal);

			ret = add(mID, mGenre, mTotal);

			scanf("%d", &ans);

			if (ret != ans)

				okay = false;

			break;

		case CMD_ERASE:

			scanf("%d", &mID);

			ret = erase(mID);

			scanf("%d", &ans);

			if (ret != ans)

				okay = false;

			break;

		case CMD_WATCH:

			scanf("%d %d %d", &uID, &mID, &mRating);

			ret = watch(uID, mID, mRating);

			scanf("%d", &ans);

			if (ret != ans)

				okay = false;

			break;

		case CMD_SUGGEST:

			scanf("%d", &uID);

			res = suggest(uID);

			scanf("%d", &cnt);

			if (res.cnt != cnt)

				okay = false;

			for (int i = 0; i < cnt; ++i)

			{

				scanf("%d", &ans);

				if (res.IDs[i] != ans)

					okay = false;

			}

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

	printf("EXECUTE TIME : %lld ms", clock() - start);

	return 0;

}
```
