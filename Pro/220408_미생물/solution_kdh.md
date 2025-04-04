```cpp
#if 1
#include <string>
#include <queue>
#include <unordered_map>
using namespace std;
#define MAX_BCNT 100
#define MAX_NAME 10
#define MAXM 20003

struct Microbe {
	int life, halfTime, cnt;
}microbe[MAXM];

struct Time {
	int order, id, time;
	bool operator<(const Time& r)const {
		return time > r.time;
	}
};
priority_queue<Time> timeQ;
struct Life {
	int order, id, currentTime;
	bool operator<(const Life& r)const {
		if (microbe[id] != r.life) return life > r.life;
		return currentTime > r.currentTime;
	}
};
priority_queue<Life> lifeQ;
unordered_map<string, int> idMap; // [bName] : int
int idCnt, order, Cnt[MAXM];
bool removed[MAXM];

int getID(char c[]) {
	return idMap.count(c) ? idMap[c] : idMap[c] = idCnt++;
}

void init(int N, char bNameList[MAX_BCNT][MAX_NAME], int mHalfTime[MAX_BCNT]) {
	order = idCnt = 0, idMap.clear(), timeQ = {}, lifeQ = {};
	for (int i = 0; i < N; i++) {
		int id = getID(bNameList[i]);
		microbe[id] = {mHalfTime[i]};
	}
	memset(removed, 0, sizeof(removed));
}

void update(int time) {
	while (!timeQ.empty() && timeQ.top().time <= time) {
		Time cur = timeQ.top(); timeQ.pop();
		if (cur.time <= 9) {
			microbe[cur.id].cnt -= cur.cnt;
			removed[cur.order] = true;
			continue;
		}
		cur.life /= 2;
		timeQ.push({ cur.order, cur.id, time + microbe[cur.id].halfTime, cur.life });
	}
}

void addBacteria(int tStamp, char bName[MAX_NAME], int mLife, int mCnt) { // 15,000
	update(tStamp);
	int id = getID(bName);
	microbe[id].cnt += mCnt;
	timeQ.push({ order, id, tStamp + microbe[id].halfTime, mLife }); // order, id, time, life, cnt;
	lifeQ.push({ order++, id, tStamp, mLife });// order, id, currentTime, life, cnt;
}

int takeOut(int tStamp, int mCnt) { // 15,000
	update(tStamp);
	while (!lifeQ.empty()) {
		Life cur = lifeQ.top(); lifeQ.pop();
		if (removed[cur.order]) continue;
		mCnt -= cur.cnt;
		if (mCnt >= 0) {
			m[cur.id].cnt -= cur.cnt;
			continue;
		}
		m[cur.id].cnt -= (cur.cnt - mCnt);
		cur.cnt = -mCnt; // mCnt < 0
		timeQ.push({ cur.order, cur.id, cur.time, cur.life, -mCnt });
		break;
	}
	return -1;
}

int checkBacteria(int tStamp, char bName[MAX_NAME]) { // 50,000 - 15,000 - 15,000 - 1
	update(tStamp);

	return -1;
}
#endif // 1

```
