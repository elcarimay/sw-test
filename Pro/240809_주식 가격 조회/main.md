```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>
#include <string.h>

extern void init();
extern int add(char mStockInfo[]);
extern int remove(int mStockCode);
extern int search(char mCondition[]);

/////////////////////////////////////////////////////////////////////////

#define MAX_STR_LEN 80

#define CMD_INIT 100
#define CMD_ADD 200
#define CMD_REMOVE 300
#define CMD_SEARCH 400

char stockInfo[MAX_STR_LEN];
char condition[MAX_STR_LEN];

static bool run() {
	bool okay = false;
	int Q, cmd, ans, ret;
	int stockCode;

	scanf("%d", &Q);

	for (int q = 0; q < Q; q++) {
		scanf("%d", &cmd);

		switch (cmd) {
		case CMD_INIT:
			init();
			okay = true;
			break;

		case CMD_ADD:
			memset(stockInfo, 0x00, sizeof(char) * MAX_STR_LEN);
			scanf("%s %d", stockInfo, &ans);
			ret = add(stockInfo);
			if (ans != ret)
				okay = false;
			break;

		case CMD_REMOVE:
			scanf("%d %d", &stockCode, &ans);
			ret = remove(stockCode);
			if (ans != ret)
				okay = false;
			break;

		case CMD_SEARCH:
			memset(condition, 0x00, sizeof(char) * MAX_STR_LEN);
			scanf("%s %d", condition, &ans);
			ret = search(condition);
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
	clock_t start = clock();
	setbuf(stdout, NULL);
	freopen("sample_input.txt", "r", stdin);

	int T, MARK;
	scanf("%d %d", &T, &MARK);

	for (int tc = 1; tc <= T; tc++) {
		int score = run() ? MARK : 0;
		printf("#%d %d\n", tc, score);
	}
	printf("Performance: %d ms\n", (clock() - start));
	return 0;
}
```
