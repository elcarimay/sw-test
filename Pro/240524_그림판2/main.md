```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#define CMD_INIT 100
#define CMD_ENCODE 200
#define CMD_MAKE_DOT 300
#define CMD_PAINT 400
#define CMD_GET_COLOR 500
#include <vector>
#include <string.h>
#include <string>
#include <algorithm>
using namespace std;

extern void init(int N, int L, char mCode[]);
extern int encode(char mCode[]);
extern void makeDot(int mR, int mC, int mSize, int mColor);
extern void paint(int mR, int mC, int mColor);
extern int getColor(int mR, int mC);

static char mCode[200'001];
static char aCode[200'001];

static int mstrncmp(char a[], char b[], int n)
{

	for (int i = 0; i < n; ++i)

		if (a[i] != b[i])

			return a[i] - b[i];

	return 0;

}



static void readcode(int L, char code[])

{

	char buf[100];

	for (int i = 0; i < L;)

	{

		scanf("%s", buf);

		for (int j = 0; buf[j] != '\0'; ++j)

			code[i++] = buf[j];

	}

	code[L] = '\0';

}



static bool run()

{

	int Q, N, L;

	int mR, mC, mSize, mColor;



	int ret = -1, ans;



	scanf("%d", &Q);



	bool okay = false;



	for (int q = 0; q < Q; ++q)

	{
		int cmd;

		scanf("%d", &cmd);

		switch (cmd)

		{

		case CMD_INIT:

			scanf("%d %d", &N, &L);

			readcode(L, mCode);

			init(N, L, mCode);

			okay = true;

			break;

		case CMD_ENCODE:

			ret = encode(mCode);

			scanf("%d", &ans);

			readcode(ans, aCode);

			if (ret != ans || mstrncmp(mCode, aCode, ans) != 0) {
				printf("q : %d\n", q);
				okay = false;
			}

			break;

		case CMD_MAKE_DOT:

			scanf("%d %d %d %d", &mR, &mC, &mSize, &mColor);

			makeDot(mR, mC, mSize, mColor);

			break;

		case CMD_PAINT:

			scanf("%d %d %d", &mR, &mC, &mColor);

			paint(mR, mC, mColor);

			break;

		case CMD_GET_COLOR:

			scanf("%d %d", &mR, &mC);

			ret = getColor(mR, mC);

			scanf("%d", &ans);

			if (ret != ans) {
				printf("q : %d\n", q);
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

		printf("#%d %d (%d ms)\n", tc, score, (clock() - start) / (CLOCKS_PER_SEC / 1000));

	}

	printf("\nPerformance : %d ms\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));

	return 0;

}
```
