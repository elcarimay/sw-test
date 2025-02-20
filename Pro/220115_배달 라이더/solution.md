```cpp
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;

#define WORKING 1
#define WAITING 2
#define RESTAURANTGO 3
#define HOUSEGO 4

struct User {
	int x, y, d;
}u[503]; // user

struct Task {
	int uid, sid, rid; // sid는 server 30명 상태를보고 선형탐색해서 결정
	int ct, rt, ut; // ct:현재위치 출발시간, rt:음식점 도착시간, ut:고객도착시간
}t[20003]; // task

queue<int> que;

struct Rider {
	bool state;
	int x, y, d, tid;
	void dist(int nx, int ny) {
		x = nx, y = ny, d = abs(x) + abs(y);
	}
	void update(int time) {
		if (time >= ct && time < rt) state = RESTAURANTGO;
		else if (time >= rt && time <= ut) state = HOUSEGO;
		else { // 주문이 완료된 경우
			state = WAITING;
			dist(user[task[taskid].uid].x, user[task[taskid].uid].y);
		}
	}
}r[2003]; //rider

struct Data {
	int id, d;
	bool operator<(const Data& r)const {
		if (d != r.d) return d > r.d;
		return id > r.id;
	}
};
priority_queue<Data> rPQ; // rider PQ, 대기하는 rider

struct Server {
	int state, tid;
	vector<int> taskid;
	void call(int time, int taskid) {
		update(time); // 이전시간까지 update
		if (rPQ.empty()) { // 대기하는 rider가 있는지 여부
			state = WAITING; return;
		}
		while (!rPQ.empty()) {
			Data rider = rPQ.top();
			t[tid].rid = rider.id;
			t[tid].
			r[riderid].rt = time + rider[riderid].d;

			state = WORKING;
		}
	}
	void update(int time) {
		rider[riderid].update(time);
		if (rider[riderid].state == RESTAURANTGO) state = WORKING;
		if (rider[riderid].state == HOUSEGO) {
			state = WAITING;
		}
	}

}s[33]; // server


void update(int time) {

}

int tCnt, fCnt, N, U, R, ct, wS, wR; // wS: waiting server, wR: waiting Rider
void init(int N, int U, int uX[], int uY[],	int R, int rX[], int rY[]){
	tCnt = fCnt = 0, riderPQ = {};
	while (!que.empty()) que.pop();
	::N = wS = N, ::U = wR = U, ::R = R;
	for (int i = 0; i < N; i++) server[i].state = WAITING;
	for (int i = 0; i < U; i++) user[i] = { uX[i], uY[i], uX[i] + uY[i] };
	for (int i = 0; i < R; i++) {
		rider[i] = { i, WAITING }, rider[i].dist(rX[i], rY[i]);
		riderPQ.push({ i, rider[i].d });
	}
}

int order(int mTimeStamp, int uID){ // 20,000
	int tid = tCnt++;
	task[tid] = { mTimeStamp, uID };
	que.push(tid);
	int sid;

	return -1;
}

int checkWaitingRiders(int mTimeStamp){ // 20,000

	return R - fCnt;
}
```
