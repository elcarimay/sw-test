```cpp
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif

#include <stdio.h>

#define CMD_INIT 100
#define CMD_ADD 200
#define CMD_MERGE 300
#define CMD_MOVE 400
#define CMD_RECRUIT 500

extern void init(int mNum);
extern void destroy();
extern int addDept(char mUpperDept[], char mNewDept[], int mNum);
extern int mergeDept(char mDept1[], char mDept2[]);
extern int moveEmployee(char mDept[], int mDepth, int mNum);
extern void recruit(int mDeptNum, int mNum);

/////////////////////////////////////////////////////////////////////////
static int Score;

static void cmd_init()
{
	int mNum;
	scanf("%d", &mNum);
	init(mNum);
}

static void cmd_destroy()
{
	destroy();
}

static void cmd_add()
{
	char mUpperDept[10], mNewDept[10];
	int mNum;
	scanf("%s %s %d", mUpperDept, mNewDept, &mNum);
	int result = addDept(mUpperDept, mNewDept, mNum);
	int check;
	scanf("%d", &check);
	if (result != check)
		Score = 0;
}

static void cmd_merge()
{
	char mDept1[10], mDept2[10];
	scanf("%s %s", mDept1, mDept2);
	int result = mergeDept(mDept1, mDept2);
	int check;
	scanf("%d", &check);
	if (result != check)
		Score = 0;
}

static void cmd_move()
{
	char mDept[10];
	int mDepth, mNum;
	scanf("%s %d %d", mDept, &mDepth, &mNum);
	int result = moveEmployee(mDept, mDepth, mNum);
	int check;
	scanf("%d", &check);
	if (result != check)
		Score = 0;
}

static void cmd_recruit()
{
	int deptNum, mNum;
	scanf("%d %d", &deptNum, &mNum);
	recruit(deptNum, mNum);
}

static void run()
{
	int N;
	scanf("%d", &N);
	Score = 100;
	for (int i = 0; i < N; ++i)
	{
		int cmd;
		scanf("%d", &cmd);
		switch (cmd)
		{
		case CMD_INIT: cmd_init(); break;
		case CMD_ADD: cmd_add(); break;
		case CMD_MOVE: cmd_move(); break;
		case CMD_MERGE: cmd_merge(); break;
		case CMD_RECRUIT: cmd_recruit(); break;
		default: break;
		}
	}
	cmd_destroy();
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
		run();
		if (Score == 100) printf("#%d %d\n", tc, MARK);
		else printf("#%d %d\n", tc, 0);
	}
	printf("Performance: %d ms\n", clock() - start);
	return 0;
}
```
