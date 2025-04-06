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
	int halfTime, cnt;
}m[MAX_BCNT];

struct Info {
	int life, cnt, currentTime;
	bool removed;
}info[MAXM];

struct Time {
	int order, id, life, cnt, time; // 반감기가 포함된 시간
	bool operator<(const Time& r)const {
		return time > r.time;
	}
};
priority_queue<Time> timeQ;
struct Life {
	int order, id, life, cnt, currentTime;
	bool operator<(const Life& r)const {
		if (life != r.life) return life > r.life;
		return currentTime > r.currentTime;
	}
};
priority_queue<Life> lifeQ;
unordered_map<string, int> idMap; // [bName] : int
int idCnt, order;

int getID(char c[]) {
	return idMap.count(c) ? idMap[c] : idMap[c] = idCnt++;
}

void init(int N, char bNameList[MAX_BCNT][MAX_NAME], int mHalfTime[MAX_BCNT]) {
	order = idCnt = 0, idMap.clear(), timeQ = {}, lifeQ = {};
	for (int i = 0; i < N; i++) m[getID(bNameList[i])] = { mHalfTime[i] };
	for (int i = 0; i < MAXM; i++) info[i] = {};
}

void update(int currentTime) {
	while(!timeQ.empty() && timeQ.top().time <= currentTime){
		Time cur = timeQ.top(); timeQ.pop();
		if (info[cur.order].removed) continue;
		if (info[cur.order].life != cur.life ||
			info[cur.order].cnt != cur.cnt) continue;
		int halfTime = m[cur.id].halfTime;
		int num = (currentTime - cur.time) / halfTime;
		for (int i = 0; i < num; i++) cur.life /= halfTime;
		info[cur.order].life = cur.life;
		if (cur.life <= 9) {
			info[cur.order].removed = true;	continue;
		}
		cur.time = cur.time + halfTime * num;
		timeQ.push(cur);
		lifeQ.push({ cur.order, cur.id, cur.life, info[cur.order].currentTime });
	}
}

void addBacteria(int tStamp, char bName[MAX_NAME], int mLife, int mCnt) { // 15,000
	update(tStamp);
	int id = getID(bName);
	m[id].cnt += mCnt;
	info[order] = { mLife, mCnt };
	// order, id, life, cnt, time; // 반감기가 포함된 시간
	timeQ.push({ order, id, mLife, mCnt, tStamp + m[id].halfTime });
	// order, id, life, cnt, currentTime;
	lifeQ.push({ order++, id, mLife, mCnt, tStamp });
}

int takeOut(int tStamp, int mCnt) { // 15,000
	int ret = 0;
	update(tStamp);
	while (!lifeQ.empty() && mCnt > 0) {
		Life cur = lifeQ.top(); lifeQ.pop();
		if (info[cur.order].removed) continue;
		if (info[cur.order].life != cur.life ||
			info[cur.order].cnt != cur.cnt) continue;
		if (mCnt > cur.cnt) {
			mCnt -= cur.cnt; // 10 5, 5 5, 5 10
			m[cur.id].cnt -= cur.cnt;
			ret += cur.life * cur.cnt;
			info[order].cnt -= cur.cnt; // 10 5, 5 5, 5 10
		}
		else if (mCnt == cur.cnt) {
			mCnt -= cur.cnt; // 10 5, 5 5, 5 10
			m[cur.id].cnt -= cur.cnt;
			ret += cur.life * cur.cnt;
			info[order].cnt -= cur.cnt; // 10 5, 5 5, 5 10
		}
		else{ // mCnt < cur.cnt
			mCnt -= cur.cnt; // 10 5, 5 5, 5 10
			m[cur.id].cnt -= mCnt;
			ret += mCnt * cur.cnt;
			info[order].cnt -= mCnt; // 10 5, 5 5, 5 10
		}
		//timeQ.push({ order, id, mLife, mCnt, tStamp + m[id].halfTime });
		timeQ.push({cur.order, cur.id, cur.cnt - mCnt, }); // order, id, time;
		lifeQ.push({ cur.order, cur.id, cur.cnt - mCnt, tStamp});// order, id, currentTime;
		return ret;
	}
}

int checkBacteria(int tStamp, char bName[MAX_NAME]) { // 50,000 - 15,000 - 15,000 - 1
	update(tStamp);
	int ret = microbe[getID(bName)].cnt;
	return ret;
}
#endif // 1
```
