```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

extern void init(int N, int K, int sCity[], int eCity[], int mCost[], int mTime[]);
extern void add(int sCity, int eCity, int mCost, int mTime);
extern int cost(int M, int sCity, int eCity);

/////////////////////////////////////////////////////////////////////////
#define MAX_E 500
#define CMD_INIT 100
#define CMD_ADD 200
#define CMD_COST 300

static bool run() {
	int q;
	scanf("%d", &q);
	int n, k, m;
	int sCityArr[MAX_E], eCityArr[MAX_E], mCostArr[MAX_E], mTimeArr[MAX_E];
	int sCity, eCity, mCost, mTime;
	int cmd, ans, ret = 0;
	bool okay = false;

	for (int i = 0; i < q; ++i) {
		scanf("%d", &cmd);
		switch (cmd) {
		case CMD_INIT:
			okay = true;
			scanf("%d %d", &n, &k);
			for (int j = 0; j < k; ++j) {
				scanf("%d %d %d %d", &sCityArr[j], &eCityArr[j], &mCostArr[j], &mTimeArr[j]);
			}
			init(n, k, sCityArr, eCityArr, mCostArr, mTimeArr);
			break;
		case CMD_ADD:
			scanf("%d %d %d %d", &sCity, &eCity, &mCost, &mTime);
			add(sCity, eCity, mCost, mTime);
			break;
		case CMD_COST:
			scanf("%d %d %d %d", &m, &sCity, &eCity, &ans);
			ret = cost(m, sCity, eCity);
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
