```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



extern void init(int N, int K, int sCity[], int eCity[], int mLimit[]);

extern void add(int sCity, int eCity, int mLimit);

extern int calculate(int sCity, int eCity);



/////////////////////////////////////////////////////////////////////////



#define MAX_E 4000

#define CMD_INIT 100

#define CMD_ADD 200

#define CMD_CALC 300



static bool run() {

	int q;

	scanf("%d", &q);



	int n, k;

	int sCityArr[MAX_E], eCityArr[MAX_E], mLimitArr[MAX_E];

	int sCity, eCity, mLimit;

	int cmd, ans, ret = 0;

	bool okay = false;



	for (int i = 0; i < q; ++i) {

		scanf("%d", &cmd);

		switch (cmd) {

		case CMD_INIT:

			okay = true;

			scanf("%d %d", &n, &k);

			for (int j = 0; j < k; ++j) {

				scanf("%d %d %d", &sCityArr[j], &eCityArr[j], &mLimitArr[j]);

			}

			init(n, k, sCityArr, eCityArr, mLimitArr);

			break;

		case CMD_ADD:

			scanf("%d %d %d", &sCity, &eCity, &mLimit);

			add(sCity, eCity, mLimit);

			break;

		case CMD_CALC:

			scanf("%d %d %d", &sCity, &eCity, &ans);

			ret = calculate(sCity, eCity);

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



int main() {

	setbuf(stdout, NULL);

	freopen("sample_input.txt", "r", stdin);



	int T, MARK;

	scanf("%d %d", &T, &MARK);



	for (int tc = 1; tc <= T; tc++) {

		int score = run() ? MARK : 0;

		printf("#%d %d\n", tc, score);

	}



	return 0;

}
```
