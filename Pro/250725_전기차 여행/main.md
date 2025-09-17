```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif
#include <stdio.h>
extern void init(int N, int mCharge[], int K, int mId[], int sCity[], int eCity[], int mTime[], int mPower[]);
extern void add(int mId, int sCity, int eCity, int mTime, int mPower);
extern void remove(int mId);
extern int cost(int B, int sCity, int eCity, int M, int mCity[], int mTime[]);
/////////////////////////////////////////////////////////////////////////
#define MAX_N 500
#define MAX_M 5
#define MAX_K 4000
#define CMD_INIT 100
#define CMD_ADD 200
#define CMD_REMOVE 300
#define CMD_COST 400

static bool run() {
	int q;
	scanf("%d", &q);
	int n, m, k, b;
	int mChargeArr[MAX_N], mIdArr[MAX_K], sCityArr[MAX_K], eCityArr[MAX_K], mTimeArr[MAX_K], mPowerArr[MAX_K];
	int mCityArr[MAX_M];
	int mId, sCity, eCity, mTime, mPower;
	int cmd, ans, ret = 0;
	bool okay = false;
	for (int i = 0; i < q; ++i) {
		scanf("%d", &cmd);
		switch (cmd) {
			case CMD_INIT:
				okay = true;
				scanf("%d %d", &n, &k);
				for (int j = 0; j < n; ++j) {
					scanf("%d", &mChargeArr[j]);
				}
				for (int j = 0; j < k; ++j) {
					scanf("%d %d %d %d %d", &mIdArr[j], &sCityArr[j], &eCityArr[j], &mTimeArr[j], &mPowerArr[j]);
				}
				init(n, mChargeArr, k, mIdArr, sCityArr, eCityArr, mTimeArr, mPowerArr);
				break;
			case CMD_ADD:
				scanf("%d %d %d %d %d", &mId, &sCity, &eCity, &mTime, &mPower);
				add(mId, sCity, eCity, mTime, mPower);
				break;
			case CMD_REMOVE:
				scanf("%d", &mId);
				remove(mId);
				break;
			case CMD_COST:
				scanf("%d %d %d %d %d", &b, &sCity, &eCity, &ans, &m);
				for (int j = 0; j < m; ++j) {
					scanf("%d %d", &mCityArr[j], &mTimeArr[j]);
				}
				ret = cost(b, sCity, eCity, m, mCityArr, mTimeArr);
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
