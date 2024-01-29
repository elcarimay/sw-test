```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>
#include <time.h>

struct Result
{
	int top;
	int count;
};

void init(int C);

Result dropBlocks(int mCol, int mHeight, int mLength);

#define CMD_INIT 100
#define CMD_DROP 200

static bool run()
{
	int query_num;
	scanf("%d", &query_num);

	int ans_top, ans_count;
	bool ok = false;

	for (int q = 0; q < query_num; q++)
	{
		int query;
		scanf("%d", &query);
		if (query == CMD_INIT)
		{
			int C;
			scanf("%d", &C);
			init(C);
			ok = true;
		}
		else if (query == CMD_DROP)
		{
			int mCol, mHeight, mLength;
			scanf("%d %d %d", &mCol, &mHeight, &mLength);
			Result ret = dropBlocks(mCol, mHeight, mLength);
			scanf("%d %d", &ans_top, &ans_count);
			if (ans_top != ret.top || ans_count != ret.count)
			{
				ok = false;
			}
		}
	}
	return ok;
}


int main()
{
	clock_t start = clock();
	setbuf(stdout, NULL);
	freopen("sample_input.txt", "r", stdin);
	int T, MARK;
	scanf("%d %d", &T, &MARK);

	for (int tc = 1; tc <= T; tc++)
	{
		int score = run() ? MARK : 0;
		printf("#%d %d\n", tc, score);
	}
	int result = (clock() - start) / (CLOCKS_PER_SEC / 1000);
	printf("\n>> Result: %d ms\n", result);
	return 0;
}
```
