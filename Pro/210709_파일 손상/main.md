```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

enum COMMAND {
	CMD_ADD = 1,
	CMD_MOVE,
	CMD_INFECT,
	CMD_RECOVER,
	CMD_REMOVE
};

extern void init();
extern int cmdAdd(int, int, int);
extern int cmdMove(int, int);
extern int cmdInfect(int);
extern int cmdRecover(int);
extern int cmdRemove(int);

static int run(int score) {
	int N;
	scanf("%d", &N);

	for (int i = 0; i < N; i++) {
		int cmd;
		int ret = 0;
		scanf("%d", &cmd);

		switch (cmd) {
		case CMD_ADD: {
			int id, pid, fileSize;
			scanf("%d%d%d", &id, &pid, &fileSize);
			ret = cmdAdd(id, pid, fileSize);
			break;
		}
		case CMD_MOVE: {
			int id, pid;
			scanf("%d%d", &id, &pid);
			ret = cmdMove(id, pid);
			break;
		}
		case CMD_INFECT: {
			int id;
			scanf("%d", &id);
			ret = cmdInfect(id);
			break;
		}
		case CMD_RECOVER: {
			int id;
			scanf("%d", &id);
			ret = cmdRecover(id);
			break;
		}
		case CMD_REMOVE: {
			int id;
			scanf("%d", &id);
			ret = cmdRemove(id);
			break;
		}
		}

		int checkSum;
		scanf("%d", &checkSum);
		if (ret != checkSum)
			score = 0;
	}
	return score;
}

#include <time.h>

int main() {
	clock_t start = clock();
	setbuf(stdout, NULL);
	freopen("sample_input.txt", "r", stdin);

	int TC, score;
	scanf("%d%d", &TC, &score);
	for (int t = 1; t <= TC; t++) {
		init();
		int ret = run(score);
		printf("#%d %d\n", t, ret);
	}
	printf("Performance: %d ms\n", clock() - start);
	return 0;
}
```
