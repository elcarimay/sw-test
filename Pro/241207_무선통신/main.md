```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



extern void init(int N, int mLimit);

extern void addRadio(int K, int mID[], int mFreq[], int mY[], int mX[]);

extern int getMinPower(int mID, int mCount);



#define CMD_INIT		0

#define CMD_ADDRADIO	1

#define CMD_GETPOWER	2



#define MAX_RADIO		100



static bool run()

{

	int Q, N, K, Limit;

	int id[MAX_RADIO], freq[MAX_RADIO], my[MAX_RADIO], mx[MAX_RADIO];

	int ret, ans;



	bool ok = false;



	scanf("%d", &Q);

	for (int q = 0; q < Q; q++)

	{

		int cmd;

		scanf("%d", &cmd);

		if (cmd == CMD_INIT)

		{

			scanf("%d %d", &N, &Limit);

			init(N, Limit);

			ok = true;

		}

		else if (cmd == CMD_ADDRADIO)

		{

			scanf("%d", &K);

			for (int i = 0; i < K; i++) scanf("%d %d %d %d", &id[i], &freq[i], &my[i], &mx[i]);

			addRadio(K, id, freq, my, mx);

		}

		else if (cmd == CMD_GETPOWER)

		{

			scanf("%d %d", &id[0], &freq[0]);

			ret = getMinPower(id[0], freq[0]);

			scanf("%d", &ans);

			if (ans != ret) {

				ok = false;

			}

		}

		else ok = false;

	}

	return ok;

}

#include <time.h>

int main()

{

	time_t start = clock();

	setbuf(stdout, NULL);

	freopen("sample_input.txt", "r", stdin);



	int T, MARK;

	scanf("%d %d", &T, &MARK);



	for (int tc = 1; tc <= T; tc++)

	{

		int score = run() ? MARK : 0;

		printf("#%d %d\n", tc, score);

	}

	printf("EXECUTE TIME : %lld ms", clock() - start);

	return 0;

}
```
