```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include<stdio.h>



extern void init(int N);

extern void add(int mCarID, int mCompanyID, int mCarInfo[]);

extern int rent(int mCondition[]);

extern int promote(int mCompanyID, int mDiscount);



/////////////////////////////////////////////////////////////////////////



#define CMD_INIT 100

#define CMD_ADD 200

#define CMD_RENT 300

#define CMD_PROMOTE 400



static bool okay = false;



static bool run() {

	int cmd, ans, ret;

	int N, companyID, carID, discount;

	int carInfo[6], condition[6];

	int Q = 0;



	scanf("%d", &Q);



	for (int q = 0; q < Q; ++q) {

		scanf("%d", &cmd);



		switch (cmd) {

		case CMD_INIT:

			scanf("%d", &N);

			init(N);

			okay = true;

			break;



		case CMD_ADD:

			scanf("%d %d", &carID, &companyID);

			for (int i = 0; i < 6; i++)

				scanf("%d", carInfo + i);

			add(carID, companyID, carInfo);

			break;



		case CMD_RENT:

			for (int i = 0; i < 6; i++)

				scanf("%d", condition + i);

			ret = rent(condition);

			scanf("%d", &ans);

			if (ret != ans)

				okay = false;

			break;



		case CMD_PROMOTE:

			scanf("%d %d", &companyID, &discount);

			ret = promote(companyID, discount);

			scanf("%d", &ans);

			if (ret != ans)

				okay = false;

			break;



		default:

			okay = false;

		}

	}



	return okay;

}

#include <time.h>

int main() {

	time_t start = clock();

	setbuf(stdout, NULL);

	freopen("sample_input.txt", "r", stdin);



	int T, MARK;

	scanf("%d %d", &T, &MARK);



	for (int tc = 1; tc <= T; tc++) {

		int score = run() ? MARK : 0;

		printf("#%d %d\n", tc, score);

	}

	printf("\nEXECUTE TIME: %d ms", clock() - start);

	return 0;

}
```
