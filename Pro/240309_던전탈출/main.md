```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif

#include <iostream>
#include <cstdio>
using namespace std;

#define MAX_MAP_SIZE 350



extern void init(int N, int mMaxStamina, int mMap[MAX_MAP_SIZE][MAX_MAP_SIZE]);

extern void addGate(int mGateID, int mRow, int mCol);

extern void removeGate(int mGateID);

extern int getMinTime(int mStartGateID, int mEndGateID);



/////////////////////////////////////////////////////////////////////////



#define CMD_INIT			0

#define CMD_ADD_GATE		1

#define CMD_REMOVE_GATE		2

#define CMD_GET_MIN_TIME	3



static int gMap[MAX_MAP_SIZE][MAX_MAP_SIZE];



static bool run()

{

	int cmd, ans, ret;

	int N, maxStamina, gateID1, gateID2, row, col;

	int Q = 0;

	bool okay = false;



	scanf("%d", &Q);



	for (int q = 0; q < Q; ++q)

	{

		scanf("%d", &cmd);

		switch (cmd)

		{

		case CMD_INIT:

			scanf("%d %d", &N, &maxStamina);

			for (int i = 0; i <= N - 1; i++) {

				for (int j = 0; j <= N - 1; j++) {

					scanf("%d", &gMap[i][j]);

				}

			}

			init(N, maxStamina, gMap);

			okay = true;

			break;



		case CMD_ADD_GATE:

			scanf("%d %d %d", &gateID1, &row, &col);

			addGate(gateID1, row, col);

			break;



		case CMD_REMOVE_GATE:

			scanf("%d", &gateID1);

			removeGate(gateID1);

			break;



		case CMD_GET_MIN_TIME:

			scanf("%d %d", &gateID1, &gateID2);

			ret = getMinTime(gateID1, gateID2);

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



	for (int tc = 1; tc<= T; tc++)

	{

		int score = run() ? MARK : 0;

		printf("#%d %d\n", tc, score);

	}

	printf("\nPerformance : %d ms\n", (clock() - start) / (CLOCKS_PER_SEC / 1000));

	return 0;

}
```
