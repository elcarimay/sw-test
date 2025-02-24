```cpp
#if 1
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;

#define WAITING 1
#define RESTAURANTGO 2
#define HOUSEGO 3

int u[503]; // user당 음식점에서의 거리
queue<int> que; // 주문한 고객 uid

struct Rider {
	int state, rid, d, uid, finish;
}r[2003]; //rider

struct Data {
	int rid, d;
	bool operator<(const Data& r)const {
		if (d != r.d) return d > r.d;
		return rid > r.rid;
	}
};
priority_queue<Data> waitPQ; // waiting PQ, 대기하는 rider

struct Data2 {
	int rid, finish;
	bool operator<(const Data2& r)const {
		return finish > r.finish;
	}
};
priority_queue<Data2> workPQ; // 배달중인 작업에 대한 우큐

int waitS; // waiting Server
void update(int time) {
	int ct = 0; // current time
	while (ct < time) { // time 바로전까지 update
		ct = time; // 마지막에는 ct = time으로 설정되어 time에 가능한 일들을 처리
		if (!workPQ.empty()) ct = min(ct, workPQ.top().finish); // 중간이벤트의 종료시간으로 ct를 설정

		while (!workPQ.empty()) { // 일하고 있는 라이더에 대한 update
			int rid = workPQ.top().rid;
			if (r[rid].finish != ct) break;
			workPQ.pop();
			if (r[rid].state == RESTAURANTGO) { // 어떤 일이 끝나는 시간이면서 음식점으로 이동중일 때
				r[rid].state = HOUSEGO; // 음식점에 도착하면 서버는 waiting으로 라이더는 house go
				r[rid].finish += u[r[rid].uid]; // 고객집으로의 이동거리를 더함
				r[rid].d = u[r[rid].uid];
				waitS++; // 대기
				workPQ.push({ rid, r[rid].finish }); // 배달중인 상태로 다시 등록
			}
			else waitPQ.push({ rid, r[rid].d }); // 고객집으로 이동해서 끝나는 경우나 이미 끝난경우 라이더 대기우큐에 입력
		}
		while (!que.empty() && waitS && !waitPQ.empty()) { // 주문, 대기직원, 대기라이더가 있는 경우 배달 배정
			int rid = waitPQ.top().rid; waitPQ.pop(); // waiting 라이더 우큐에서 하나 빼서 라이더 배정함
			r[rid].uid = que.front(); que.pop(); // 주문한 고객을 라이더에 입력
			r[rid].finish = ct + r[rid].d; // 현시간에 라이더에서 음식점까지 위치를 더함
			r[rid].state = RESTAURANTGO; // 배정된 라이더가 음식점으로 출발 restaurant go
			waitS--;
			workPQ.push({ rid, r[rid].finish }); // 배달중인 상태로 등록
		}
	}
}

int tCnt, N, U, R;
void init(int N, int U, int uX[], int uY[], int R, int rX[], int rY[]) {
	tCnt = 0, waitPQ = {}, workPQ = {}, que = {};
	::N = waitS = N, ::U = U, ::R = R;
	for (int i = 0; i < U; i++) u[i] = uX[i] + uY[i];
	for (int i = 0; i < R; i++)
		r[i] = { WAITING, i, rX[i] + rY[i] }, waitPQ.push({ i, r[i].d });
}

int order(int mTimeStamp, int uID) { // 20,000
	update(mTimeStamp - 1); // 직전시간까지 update
	que.push(uID);
	update(mTimeStamp); // 현시간까지 update
	return waitS;
}

int checkWaitingRiders(int mTimeStamp) { // 20,000
	update(mTimeStamp);
	return waitPQ.size();
}
#endif // 1

```
