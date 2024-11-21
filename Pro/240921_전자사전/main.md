```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>
#include <string.h>

#define CMD_INIT		(100)
#define CMD_ADD			(200)
#define CMD_ERASE		(300)
#define CMD_FIND		(400)
#define CMD_GET_INDEX	(500)

#define MAX_N			(30000)
#define MAX_L			(8)

struct RESULT
{
	int success;
	char word[MAX_L + 1];
};

extern void init(int N, char mWordList[][MAX_L + 1]);
extern int add(char mWord[]);
extern int erase(char mWord[]);
extern RESULT find(char mInitial, int mIndex);
extern int getIndex(char mWord[]);

static char mWordList[MAX_N][MAX_L + 1];

static bool run()
{
	int Q, N, mIndex;

	int ret = -1, ans;

	RESULT res;

	char mWord[MAX_L + 1];

	scanf("%d", &Q);

	bool okay = false;

	for (int q = 0; q < Q; ++q)
	{
		if (q == 6) {
			q = q;
		}
		int cmd;
		scanf("%d", &cmd);
		switch (cmd)
		{
		case CMD_INIT:
			scanf("%d", &N);
			for (int i = 0; i < N; ++i)
				scanf("%s", mWordList[i]);
			init(N, mWordList);
			okay = true;
			break;
		case CMD_ADD:
			scanf("%s", mWord);
			ret = add(mWord);
			scanf("%d", &ans);
			if (ret != ans)
				okay = false;
			break;
		case CMD_ERASE:
			scanf("%s", mWord);
			ret = erase(mWord);
			scanf("%d", &ans);
			if (ret != ans)
				okay = false;
			break;
		case CMD_FIND:
			scanf("%s %d", mWord, &mIndex);
			res = find(mWord[0], mIndex);
			scanf("%d", &ans);
			if (res.success != ans)
				okay = false;
			if (ans)
			{
				scanf("%s", mWord);
				if (strcmp(res.word, mWord) != 0)
					okay = false;
			}
			break;
		case CMD_GET_INDEX:
			scanf("%s", mWord);
			ret = getIndex(mWord);
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
