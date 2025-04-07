```cpp
#if 1
#define MAX_BCNT 100
#define MAX_NAME 10
#define MAXB 15000
#include <unordered_map>
#include <vector>
#include <queue>
#include <iostream>
#include <algorithm>
using namespace std;

struct Bacteria {
	int halfTime, cnt;
}bacteria[MAXB];

unordered_map<string, int> idmap;
int idmapCnt;

struct Sample {
	int bIdx, cnt, life, addTime;
}samples[MAXB];
int sIdx;

int cTime, totalCnt;

struct TimeData { // 반감기가 왔을때 갯수 축소를 위해 우선순위 정렬 필요
	int halfTime, sIdx;
	bool operator<(const TimeData& r)const {
		return halfTime > r.halfTime;
	}
};
priority_queue<TimeData> timePQ;

struct LifeData { // takeout시 생명력이 가장 작은 순서 정렬 필요
	int life, addTime, sIdx;
	bool operator<(const LifeData& r)const {
		if (life != r.life) return life > r.life;
		return addTime > r.addTime;
	}
};
priority_queue<LifeData> lifePQ;

void update(int cTime) {
	while (!timePQ.empty() && timePQ.top().halfTime == cTime) {
		int sIdx = timePQ.top().sIdx; timePQ.pop();
		auto& s = samples[sIdx];
		s.life /= 2;
		int bIdx = s.bIdx;
		if (s.life < 10) {
			bacteria[bIdx].cnt -= s.cnt;
			s.cnt = 0;
			totalCnt -= s.cnt;
		}
		else {
			timePQ.push({ cTime + bacteria[bIdx].halfTime, sIdx });
			lifePQ.push({ s.life, s.addTime, sIdx });
		}
	}
}

int getid(char bName[]) {
	int id;
	auto it = idmap.find(bName);
	if (it == idmap.end()) {
		id = idmapCnt++;
		idmap[bName] = id;
	}
	else id = it->second;
	return id;
}

void init(int N, char bNameList[MAX_BCNT][MAX_NAME], int mHalfTime[MAX_BCNT]) {
	cTime = totalCnt = sIdx = idmapCnt = 0;
	idmap.clear(); timePQ = {}; lifePQ = {};
	for (int i = 0; i < N; i++) {
		int bIdx = getid(bNameList[i]);
		bacteria[bIdx] = { mHalfTime[i], 0 };
	}
}

void addBacteria(int tStamp, char bName[MAX_NAME], int mLife, int mCnt) {
	while (cTime < tStamp) update(++cTime);
	int bIdx = getid(bName);
	samples[sIdx] = { bIdx, mCnt, mLife, tStamp };
	bacteria[bIdx].cnt += mCnt;
	totalCnt += mCnt;
	timePQ.push({ cTime + bacteria[bIdx].halfTime, sIdx });
	lifePQ.push({ mLife, tStamp, sIdx++ });
}

int takeOut(int tStamp, int mCnt) {
	while (cTime < tStamp) update(++cTime);
	int takeOutCnt = min(totalCnt, mCnt);
	int res = 0;
	while (!lifePQ.empty() && takeOutCnt > 0) {
		auto cur = lifePQ.top(); lifePQ.pop();
		int sIdx = cur.sIdx;
		auto& s = samples[sIdx];
		if (s.life != cur.life) continue;
		int cnt = min(s.cnt, takeOutCnt);
		takeOutCnt -= cnt;
		s.cnt -= cnt;
		int bIdx = s.bIdx;
		bacteria[bIdx].cnt -= cnt;
		res += s.life * cnt;
		if (s.cnt > 0) lifePQ.push({ s.life, s.addTime, sIdx });
	}
	return res;
}

int checkBacteria(int tStamp, char bName[MAX_NAME]) {
	while (cTime < tStamp) update(++cTime);
	int bIdx = getid(bName);
	return bacteria[bIdx].cnt;
}

#endif // 1
```
