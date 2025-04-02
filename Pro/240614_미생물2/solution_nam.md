```cpp
#include <queue>
using namespace std;

#define MAX_N 1000001

struct Bacteria {
	int mLifeSpan, mHalfTime;
}bacteria[30001];

int bacteriaCnt;

struct TimeData
{
	int mHalfTime, mID;
	bool operator<(const TimeData& r)const {
		return mHalfTime > r.mHalfTime;
	}
};

priority_queue<TimeData> timePQ;

struct LifeData
{
	int mLifeSpan, mID;
	bool operator<(const LifeData& r)const {
		return (mLifeSpan > r.mLifeSpan) ||
			(mLifeSpan == r.mLifeSpan && mID > r.mID);
	}
};

priority_queue<LifeData> lifePQ;

int currentTime;

struct SqrtDecomp
{
	int arr[MAX_N], buckets[MAX_N], bSize, bCnt;
	void clear() {
		bSize = sqrt(MAX_N);
		memset(arr, 0, sizeof(arr));
		memset(buckets, 0, sizeof(buckets));
	}
	void update(int idx, int value) {
		arr[idx] += value;
		buckets[idx / bSize] += value;
	}
	int query(int left, int right) {
		int res = 0;
		int s = left / bSize, e = right / bSize;
		if (s == e) {
			for (int i = left; i <= right; i++) res += arr[i];
			return res;
		}
		for (int i = left; i < (s + 1) * bSize; i++) res += arr[i];
		for (int i = s + 1; i <= e - 1; i++) res += buckets[i];
		for (int i = e * bSize; i <= right; i++) res += arr[i];
		return res;
	}
}S;

void update(int currentTime) {
	while (!timePQ.empty() && timePQ.top().mHalfTime == currentTime) {
		int mID = timePQ.top().mID; timePQ.pop();
		S.update(bacteria[mID].mLifeSpan, -1);
		bacteria[mID].mLifeSpan /= 2;
		S.update(bacteria[mID].mLifeSpan, +1);
		if (bacteria[mID].mLifeSpan < 100) {
			S.update(bacteria[mID].mLifeSpan, -1);
			continue;
		}
		timePQ.push({ currentTime + bacteria[mID].mHalfTime, mID });
		lifePQ.push({ bacteria[mID].mLifeSpan, mID });
	}
}

void init() {
	currentTime = bacteriaCnt = 0;
	timePQ = {}; lifePQ = {};
	S.clear();
}

void addBacteria(int tStamp, int mID, int mLifeSpan, int mHalfTime) {
	while (currentTime < tStamp) update(++currentTime);
	bacteriaCnt = mID;
	bacteria[mID].mLifeSpan = mLifeSpan;
	bacteria[mID].mHalfTime = mHalfTime;
	timePQ.push({ currentTime + mHalfTime, mID });
	lifePQ.push({ mLifeSpan, mID });
	S.update(mLifeSpan, 1);
}

int getMinLifeSpan(int tStamp) {
	while(currentTime < tStamp) update(++currentTime);

	int res = -1;
	while (!lifePQ.empty()) {
		auto cur = lifePQ.top(); lifePQ.pop();
		int mID = cur.mID;
		if (cur.mLifeSpan < 100) continue;
		if (cur.mLifeSpan != bacteria[mID].mLifeSpan) continue;
		res = mID;
		lifePQ.push(cur);
		break;
	}
	return res;
}

int getCount(int tStamp, int mMinSpan, int mMaxSpan) {
	while (currentTime < tStamp) update(++currentTime);
	int res = S.query(mMinSpan, mMaxSpan);
	return res;
}
```
