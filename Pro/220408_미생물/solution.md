```cpp
#include <string>
#include <unordered_map>
#include <queue>
using namespace std;

#define MAX_BCNT 100
#define MAX_NAME 10
#define MAX_ADD 15000

struct Bacteria
{
	int mHalfTime, mCnt;
}bacteria[MAX_BCNT];
int bacCnt;
unordered_map<string, int> bacMap;

struct Sample
{
	int bIdx, mCnt, mLife, addTime;
}samples[MAX_ADD];
int sIdx;

int curTime, totalCnt;

struct TimeData {
	int mHalfTime, sIdx;
	bool operator<(const TimeData& r)const {
		return mHalfTime > r.mHalfTime;
	}
};
priority_queue<TimeData> timePQ;

struct LifeData {
	int mLife, addTime, sIdx;
	bool operator<(const LifeData& r)const {
		return (mLife > r.mLife) ||
			(mLife == r.mLife && addTime > r.addTime);
	}
};
priority_queue<LifeData> lifePQ;

void update(int curTime) {
	while (!timePQ.empty() && timePQ.top().mHalfTime == curTime) {
		int sIdx = timePQ.top().sIdx; timePQ.pop();
		auto& s = samples[sIdx];
		s.mLife /= 2;
		
		int bIdx = s.bIdx;
		if (s.mLife < 10) {
			bacteria[bIdx].mCnt -= s.mCnt;
			s.mCnt = 0;
			totalCnt -= s.mCnt;
		}
		else {
			timePQ.push({ curTime + bacteria[bIdx].mHalfTime, sIdx });
			lifePQ.push({ s.mLife, s.addTime, sIdx });
		}
	}
}

int get_bacIndex(const char bName[]) {
	int bIdx;
	auto iter = bacMap.find(bName);
	if (iter == bacMap.end()) {
		bIdx = bacCnt++;
		bacMap.insert({ bName, bIdx });
	}
	else bIdx = iter->second;
	return bIdx;
}

void init(int N, char bNameList[MAX_BCNT][MAX_NAME], int mHalfTime[MAX_BCNT]){
	curTime = totalCnt = sIdx = bacCnt = 0;
	bacMap.clear();	timePQ = {}; lifePQ = {};
	for (int i = 0; i < N; i++){
		int bIdx = get_bacIndex(bNameList[i]);
		bacteria[bIdx] = { mHalfTime[i], 0 };
	}
}

void addBacteria(int tStamp, char bName[MAX_NAME], int mLife, int mCnt){
	while (curTime < tStamp) update(++curTime);
	int bIdx = get_bacIndex(bName);
	samples[sIdx] = { bIdx, mCnt, mLife, tStamp };
	bacteria[bIdx].mCnt += mCnt;
	totalCnt += mCnt;
	timePQ.push({ curTime + bacteria[bIdx].mHalfTime, sIdx });
	lifePQ.push({ mLife, tStamp, sIdx++ });
}

int takeOut(int tStamp, int mCnt) {
	while (curTime < tStamp) update(++curTime);

	int takeOutCnt = min(totalCnt, mCnt);
	int res = 0;
	while (!lifePQ.empty() && takeOutCnt > 0) {
		auto cur = lifePQ.top(); lifePQ.pop();
		int sIdx = cur.sIdx; auto& s = samples[sIdx];
		if (s.mLife != cur.mLife) continue;

		int cnt = min(s.mCnt, takeOutCnt);
		takeOutCnt -= cnt;
		s.mCnt -= cnt;
		int bIdx = s.bIdx;
		bacteria[bIdx].mCnt -= cnt;
		res += s.mLife * cnt;
		if (s.mCnt > 0)
			lifePQ.push({ s.mLife, s.addTime, sIdx });
	}
	return res;
}

int checkBacteria(int tStamp, char bName[MAX_NAME]){
	while (curTime < tStamp) update(++curTime);
	int bIdx = get_bacIndex(bName);
	return bacteria[bIdx].mCnt;
}

```
