#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

extern void init(int N, int K, int sCity[], int eCity[], int mLimit[]);
extern void add(int sCity, int eCity, int mLimit);
extern int calculate(int sCity, int eCity, int M, int mStopover[]);

/////////////////////////////////////////////////////////////////////////

#define MAX_E 2000
#define MAX_S 3
#define CMD_INIT 100
#define CMD_ADD 200
#define CMD_CALC 300

static bool run() {
	int q;
	scanf("%d", &q);

	int n, m, k;
	char strTmp[20];
	int sCityArr[MAX_E], eCityArr[MAX_E], mLimitArr[MAX_E], mStopover[MAX_S];
	int sCity, eCity, mLimit;
	int cmd, ans, ret = 0;
	bool okay = false;

	for (int i = 0; i < q; ++i) {
		scanf("%d %s", &cmd, strTmp);
		switch (cmd) {
			case CMD_INIT:
				okay = true;
				scanf("%s %d %s %d", strTmp, &n, strTmp, &k);
				for (int j = 0; j < k; ++j) {
					scanf("%s %d %s %d %s %d", strTmp, &sCityArr[j], strTmp, &eCityArr[j], strTmp, &mLimitArr[j]);
				}
				init(n, k, sCityArr, eCityArr, mLimitArr);
				break;
			case CMD_ADD:
				scanf("%s %d %s %d %s %d", strTmp, &sCity, strTmp, &eCity, strTmp, &mLimit);
				add(sCity, eCity, mLimit);
				break;
			case CMD_CALC:
				scanf("%s %d %s %d %s %d", strTmp, &sCity, strTmp, &eCity, strTmp, &m);
				for (int j = 0; j < m; ++j) {
					scanf("%s %d", strTmp, &mStopover[j]);
				}
				scanf("%s %d", strTmp, &ans);
				ret = calculate(sCity, eCity, m, mStopover);
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
	//freopen("sample_input.txt", "r", stdin);

	int T, MARK;
	scanf("%d %d", &T, &MARK);

	for (int tc = 1; tc <= T; tc++) {
		int score = run() ? MARK : 0;
		printf("#%d %d\n", tc, score);
	}

	return 0;
}
