```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



#define CMD_INIT		(100)

#define CMD_SELL		(200)

#define CMD_CLOSE_SALE	(300)

#define CMD_DISCOUNT	(400)

#define CMD_SHOW		(500)



struct RESULT

{

	int cnt;

	int IDs[5];

};



extern void init();

extern int sell(int mID, int mCategory, int mCompany, int mPrice);

extern int closeSale(int mID);

extern int discount(int mCategory, int mCompany, int mAmount);

extern RESULT show(int mHow, int mCode);



static bool run()

{

	int Q;

	int mID, mCategory, mCompany, mPrice, mAmount;

	int mHow, mCode;



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

			init();

			okay = true;

			break;

		case CMD_SELL:

			scanf("%d %d %d %d", &mID, &mCategory, &mCompany, &mPrice);

			ret = sell(mID, mCategory, mCompany, mPrice);

			scanf("%d", &ans);

			if (ret != ans)

				okay = false;

			break;

		case CMD_CLOSE_SALE:

			scanf("%d", &mID);

			ret = closeSale(mID);

			scanf("%d", &ans);

			if (ret != ans)

				okay = false;

			break;

		case CMD_DISCOUNT:

			scanf("%d %d %d", &mCategory, &mCompany, &mAmount);

			ret = discount(mCategory, mCompany, mAmount);

			scanf("%d", &ans);

			if (ret != ans)

				okay = false;

			break;

		case CMD_SHOW:

			scanf("%d %d", &mHow, &mCode);

			res = show(mHow, mCode);

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

#include<time.h>

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
