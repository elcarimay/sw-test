```cpp
// 대기하는 순간에 기구가 동작하면 들어가야 하므로 대기하는 전순간까지 update하고 대기하는 순간에 update하는게 구현편함
// 기구가 새로 작동되는 순간에 capacity를 따로 변수로 두고 채워질때까지 채우고 시간을 업데이트 하는 방식으로 구현했음
// update함수 입력이 true면 특정 id만, false면 전체를 update하였음
#include <queue>
#include <vector>
#include <unordered_map>
using namespace std;

#define MAXN 103

struct Info {
	int startTime, endTime; // capacity는 탑승인원
};
unordered_map<int, Info> l;

struct DB {
	int mid, duration, capacity;
	void init(int id) {
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
		db[i] = { mId[i], mDuration[i], mCapacity[i] };
	}
}

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
			if (w[i].empty()) { // 대기가 없고 시간만 지나면 시작/끝 시간 0으로 초기화
				db[i].init(i); break;
			}
			Data tmp;
			int capa = db[i].capacity;
			while (true) { // 대기열이 있을때
				Data cur = w[i].top(); w[i].pop();
				waitTotal[i] -= cur.waitNum;
				// 비워있는 자리를 채우는데 다 못채우면 채울때까지 넣음
				if (capa <= cur.waitNum) // 빈자리가 대기인원보다 같거나 작을때
					cur.waitNum -= capa, capa = 0;
				else // capa > cur.waitNum
					capa -= cur.waitNum, cur.waitNum = 0;
				if (w[i].empty() || capa == 0) { // 채울 대기가 없고 빈자리가 없을때
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
	priority_queue<Rank> pq;
	for (int i = 0; i < mCnt; i++) pq.push({ i, waitTotal[i] });
	for (int i = 0; i < mCount; i++) {
		id = pq.top().id; pq.pop();
		mId[i] = db[id].mid, mWait[i] = waitTotal[id];
	}
}
```
