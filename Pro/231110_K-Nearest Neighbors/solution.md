```cpp
#include <vector>
#include <unordered_map>
#include <queue>
using namespace std;

#define MAX_SAMPLES 20000
#define MAX_TYPES 11
#define MAX_SIZE 4001

#define ADDED 0
#define REMOVED 1

#define N 100
#define MAX_BUCKETS 200

struct Sample
{
	int mX, mY, mC, state;
	int dist(int mX2, int mY2) {
		return abs(mX - mX2) + abs(mY - mY2);
	}
};

struct Data
{
	int dist, mX, mY, sIdx;
	bool operator<(const Data& data) const {
		return (dist > data.dist) || (dist == data.dist && mX > data.mX)
			|| (dist == data.dist && mX == data.mX && mY > data.mY);
	}
};

Sample samples[MAX_SAMPLES];
unordered_map<int, int> sampleMap;
int sampleCnt;
vector<int> sampleList[MAX_BUCKETS][MAX_BUCKETS];
int K, L;

void init(int _K, int _L){
	K = _K; L = _L;
	for (int i = 0; i < MAX_SAMPLES; i++) samples[i] = {};
	sampleCnt = 0; sampleMap.clear();
	for (int i = 0; i < MAX_BUCKETS; i++)
		for (int j = 0; j < MAX_BUCKETS; j++)
			sampleList[i][j].clear();
}

int get_sampleIdx(int mID) {
	int sIdx;
	auto cur = sampleMap.find(mID);
	if (cur == sampleMap.end()) {
		sIdx = sampleCnt++;
		sampleMap.insert({ mID, sIdx });
	}
	else sIdx = cur->second;
	return sIdx;
}

// addSample() 함수의 호출 횟수는 20,000 이하이다.
void addSample(int mID, int mX, int mY, int mC){
	int sIdx = get_sampleIdx(mID);
	samples[sIdx] = { mX, mY, mC,ADDED };
	sampleList[(mX - 1) / N][(mY - 1) / N].push_back(sIdx);
}

// deleteSample() 함수의 호출 횟수는 1,000 이하이다.
void deleteSample(int mID){
	int sIdx = get_sampleIdx(mID);
	samples[sIdx].state = REMOVED;
}

 //predict() 함수의 호출 횟수는 10,000 이하이다.
int predict(int mX, int mY){
	priority_queue<Data> Q;
	int spX = max((mX - 1 - L) / N, 0);
	int spY = max((mY - 1 - L) / N, 0);
	int epX = min((mX - 1 + L) / N, MAX_BUCKETS - 1);
	int epY = min((mY - 1 + L) / N, MAX_BUCKETS - 1);

	for (int i = spX; i <= epX; i++)
	{
		for (int j = spY; j <= epY; j++)
		{
			for (int sIdx:sampleList[i][j])
			{
				if (samples[sIdx].state == REMOVED) continue;
				Q.push({ samples[sIdx].dist(mX, mY), samples[sIdx].mX, samples[sIdx].mY, sIdx });
			}
		}
	}
	int cnt = 0;
	int topk[MAX_TYPES] = {};
	while (!Q.empty() && cnt < K) {
		auto data = Q.top(); Q.pop();
		if (data.dist > L) return -1;
		topk[samples[data.sIdx].mC]++;
		cnt++;
	}
	int res = 1;
	for (int mC = 2; mC < MAX_TYPES; mC++)
	{
		if (topk[mC] > topk[res]) res = mC;
		else if (topk[mC] == topk[res] && mC < res) res = mC;
	}
	return res;
}

```
