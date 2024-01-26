```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>

#include <string.h>
#include <time.h>



struct Result

{

	int mOrder;

	int mRank;

};



extern void init();

extern void search(char mStr[], int count);

extern Result recommend(char mStr[]);

extern int relate(char mStr1[], char mStr2[]);

extern void rank(char mPrefix[], int mRank, char mReturnStr[]);



#define MAX_LENGTH      (7 + 1)

#define CMD_INIT		(100)

#define CMD_SEARCH		(200)

#define CMD_RECOMMEND	(300)

#define CMD_RELATE		(400)

#define CMD_RANK		(500)



static bool run()

{

	int query_num;

	bool okay = false;



	scanf("%d", &query_num);



	for (int q = 0; q < query_num; ++q) {

		int cmd, ret, ans, ans2, mCount, mRank;

		char mStr[MAX_LENGTH], mStr2[MAX_LENGTH], mReturnStr[MAX_LENGTH];

		Result res;



		scanf("%d", &cmd);



		switch (cmd) {

		case CMD_INIT:

			init();

			okay = true;

			break;

		case CMD_SEARCH:

			scanf("%s %d", mStr, &mCount);

			search(mStr, mCount);

			break;

		case CMD_RECOMMEND:

			scanf("%s", mStr);

			res = recommend(mStr);

			scanf("%d %d", &ans, &ans2);

			if (res.mOrder != ans || res.mRank != ans2)

				okay = false;

			break;

		case CMD_RELATE:

			scanf("%s %s", mStr, mStr2);

			ret = relate(mStr, mStr2);

			scanf("%d", &ans);

			if (ret != ans)

				okay = false;

			break;

		case CMD_RANK:

			scanf("%s %d", mStr, &mRank);

			rank(mStr, mRank, mReturnStr);

			scanf("%s", mStr2);

			if (strncmp(mStr2, mReturnStr, MAX_LENGTH) != 0)

				okay = false;

			break;

		default:

			okay = false;

			break;

		}

	}



	return okay;

}



int main()

{
	clock_t start = clock();

	setbuf(stdout, NULL);

	freopen("sample_input.txt", "r", stdin);



	int T, MARK;

	scanf("%d %d", &T, &MARK);



	for (int tc = 1; tc <= T; tc++) {

		int score = run() ? MARK : 0;

		printf("#%d %d\n", tc, score);

	}

	printf("Result: %d ms\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));

	return 0;

}
```
