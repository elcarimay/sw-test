```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



extern void init(int N, int K, int mId[], int sId[], int eId[], int mInterval[]);

extern void add(int mId, int sId, int eId, int mInterval);

extern void remove(int mId);

extern int calculate(int sId, int eId);



/////////////////////////////////////////////////////////////////////////



#define MAX_K 200

#define CMD_INIT 100

#define CMD_ADD 200

#define CMD_REMOVE 300

#define CMD_CALC 400



static bool run() {

	int q;

	scanf("%d", &q);



	int n, k;

	char strTmp[30];

	int mIdArr[MAX_K], sIdArr[MAX_K], eIdArr[MAX_K], mIntervalArr[MAX_K];

	int mId, sId, eId, mInterval;

	int cmd, ans, ret = 0;

	bool okay = false;



	for (int i = 0; i < q; ++i) {

		scanf("%d %s", &cmd, strTmp);

		switch (cmd) {

		case CMD_INIT:

			okay = true;

			scanf("%s %d %s %d", strTmp, &n, strTmp, &k);

			for (int j = 0; j < k; ++j) {

				scanf("%s %d %s %d %s %d %s %d", strTmp, &mIdArr[j], strTmp, &sIdArr[j], strTmp, &eIdArr[j], strTmp, &mIntervalArr[j]);

			}

			init(n, k, mIdArr, sIdArr, eIdArr, mIntervalArr);

			break;

		case CMD_ADD:

			scanf("%s %d %s %d %s %d %s %d", strTmp, &mId, strTmp, &sId, strTmp, &eId, strTmp, &mInterval);

			add(mId, sId, eId, mInterval);

			break;

		case CMD_REMOVE:

			scanf("%s %d", strTmp, &mId);

			remove(mId);

			break;

		case CMD_CALC:

			scanf("%s %d %s %d %s %d", strTmp, &sId, strTmp, &eId, strTmp, &ans);

			ret = calculate(sId, eId);

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

	printf("EXECUTE TIME : %d ms", clock() - start);

	return 0;

}
```
