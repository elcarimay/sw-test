```cpp
#if 1
#include <vector>
#include <unordered_map>
#include <queue>
using namespace std;

#define NUM_NEWS 30000
#define NUM_USERS 500
#define NUM_CHANNELS 500
#define CANCELED 1

struct News {
	int newsID, channelIdx, alarm_time, state;
	bool operator<(const News& news)const {
		return alarm_time > news.alarm_time;
	}
};

News news[NUM_NEWS];
vector<int> users[NUM_USERS]; // newsList
vector<int> channels[NUM_CHANNELS]; // userList

unordered_map<int, int> newsMap, userMap, channelMap;
int newsCnt, userCnt, channelCnt;

struct NewsData
{
	int newsID, alarm_time;
	bool operator<(const NewsData& newsData)const {
		return (alarm_time < newsData.alarm_time) ||
			(alarm_time == newsData.alarm_time && newsID < newsData.newsID);
	}
};

priority_queue<News> newsPQ;

int get_newsIndex(int mNewsID) {
	int nIdx;
	auto iter = newsMap.find(mNewsID);
	if (iter == newsMap.end()) {
		nIdx = newsCnt++;
		newsMap[mNewsID] = nIdx;
	}
	else  nIdx = iter->second;
	return nIdx;
}

int get_userIndex(int mUID) {
	int uIdx;
	auto iter = userMap.find(mUID);
	if (iter == userMap.end()) {
		uIdx = userCnt++;
		userMap[mUID] = uIdx;
	}
	else  uIdx = iter->second;
	return uIdx;
}

int get_channelIndx(int mchannelID) {
	int cIdx;
	auto iter = channelMap.find(mchannelID);
	if (iter == channelMap.end()) {
		cIdx = channelCnt++;
		channelMap[mchannelID] = cIdx;
	}
	else cIdx = iter->second;
	return cIdx;
}

void init(int N, int K) {
	newsMap.clear(); userMap.clear(); channelMap.clear();
	newsCnt = userCnt = channelCnt = 0;
	for (int i = 0; i < NUM_NEWS; i++) news[i] = {};
	for (int i = 0; i < NUM_USERS; i++) users[i] = {};
	for (int i = 0; i < NUM_CHANNELS; i++) channels[i] = {};
	while (!newsPQ.empty()) newsPQ.pop();
}

void update_news(int mTime) {
	auto& Q = newsPQ;
	while (!Q.empty() && Q.top().alarm_time <= mTime) {
		auto cur = Q.top(); Q.pop();
		int nIdx = get_newsIndex(cur.newsID);
		if (news[nIdx].alarm_time != cur.alarm_time) continue;
		if (news[nIdx].state != CANCELED) {
			int cIdx = news[nIdx].channelIdx;
			for (auto uIdx : channels[cIdx])
				users[uIdx].push_back(nIdx);
		}
	}
}

void registerUser(int mTime, int mUID, int mNum, int mChannelIDs[]) {
	update_news(mTime);
	int uIdx = get_userIndex(mUID);
	for (int i = 0; i < mNum; i++) {
		int cIdx = get_channelIndx(mChannelIDs[i]);
		channels[cIdx].push_back(uIdx);
	}
}

int offerNews(int mTime, int mNewsID, int mDelay, int mChannelID) {
	update_news(mTime);
	int nIdx = get_newsIndex(mNewsID);
	int cIdx = get_channelIndx(mChannelID);

	news[nIdx] = { mNewsID, cIdx, mTime + mDelay };
	newsPQ.push(news[nIdx]);
	return channels[cIdx].size();
}

void cancelNews(int mTime, int mNewsID) {
	update_news(mTime);
	int nIdx = get_newsIndex(mNewsID);
	news[nIdx].state = CANCELED;
}

int checkUser(int mTime, int mUID, int mRetIDs[]) {
	update_news(mTime);
	int res = 0;
	int uIdx = get_userIndex(mUID);
	priority_queue<NewsData> Q;
	for (int nIdx : users[uIdx])
		if (news[nIdx].state != CANCELED)
			Q.push({ news[nIdx].newsID, news[nIdx].alarm_time });
	res = Q.size();
	int cnt = 0;
	while (!Q.empty() && cnt < 3) {
		auto cur = Q.top(); Q.pop();
		mRetIDs[cnt++] = cur.newsID;
	}
	users[uIdx].clear();
	return res;
}
#endif
```
