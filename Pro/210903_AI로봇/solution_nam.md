```cpp
#include <queue>
#include <vector>
#include <string.h>
using namespace std;
using pii = pair<int, int>; // workTime, id

#define MAX_N 50005
#define Training 0
#define Broken 1
#define Working 2

struct Robot{
	int stime; // start_time 일을 시작한 시간
	int wtime; // working time = cTime - start_time
	int wid, state; // jid, state: training:0, broken:1, working:2
}robot[MAX_N]; 

// 우큐는 기본적으로 높은게 top으로 옴.
// IQ => MAX_WORKTIME - workTime (IQ 는 workTime 에 반비례)
priority_queue<pii> maxpq; // {-workTime, -id}높은게 위로 
priority_queue<pii, vector<pii>, greater<>> minpq; // {-workTime, id}낮은게 위로
vector<int> job[MAX_N];

int N, currentTime;
void init(int N) {
	::N = N, currentTime = 0;
	for (int i = 1; i < MAX_N; i++)
		job[i].clear();
	maxpq = {}, minpq = {};
	for (int i = 1; i <= N; i++)
		robot[i] = {}, maxpq.push({ 0,-i }), minpq.push({ 0,i });
}

int callJob(int cTime, int wID, int mNum, int mOpt) {
	int ret = 0, rid = 0, wt = 0, cnt = 0;
	while (cnt < mNum && !maxpq.empty() && !minpq.empty()) {
		if (mOpt == 0)
			rid = -maxpq.top().second, wt = -maxpq.top().first, maxpq.pop();
		if (mOpt == 1)
			rid = minpq.top().second, wt = -minpq.top().first, minpq.pop();
		if (robot[rid].state != Training) continue;
		if (robot[rid].wtime != wt) continue;
		robot[rid].stime = cTime;
		robot[rid].wid = wID;
		robot[rid].state = Working;
		job[wID].push_back(rid);
		ret += rid;
		cnt++;
	}
	return ret;
}

void returnJob(int cTime, int wID) {
	for (int rid: job[wID]) {
		if (robot[rid].wid != wID) continue;
		if (robot[rid].state == Working) {
			robot[rid].state = Training;
			robot[rid].wtime += (cTime - robot[rid].stime);
			minpq.push({ -robot[rid].wtime, rid });
			maxpq.push({ -robot[rid].wtime, -rid });
		}
	}
}

void broken(int cTime, int rID) {
	if (robot[rID].state == Working) robot[rID].state = Broken;
}

void repair(int cTime, int rID) {
	if (robot[rID].state == Broken) {
		robot[rID].state = Training;
		robot[rID].wtime = cTime;
		minpq.push({ -cTime, rID });
		maxpq.push({ -cTime, -rID });
	}
}

int check(int cTime, int rID) {
	if (robot[rID].state == Training) return cTime - robot[rID].wtime;
	else if (robot[rID].state == Broken) return 0;
	else return -robot[rID].wid;
}
```
