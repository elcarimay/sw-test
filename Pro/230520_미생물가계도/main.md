```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>
#include <time.h>

#define MAXL 12
#define CMD_INIT 100
#define CMD_ADD 200
#define CMD_DISTANCE 300
#define CMD_COUNT 400

extern void init(char mAncestor[], int mLastday);
extern int add(char mName[], char mParent[], int mFirstday, int mLastday);
extern int distance(char mName1[], char mName2[]);
extern int count(int mDay);

static bool run()
{
	int Q;
	int ret = -1, ans;
	bool okay = false;
	scanf("%d", &Q);
	for (int q = 0; q < Q; ++q)
	{
		int cmd;
		char mName1[MAXL], mName2[MAXL];
		int mDay1, mDay2;
		scanf("%d", &cmd);
		switch (cmd)
		{
		case CMD_INIT:
			scanf("%s %d", mName1, &mDay1);
			init(mName1, mDay1);
			okay = true;
			break;
		case CMD_ADD:
			scanf("%s %s %d %d", mName1, mName2, &mDay1, &mDay2);
			ret = add(mName1, mName2, mDay1, mDay2);
			scanf("%d", &ans);
			if (ret != ans)
				okay = false;
			break;
		case CMD_DISTANCE:
			scanf("%s %s", mName1, mName2);
			ret = distance(mName1, mName2);
			scanf("%d", &ans);
			if (ret != ans)
				okay = false;
			break;
		case CMD_COUNT:
			scanf("%d", &mDay1);
			ret = count(mDay1);
			scanf("%d", &ans);
			if (ret != ans)
				okay = false;
			break;
		default:
			okay = false;
			break;
		}
	}
	return okay;
}

int main()
{
	clock_t start = clock();
	setbuf(stdout, NULL);
	freopen("sample_input.txt", "r", stdin);
	int TC, MARK;
	scanf("%d %d", &TC, &MARK);
	for (int tc = 1; tc <= TC; ++tc)
	{
		int score = run() ? MARK : 0;
		printf("#%d %d\n", tc, score);
	}
	printf("RESULT : %dms\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));
	return 0;
}
```
