```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



extern void init(int mId, int mNum);

extern int add(int mId, int mNum, int mParent);

extern int remove(int mId);

extern int reorganize(int M, int K);



/////////////////////////////////////////////////////////////////////////



#define CMD_INIT 1

#define CMD_ADD 2

#define CMD_REMOVE 3

#define CMD_REORGANIZE 4



static bool run() {

	int q;

	scanf("%d", &q);



	int mid, mnum, mparent, m, k;

	int cmd, ans, ret = 0;

	bool okay = false;



	for (int i = 0; i < q; ++i) {

		scanf("%d", &cmd);

		switch (cmd) {

		case CMD_INIT:

			scanf("%d %d", &mid, &mnum);

			init(mid, mnum);

			okay = true;

			break;

		case CMD_ADD:

			scanf("%d %d %d %d", &mid, &mnum, &mparent, &ans);

			ret = add(mid, mnum, mparent);

			if (ans != ret)

				okay = false;

			break;

		case CMD_REMOVE:

			scanf("%d %d", &mid, &ans);

			ret = remove(mid);

			if (ans != ret)

				okay = false;

			break;

		case CMD_REORGANIZE:

			scanf("%d %d %d", &m, &k, &ans);

			ret = reorganize(m, k);

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

	printf("\nPerformance : %d ms\n", (clock() - start)/(CLOCKS_PER_SEC / 1000));

	return 0;

}

```
