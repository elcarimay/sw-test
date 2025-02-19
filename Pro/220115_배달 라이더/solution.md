```cpp
#include <vector>
#include <algorithm>
using namespace std;

#define WORKING 1
#define WAITING 2

int map[303][303];

struct User {
	int x, y, riderid;
}user[503];

struct Rider {
	int dist;
	int x, y, taskid;
	bool state;
}rider[2003];

struct Task {
	int start, end;
}task[20003];

struct Server {
	vector<int> taskid; // 20,000
	int state;
	void situation() {
		state = taskid.size() ? WORKING : WAITING;
	}
	void update(int time) {
		for (int i = 0; i < taskid.size(); i++) {
			if (task[taskid[i]].end < time) taskid.erase(taskid.begin() + i);
		}
	}
}server[33];

int uCnt, rCnt, sCnt, tCnt, N, U, R, ct; // current time
void init(int N, int U, int uX[], int uY[],	int R, int rX[], int rY[]){
	uCnt = rCnt = sCnt = tCnt = 0, ::N = N, ::U = U, ::R = R;
	for (int i = 0; i < N; i++) server[sCnt++].state = WAITING;
	for (int i = 0; i < U; i++) user[uCnt++] = { uX[i], uY[i] };
	for (int i = 0; i < R; i++) rider[rCnt++] = { rX[i], rY[i] };
}

int order(int mTimeStamp, int uID){
	task[tCnt++].start = mTimeStamp;
	int sid;
	for (int i = 0; i < N; i++)
		if (server[i].state == WAITING) sid = i, server[i].
	return -1;
}

int checkWaitingRiders(int mTimeStamp){

	return -1;
}
```
