```cpp
#if 1 // 구현난해함
// 대기하는 순간에 기구가 동작하면 들어가야 하므로 대기하는 전순간까지 update하고 대기하는 순간에 update하는게 구현편함
#include <queue>
#include <vector>
#include <unordered_map>
#include <map>
using namespace std;

#define MAXN 103

struct Info {
	int startTime, endTime; // capacity는 탑승인원
};
unordered_map<int, Info> l;

struct DB {
	int mid, duration, capacity, restNum;
	void init(int id) {
		restNum = capacity;
		l[id].startTime = l[id].endTime = 0;
	}
}db[MAXN];

unordered_map<int, int> m;
int mCnt;
struct Data {
	int waitNum, priority;
	bool operator<(const Data& r)const {
		return priority < r.priority;
	}
};
priority_queue<Data> w[MAXN];

int waitTotal[MAXN];

int getID(int mid) {
	return m.count(mid) ? m[mid] : m[mid] = mCnt++;
}

int N, id;
void init(int N, int mId[], int mDuration[], int mCapacity[]) {
	::N = N, mCnt = 0, m.clear(), l.clear();
	for (int i = 0; i < N; i++) {
		i = getID(mId[i]), waitTotal[i] = 0, w[i] = {};
		db[i] = { mId[i], mDuration[i], mCapacity[i], mCapacity[i] };
	}
}

// 시간대별로 들어갈수 있는지 판별해야 하는 로직을 아직 구현못했음
void update(int time, bool opt) { // 대기열 update opt:true면 특정 ID만, false면 전체
	if (time == 28) {
		time = time;
	}
	int sid, eid; // start id, end id
	if (opt) sid = id, eid = id + 1;
	else sid = 0, eid = N;
	for (int i = sid; i < eid; i++) {
		while (l[i].endTime <= time) { // 설비의 끝나는 시간이 현재시간보다 같거나 작을때만 작동
			bool flag = true;
			if (w[i].empty()) {
				db[i].init(i); break;
			}
			Data tmp;
			while (true) { // 대기열이 있을때
				Data cur = w[i].top(); w[i].pop();
				waitTotal[i] -= cur.waitNum;
				// 비워있는 자리를 채우는데 다 못채우면 채울때까지 넣음
				if (db[i].restNum == 0) {
					cur.waitNum -= db[i].capacity;
				}
				else if (db[i].restNum == cur.waitNum) // 빈자리와 대기인원이 같을때
					db[i].restNum = cur.waitNum = 0;
				else if (db[i].restNum > cur.waitNum)
					db[i].restNum -= cur.waitNum, cur.waitNum = 0;
				else if (db[i].restNum < cur.waitNum)  // 빈자리보다 대기인원이 많을때
					cur.waitNum -= db[i].restNum, db[i].restNum = 0;
				if (w[i].empty() || db[i].restNum == 0) { // 대기가 없고 빈자리가 없을때
					tmp = cur; break;
				}
			}
			l[i].startTime = l[i].endTime == 0 ? time : l[i].endTime;
			l[i].endTime = l[i].startTime + db[i].duration;
			if (tmp.waitNum)
				w[i].push({ tmp.waitNum, tmp.priority }), waitTotal[i] += tmp.waitNum;
		}
	}
}

int add(int tStamp, int mId, int mNum, int mPriority) {
	id = getID(mId);
	update(tStamp - 1, true);
	w[id].push({ mNum, mPriority }), waitTotal[id] += mNum;
	update(tStamp, true);
	return w[id].empty() ? 0 : w[id].top().priority;
}

struct Rank {
	int id, TotalwaitNum;
	bool operator<(const Rank& r)const {
		if (TotalwaitNum != r.TotalwaitNum) return TotalwaitNum < r.TotalwaitNum;
		return db[id].mid < db[r.id].mid;
	}
};

void search(int tStamp, int mCount, int mId[], int mWait[]) {
	update(tStamp, false);
	int cnt = 0;
	priority_queue<Rank> pq;
	// 저장되어 있는 	waitTotal에서 불러오기
	for (int i = 0; i < mCnt; i++) pq.push({ i, waitTotal[i] });
	for (int i = 0; i < mCount; i++) {
		Rank cur = pq.top(); pq.pop();
		mId[i] = db[cur.id].mid, mWait[i] = waitTotal[cur.id];
	}
}
#endif // 1

```
