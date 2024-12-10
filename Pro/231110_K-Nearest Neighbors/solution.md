```cpp
/*
TS KNN_ver1
- 0부터 id 부여
- 그룹별로 부여한 id 기록
- 부여한 id별로 (x,y,c) 기록
- 삭제시 해당되는 그룹만 탐색
*/
#include <vector>
#include <unordered_map>
#include <queue>
using namespace std;

#define MAX_SAMPLES 20000
#define MAX_TYPES 11
#define ADDED 0
#define REMOVED 1
#define N 100
#define MAX_BLOCKS 200

struct Sample {
	int x, y, cat, state;
	int dist(int x2, int y2) {
		return abs(x - x2) + abs(y - y2);
	}
}sample[MAX_SAMPLES];

struct Data{
	int x, y, c, dist;
	bool operator<(const Data& r)const {
		if (dist != r.dist) return dist > r.dist;
		if (x != r.x) return x > r.x;
		return y > r.y;
	}
};

unordered_map<int, int> idMap; // id, sid
int idCnt;
vector<int> sampleList[MAX_BLOCKS][MAX_BLOCKS];

int getID(int mid) {
	int id;
	auto it = idMap.find(mid);
	if (it == idMap.end()) {
		id = idCnt;
		idMap[mid] = idCnt++;
	}
	else id = idMap[mid];
	return id;
}

int K, L;
void init(int K, int L) {
	::K = K, ::L = L; idMap.clear(); idCnt = 1;
	for (int i = 0; i < MAX_SAMPLES; i++) sample[i] = {};
	for (int i = 0; i < MAX_BLOCKS; i++) for (int j = 0; j < MAX_BLOCKS; j++)
		sampleList[i][j].clear();
}

void addSample(int mID, int mX, int mY, int mC) {
	int id = getID(mID);
	sample[id] = { mX, mY, mC, ADDED };
	sampleList[(mX - 1) / N][(mY - 1) / N].push_back(id);
}

void deleteSample(int mID) {
	sample[getID(mID)].state = REMOVED;
}

int predict(int mX, int mY) {
	priority_queue<Data> pq;
	int sx = max((mX - L - 1) / N, 0), sy = max((mY - L - 1) / N, 0);
	int ex = min((mX + L + 1) / N, MAX_BLOCKS), ey = min((mY + L + 1) / N, MAX_BLOCKS);
	for(int i = sx; i <= ex; i++) for (int j = sy; j <= ey; j++)
		for (int id : sampleList[i][j]) {
			if (sample[id].state == REMOVED) continue;
			pq.push({ sample[id].x, sample[id].y, sample[id].cat, sample[id].dist(mX, mY) });
		}
	int cnt = 0, topk[MAX_TYPES];
	memset(topk, 0, sizeof(topk));
	while (!pq.empty() && cnt < K) {
		auto cur = pq.top(); pq.pop();
		if (cur.dist > L) return -1;
		topk[cur.c]++;
		cnt++;
	}
	int cat = 0, Cnt = 0;
	for (int i = 1; i < MAX_TYPES; i++)
		if (Cnt < topk[i]) Cnt = topk[i], cat = i;
	return (cat == 0) ? -1 : cat;
}
```
