```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif



#include <stdio.h>



#define CMD_INIT (100)

#define CMD_ADD_SCHEDULE (200)

#define CMD_GET_SCHEDULE (300)

#define CMD_DELETE_SCHEDULE (400)

#define CMD_FIND_EMPTY_SCHEDULE (500)



#define MAXL (11)



struct RESULT

{

	char mTitle[MAXL];

	int mStartDay;

	int mEndDay;

};



extern void init(int N);

extern int addSchedule(char mTitle[], int mStartDay, int mEndDay, int mForced);

extern RESULT getSchedule(int mDay);

extern int deleteSchedule(char mTitle[]);

extern int findEmptySchedule();



/////////////////////////////////////////////////////////////////////////

/////////////////////////////////////////////////////////////////////////



static int mstrcmp(const char* s1, const char* s2)

{

	while (*s1 && *s1 == *s2)

		++s1, ++s2;


	return *s1 - *s2;

}



static bool run()

{

	int Q, N;


	char mTitle[MAXL];

	int mStartDay, mEndDay, mDay, mForced;



	int ret = -1, ans;


	RESULT result;



	scanf("%d", &Q);


	bool okay = false;



	for (int q = 0; q <= Q; ++q)

	{

		int cmd;

		scanf("%d", &cmd);



		switch (cmd)

		{

		case CMD_INIT:

			scanf("%d", &N);

			init(N);

			okay = true;

			break;

		case CMD_ADD_SCHEDULE:

			scanf("%s %d %d %d", mTitle, &mStartDay, &mEndDay, &mForced);

			ret = addSchedule(mTitle, mStartDay, mEndDay, mForced);

			scanf("%d", &ans);

			if (ans != ret)

				okay = false;

			break;

		case CMD_GET_SCHEDULE:

			scanf("%d", &mDay);

			result = getSchedule(mDay);

			scanf("%s", mTitle);

			if (mTitle[0] != '$')

			{

				scanf("%d %d", &mStartDay, &mEndDay);

				if (mstrcmp(result.mTitle, mTitle) != 0

					|| result.mStartDay != mStartDay

					|| result.mEndDay != mEndDay)

					okay = false;

			}

			else

			{

				if (result.mTitle[0] != '\0')

					okay = false;

			}

			break;

		case CMD_DELETE_SCHEDULE:

			scanf("%s", mTitle);

			ret = deleteSchedule(mTitle);

			scanf("%d", &ans);

			if (ans != ret)

				okay = false;

			break;

		case CMD_FIND_EMPTY_SCHEDULE:

			ret = findEmptySchedule();

			scanf("%d", &ans);

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
