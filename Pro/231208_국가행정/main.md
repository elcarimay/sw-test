```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



extern void init(int N, int mPopulation[]);

extern int expand(int M);

extern int calculate(int mFrom, int mTo);

extern int divide(int mFrom, int mTo, int K);



/////////////////////////////////////////////////////////////////////////



#define MAX_N 10000



#define CMD_INIT 100

#define CMD_EXPAND 200

#define CMD_CALCULATE 300

#define CMD_DIVIDE 400



static bool run()

{

	int population[MAX_N];

	int cmd, ans, ret;

	int Q = 0;

	int N, from, to, num;

	bool okay = false;



	scanf("%d", &Q);



	for (int q = 0; q < Q; ++q)

	{

		scanf("%d", &cmd);



		switch (cmd)

		{

		case CMD_INIT:

			scanf("%d", &N);



			for (int i = 0; i < N; i++)

				scanf("%d", &population[i]);



			init(N, population);

			okay = true;

			break;



		case CMD_EXPAND:

			scanf("%d", &num);

			ret = expand(num);

			scanf("%d", &ans);

			if (ret != ans)

				okay = false;

			break;



		case CMD_CALCULATE:

			scanf("%d %d", &from, &to);

			ret = calculate(from, to);

			scanf("%d", &ans);

			if (ret != ans)

				okay = false;

			break;



		case CMD_DIVIDE:

			scanf("%d %d %d", &from, &to, &num);

			ret = divide(from, to, num);

			scanf("%d", &ans);

			if (ret != ans)

				okay = false;

			break;



		default:

			okay = false;

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



	int T, MARK;

	scanf("%d %d", &T, &MARK);



	for (int tc = 1; tc <= T; tc++)

	{

		int score = run() ? MARK : 0;

		printf("#%d %d\n", tc, score);

	}

	printf("Performance = %d ms\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));

	return 0;

}
```
