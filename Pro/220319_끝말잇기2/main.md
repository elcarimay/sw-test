```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include<stdio.h>
#include<string.h>

#define MAX_N 50000
#define MAX_M 50000
#define MAX_LEN 11

extern void init(int N, int M, char mWords[][MAX_LEN]);
extern int playRound(int mID, char mCh);

static char mWords[MAX_M][MAX_LEN];

static bool run()
{
	bool ok = true;
	int N, M;
	int cnt;

	scanf("%d%d", &N, &M);
	for (int m = 0; m < M; m++)
	{
		scanf("%s", mWords[m]);
	}
	init(N, M, mWords);

	scanf("%d", &cnt);
	for (int i = 0; i < cnt; i++)
	{
		int mID, ret, ans;
		char mCh[2];

		scanf("%d", &mID);
		scanf("%s", mCh);
		ret = playRound(mID, mCh[0]);
		scanf("%d", &ans);
		if (ret != ans)
		{
			ok = false;
		}
	}

	return ok;
}

#include <time.h>

int main()
{
	clock_t start = clock();
	setbuf(stdout, NULL);
	freopen("sample_input.txt", "r", stdin);

	int T, MARK;
	scanf("%d%d", &T, &MARK);

	for (int tc = 1; tc <= T; tc++)
	{
		int score = run() ? MARK : 0;
		printf("#%d %d\n", tc, score);
	}
	printf("Performance: %d ms\n", (clock() - start));
	return 0;
}
```
