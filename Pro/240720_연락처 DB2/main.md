```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>
#include <string.h>

#define CMD_INIT		(100)
#define CMD_ADD			(200)
#define CMD_REMOVE		(300)
#define CMD_CALL		(400)
#define CMD_SEARCH		(500)

#define MAX_N			(5)
#define MAX_L			(8)

struct Result
{
	int size;
	char mNameList[MAX_N][MAX_L + 1];
	char mTelephoneList[MAX_N][MAX_L + 1];
};

extern void init();
extern void add(char mName[], char mTelephone[]);
extern void remove(char mStr[]);
extern void call(char mTelephone[]);
extern Result search(char mStr[]);

static bool run()
{
	int Q;
	char mName[MAX_L + 1];
	char mTelephone[MAX_L + 1];
	char mStr[MAX_L + 1];

	Result ret;

	int ans;

	scanf("%d", &Q);

	bool okay = false;

	for (int q = 0; q < Q; ++q)
	{
		int cmd;
		scanf("%d", &cmd);
		switch (cmd)
		{
		case CMD_INIT:
			init();
			okay = true;
			break;
		case CMD_ADD:
			scanf("%s %s", mName, mTelephone);
			add(mName, mTelephone);
			break;
		case CMD_REMOVE:
			scanf("%s", mStr);
			remove(mStr);
			break;
		case CMD_CALL:
			scanf("%s", mTelephone);
			call(mTelephone);
			break;
		case CMD_SEARCH:
			scanf("%s", mStr);
			ret = search(mStr);
			scanf("%d", &ans);
			if (ret.size != ans)
				okay = false;
			for (int i = 0; i < ans; ++i)
			{
				scanf("%s", mStr);
				if (mStr[0] != '$' && strcmp(mStr, ret.mNameList[i]) != 0)
					okay = false;
				scanf("%s", mStr);
				if (strcmp(mStr, ret.mTelephoneList[i]) != 0)
					okay = false;
			}
			break;
		default:
			okay = false;
			break;
		}
	}

	return okay;
}

#include <time.h>

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
	printf("Performance: %d ms\n", (clock() - start));
	return 0;
}
```
