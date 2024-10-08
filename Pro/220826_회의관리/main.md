```cpp
#ifndef _CRT_SECURE_NO_WARNINGS

#define _CRT_SECURE_NO_WARNINGS

#endif

#include <stdio.h>



#define CMD_INIT                                                (100)

#define CMD_ADD_MEETING                                        (200)

#define CMD_CANCEL_MEETING                                (300)

#define CMD_CHANGE_MEETING_MEMBER                (400)

#define CMD_CHANGE_MEETING                                (500)

#define CMD_CHECK_NEXT_MEETING                        (600)

#define MAXM (10)

#define MAXL (11)



extern void init();
extern int addMeeting(char mMeeting[MAXL], int M, char mMemberList[MAXM][MAXL], int mStartTime, int mEndTime);
extern int cancelMeeting(char mMeeting[MAXL]);
extern int changeMeetingMember(char mMeeting[MAXL], char mMember[MAXL]);
extern int changeMeeting(char mMeeting[MAXL], int mStartTime, int mEndTime);
extern void checkNextMeeting(char mMember[MAXL], int mTime, char mResult[MAXL]);



/////////////////////////////////////////////////////////////////////////

/////////////////////////////////////////////////////////////////////////



static int mstrcmp(const char* a, const char* b)

{

	while (*a && *a == *b)

		++a, ++b;

	return *a - *b;

}



static bool run()

{

	int Q, M;

	char mMeeting[MAXL];

	char mMemberList[MAXM][MAXL], mMember[MAXL];

	int mStartTime, mEndTime, mTime;

	int ret = -1, ans;

	char mResult[MAXL], aResult[MAXL];

	scanf("%d", &Q);

	bool okay = false;



	for (int q = 0; q < Q; ++q)

	{

		int cmd;

		scanf("%d", &cmd);



		switch (cmd)

		{

		case CMD_INIT:

			init();

			okay = true;

			break;

		case CMD_ADD_MEETING:

			scanf("%s %d", mMeeting, &M);

			for (int i = 0; i < M; ++i)

				scanf("%s", mMemberList[i]);

			scanf("%d %d", &mStartTime, &mEndTime);

			ret = addMeeting(mMeeting, M, mMemberList, mStartTime, mEndTime);

			scanf("%d", &ans);

			if (ans != ret)

				okay = false;

			break;

		case CMD_CANCEL_MEETING:

			scanf("%s", mMeeting);

			ret = cancelMeeting(mMeeting);

			scanf("%d", &ans);

			if (ans != ret)

				okay = false;

			break;

		case CMD_CHANGE_MEETING_MEMBER:

			scanf("%s %s", mMeeting, mMember);

			ret = changeMeetingMember(mMeeting, mMember);

			scanf("%d", &ans);

			if (ans != ret)

				okay = false;

			break;

		case CMD_CHANGE_MEETING:

			scanf("%s %d %d", mMeeting, &mStartTime, &mEndTime);

			ret = changeMeeting(mMeeting, mStartTime, mEndTime);

			scanf("%d", &ans);

			if (ans != ret)

				okay = false;

			break;

		case CMD_CHECK_NEXT_MEETING:

			scanf("%s %d", mMember, &mTime);

			checkNextMeeting(mMember, mTime, mResult);

			scanf("%s", &aResult);

			if (aResult[0] == '$')

				aResult[0] = '\0';

			if (mstrcmp(aResult, mResult) != 0)

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
