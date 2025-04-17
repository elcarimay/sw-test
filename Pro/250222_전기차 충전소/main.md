```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



extern void init(int N, int mCost[], int K, int mId[], int sCity[], int eCity[], int mDistance[]);

extern void add(int mId, int sCity, int eCity, int mDistance);

extern void remove(int mId);

extern int cost(int sCity, int eCity);



/////////////////////////////////////////////////////////////////////////



#define MAX_N 300

#define MAX_K 2000

#define CMD_INIT 100

#define CMD_ADD 200

#define CMD_REMOVE 300

#define CMD_COST 400



static bool run() {

	int q;

	scanf("%d", &q);



	int n, k;

	char strTmp[20];

	int mCostArr[MAX_N], mIdArr[MAX_K], sCityArr[MAX_K], eCityArr[MAX_K], mDistArr[MAX_K];

	int mId, sCity, eCity, mDist;

	int cmd, ans, ret = 0;

	bool okay = false;



	for (int i = 0; i < q; ++i) {

		scanf("%d %s", &cmd, strTmp);

		switch (cmd) {

		case CMD_INIT:

			okay = true;

			scanf("%s %d %s %d", strTmp, &n, strTmp, &k);

			for (int j = 0; j < n; ++j) {

				scanf("%s %d", strTmp, &mCostArr[j]);

			}

			for (int j = 0; j < k; ++j) {

				scanf("%s %d %s %d %s %d %s %d", strTmp, &mIdArr[j], strTmp, &sCityArr[j], strTmp, &eCityArr[j], strTmp, &mDistArr[j]);

			}

			init(n, mCostArr, k, mIdArr, sCityArr, eCityArr, mDistArr);

			break;

		case CMD_ADD:

			scanf("%s %d %s %d %s %d %s %d", strTmp, &mId, strTmp, &sCity, strTmp, &eCity, strTmp, &mDist);

			add(mId, sCity, eCity, mDist);

			break;

		case CMD_REMOVE:

			scanf("%s %d", strTmp, &mId);

			remove(mId);

			break;

		case CMD_COST:

			scanf("%s %d %s %d %s %d", strTmp, &sCity, strTmp, &eCity, strTmp, &ans);

			ret = cost(sCity, eCity);

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

#include<time.h>

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

	printf("EXECUTE TIME : %lld ms", clock() - start);

	return 0;

}
```
