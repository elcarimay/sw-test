```cpp
#if 1
#include <vector>
#include <unordered_map>
#include <unordered_set>
#include <algorithm>
#include <queue>
using namespace std;

#define MAXN 1003
#define MAX_ONEFILE		50
#define INF 1e6

struct Down {
	int currentTime, endTime;
	unordered_map<int, vector<int>> path;
};

unordered_map<int, int> getList[MAXN]; // [com] fid, size
unordered_map<int, Down> downList[MAXN]; // [com] fid, struct Down
unordered_map<int, int> fileCapa;
unordered_map<int, pair<int, int>> link;

struct Edge {
	int to, cost, mid;
	bool operator<(const Edge& r)const {
		return cost > r.cost;
	}
};
vector<Edge> adj[MAXN];

int N;
void init(int N, int mFileCnt[], int mFileID[][MAX_ONEFILE], int mFileSize[][MAX_ONEFILE]) {
	::N = N, fileCapa.clear();
	for (int i = 1; i <= N; i++) {
		for (int j = 0; j < mFileCnt[i - 1]; j++) {
			getList[i][mFileID[i - 1][j]] = mFileSize[i - 1][j];
			fileCapa[mFileID[i - 1][j]] = mFileSize[i - 1][j];
		}
	}
}

void makeNet(int K, int mID[], int mComA[], int mComB[], int mDis[]) {
	for (int i = 0; i < K; i++) {
		link[mID[i]] = { mComA[i], mComB[i] };
		adj[mComA[i]].push_back({ mComB[i], mDis[i], mID[i] });
		adj[mComB[i]].push_back({ mComA[i], mDis[i], mID[i] });
	}
}

//struct Down {
//	int currentTime, endTime;
//	unordered_map<int, vector<int>> path; // [com] mids
//};
//unordered_map<int, int> getList[MAXN]; // [com] fid, size
//unordered_map<int, Down> downList[MAXN]; // [com] fid, struct Down
void update(int time) {
	for (int i = 0; i < N; i++) {
		if (downList[i].empty()) continue;
		for (auto it = downList[i].begin(); it != downList[i].end();) { // fid, currentTime, endTime
			int fid = it->first, currentTime = it->second.currentTime, endTime = it->second.endTime, dlCnt = it->second.path.size(), dT;
			it->second.currentTime = time;
			if (currentTime < time && time < endTime) {
				dT = time - currentTime;
				for (auto nx : it->second.path) {
					getList[nx.first][fid] -= (dlCnt * 9) * dT;
					if (getList[nx.first][fid] <= 0) getList[nx.first].erase(fid);
				}
				it++;
			}
			else if (endTime <= time) {
				dT = endTime - currentTime;
				for (auto nx : it->second.path) {
					getList[nx.first][fid] -= (dlCnt * 9) * dT;
					if (getList[nx.first][fid] <= 0) getList[nx.first].erase(fid);
				}
				it = downList[i].erase(it);
			}
			getList[i][fid] += dlCnt * 9 * dT;
			getList[i][fid] = min(getList[i][fid], fileCapa[fid]);

		}
	}
}

struct Route {
	int prev, mid;
}route[MAXN];

int cost[MAXN];
void dijkstra(int s) {
	for (int i = 1; i <= N; i++) cost[i] = INF;
	priority_queue<Edge> pq;
	pq.push({ s, cost[s] = 0 });
	while (!pq.empty()) {
		Edge cur = pq.top(); pq.pop();
		if (cur.cost > cost[cur.to]) continue;
		for (Edge nx : adj[cur.to]) {
			int nextCost = cost[cur.to] + nx.cost;
			if (cost[nx.to] > nextCost)
				route[nx.to] = { cur.to, nx.mid }, pq.push({ nx.to, cost[nx.to] = nextCost });
		}
	}
}

void linkErase(int mID) {
	int from = link[mID].first, to = link[mID].second;
	auto& a = adj[from];
	for (int i = 0; i < a.size(); i++) if (a[i].to == to) a.erase(a.begin() + i);
	auto& b = adj[to];
	for (int i = 0; i < b.size(); i++) if (b[i].to == from) b.erase(b.begin() + i);
	link.erase(mID);
}

//struct Down {
//	int currentTime, endTime;
//	unordered_map<int, vector<int>> path; // [com] mids
//};
//unordered_map<int, int> getList[MAXN]; // [com] fid, size
//unordered_map<int, Down> downList[MAXN]; // [com] fid, struct Down
void removeLink(int mTime, int mID) {
	update(mTime);
	for (int i = 1; i <= N; i++) {
		if (downList[i].empty()) continue;
		for (unordered_map<int, Down>::iterator it = downList[i].begin(); it != downList[i].end(); it++) { // fid, currentTime, endTime, dlCnt
			for (unordered_map<int, vector<int>>::iterator nx = it->second.path.begin(); nx != it->second.path.end();) {
				bool removed = false;
				for (vector<int>::iterator it2 = nx->second.begin(); it2 != nx->second.end();it2++) {
					if (*it2 == mID) {
						nx = it->second.path.erase(nx); removed = true; break;
					}
				}
				if (!removed) nx++;
			}
		}
	}
	linkErase(mID);
}

bool isPossible(int mFileID) {
	for (int i = 1;i <= N;i++) if (getList[i].count(mFileID))
		return true;
	return false;
}

//struct Down {
//	int currentTime, endTime;
//	unordered_map<int, vector<int>> path; // [other com] mids
//};
//unordered_map<int, int> getList[MAXN]; // [com] fid, size
//unordered_map<int, Down> downList[MAXN]; // [com] fid, struct Down
int downloadFile(int mTime, int mComA, int mFileID) {
	if (mTime == 520) {
		mTime = mTime;
	}
	update(mTime);
	if (!isPossible(mFileID)) return 0;
	dijkstra(mComA);
	int size = 0;
	for (int i = 1;i <= N;i++) if (getList[i].count(mFileID)) {
		for (int com : fileLocation[mFileID]) {
			if (downList[com].count(mFileID)) continue;
			if (cost[com] <= 5) {
				int node = com;
				while (node != mComA) {
					downList[mComA][mFileID].path[com].push_back(route[node].mid);
					node = route[node].prev;
				}
				size = getList[com][mFileID];
			}
		}
	}
	if (!size) return 0;
	int dlCnt = downList[mComA][mFileID].path.size();
	downList[mComA][mFileID].currentTime = mTime;
	downList[mComA][mFileID].endTime = (int)size / (dlCnt * 9);
	return dlCnt;
}

int getFileSize(int mTime, int mComA, int mFileID) {
	update(mTime);
	if (!isPossible(mFileID)) return 0;
	if (!getList[mComA].count(mFileID)) return 0;
	int ret = getList[mComA][mFileID];
	return ret;
}
#endif // 1

```
