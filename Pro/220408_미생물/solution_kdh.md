```cpp
#if 1
#include <string>
#include <queue>
#include <unordered_map>
using namespace std;
#define MAX_BCNT 100
#define MAX_NAME 10
#define MAXM 15003

struct Microbe {
	int halfTime, cnt;
}m[MAX_BCNT];

struct Info {
	int life, cnt, currentTime, time; // time: 기존시간 + halfTime
}info[MAXM];

struct Time {
	int infoId, id, time; // 반감기가 포함된 시간
	bool operator<(const Time& r)const {
		return time > r.time;
	}
};
priority_queue<Time> timeQ;
struct Life {
	int infoId, id, life, cnt;
	bool operator<(const Life& r)const {
		if (life != r.life) return life > r.life;
		return info[infoId].currentTime > info[r.infoId].currentTime;
	}
};
priority_queue<Life> lifeQ;
unordered_map<string, int> idMap; // [bName] : int
int idCnt, infoId;
int getID(char c[]) {
	return idMap.count(c) ? idMap[c] : idMap[c] = idCnt++;
}

void init(int N, char bNameList[MAX_BCNT][MAX_NAME], int mHalfTime[MAX_BCNT]) {
	infoId = idCnt = 0, idMap.clear(), timeQ = {}, lifeQ = {};
	for (int i = 0; i < N; i++) m[getID(bNameList[i])] = { mHalfTime[i] };
}

void update(int currentTime) {
	while (!timeQ.empty() && timeQ.top().time <= currentTime) {
		Time cur = timeQ.top(); timeQ.pop();
		if (cur.time != info[cur.infoId].time) continue;
		int halfTime = m[cur.id].halfTime;
		int num = (currentTime - cur.time) / halfTime;
		for (int i = 0; i <= num; i++) info[cur.infoId].life /= 2;
		if (info[cur.infoId].life <= 9) {
			m[cur.id].cnt -= info[cur.infoId].cnt;
			continue;
		}
		info[cur.infoId].time = cur.time = cur.time + halfTime * (num + 1);
		timeQ.push(cur);
		lifeQ.push({ cur.infoId, cur.id, info[cur.infoId].life, info[cur.infoId].cnt }); //infoId, id, life, cnt;
	}
}

void addBacteria(int tStamp, char bName[MAX_NAME], int mLife, int mCnt) { // 15,000
	update(tStamp);
	int id = getID(bName);
	m[id].cnt += mCnt;
	info[infoId] = { mLife, mCnt, tStamp, tStamp + m[id].halfTime };
	timeQ.push({ infoId, id, tStamp + m[id].halfTime }); // infoId, id, time;
	lifeQ.push({ infoId++, id, mLife, mCnt }); // infoId, id, life, cnt;
}

int takeOut(int tStamp, int mCnt) { // 15,000
	int ret = 0, minv;
	update(tStamp);
	while (!lifeQ.empty() && mCnt > 0) {
		Life cur = lifeQ.top(); lifeQ.pop();
		if (info[cur.infoId].life != cur.life || info[cur.infoId].cnt != cur.cnt) continue;
		if (mCnt >= cur.cnt) mCnt -= cur.cnt, minv = cur.cnt;
		else cur.cnt -= mCnt, minv = mCnt, mCnt = 0;
		m[cur.id].cnt -= minv;
		ret += cur.life * minv;
		info[cur.infoId].cnt -= minv;
		if (mCnt <= 0) cur.cnt = info[cur.infoId].cnt, lifeQ.push(cur);
	}
	return ret;
}

int checkBacteria(int tStamp, char bName[MAX_NAME]) { // 50,000 - 15,000 - 15,000 - 1
	update(tStamp);
	return m[getID(bName)].cnt;
}
#endif // 1  
```
