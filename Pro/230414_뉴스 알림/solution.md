```cpp
#if 1
#include <vector>
#include <queue>
#include <unordered_map>
using namespace std;

#define MAXN 30000
#define MAXU 500
#define MAXC 500

struct News {
	int mNewsID, cid, alarm_time;
	bool removed;
	bool operator<(const News& r)const {
		return alarm_time > r.alarm_time;
	}
}news[MAXN];
priority_queue<News> pq;
vector<int> users[MAXU], channels[MAXC];
unordered_map<int, int> nMap, uMap, cMap;
int N, K;

int getID(unordered_map<int, int>& m, int c) {
	return m.count(c) ? m[c] : m[c] = m.size();
}

void init(int N, int K) {
	::N = N, ::K = K, pq = {}, nMap.clear(), uMap.clear(), cMap.clear();
	for (int i = 0; i < MAXN; i++) news[i] = {};
	for (int i = 0; i < MAXU; i++) users[i] = {}, channels[i] = {};
}

void update(int time) {
	while (!pq.empty() && pq.top().alarm_time <= time) {
		auto cur = pq.top(); pq.pop();
		int nid = getID(nMap, cur.mNewsID);
		if (news[nid].alarm_time != cur.alarm_time) continue;
		if (!news[nid].removed)
			for (auto uid : channels[news[nid].cid]) users[uid].push_back(nid);
	}
}

void registerUser(int mTime, int mUID, int mNum, int mChannelIDs[]) {
	update(mTime);
	int uid = getID(uMap, mUID);
	for (int i = 0; i < mNum; i++)
		channels[getID(cMap, mChannelIDs[i])].push_back(uid);
}

int offerNews(int mTime, int mNewsID, int mDelay, int mChannelID) {
	update(mTime);
	int nid = getID(nMap, mNewsID), cid = getID(cMap, mChannelID);
	news[nid] = {mNewsID, cid, mTime + mDelay};
	pq.push(news[nid]);
	return (int)channels[cid].size();
}

void cancelNews(int mTime, int mNewsID) {
	update(mTime);
	int nid = getID(nMap, mNewsID);
	news[nid].removed = true;
}

struct Data {
	int mNewsID, alarm_time;
	bool operator<(const Data& r)const {
		return alarm_time == r.alarm_time ? mNewsID < r.mNewsID: alarm_time < r.alarm_time;
	}
};

int checkUser(int mTime, int mUID, int mRetIDs[]) {
	update(mTime);
	int uid = getID(uMap, mUID), ret = 0;
	priority_queue<Data> Q;
	for (int nid : users[uid])
		if (!news[nid].removed) Q.push({ news[nid].mNewsID, news[nid].alarm_time });
	ret = Q.size();
	int cnt = 0;
	while (!Q.empty() && cnt < 3) {
		Data cur = Q.top(); Q.pop();
		mRetIDs[cnt++] = cur.mNewsID;
	}
	users[uid].clear();
	return ret;
}
#endif // 1
```
