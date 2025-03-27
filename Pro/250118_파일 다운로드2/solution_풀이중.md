```cpp
#if 1
#include <vector>
#include <unordered_map>
#include <unordered_set>
#include <algorithm>
#include <queue>
#include <cmath>
using namespace std;

#define MAXN 1003
#define MAX_ONEFILE		50
#define INF 1e6

struct Down {
	int currentTime;
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
	::N = N, fileCapa.clear(), link.clear();
	for (int i = 1; i <= N; i++) {
		getList[i].clear(), downList[i].clear(), adj[i].clear();
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

void update(int time) {
	for (int i = 1; i <= N; i++) {
		if (downList[i].empty()) continue;
		for (auto it = downList[i].begin(); it != downList[i].end();) { // fid, currentTime, endTime
			int fid = it->first, currentTime = it->second.currentTime, dlCnt = it->second.path.size(), dT = 0;
			it->second.currentTime = time;
			dT = time - currentTime;
			getList[i][fid] += dlCnt * 9 * dT;
			if (getList[i][fid] >= fileCapa[fid]) {
				getList[i][fid] = min(getList[i][fid], fileCapa[fid]);
				it = downList[i].erase(it);
			}
			else it++;
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

void removeLink(int mTime, int mID) {
	update(mTime);
	for (int i = 1; i <= N; i++) {
		if (downList[i].empty()) continue;
		for (unordered_map<int, Down>::iterator it = downList[i].begin(); it != downList[i].end();) { // fid, currentTime, endTime, dlCnt
			for (unordered_map<int, vector<int>>::iterator nx = it->second.path.begin(); nx != it->second.path.end();) {
				bool removed = false;
				for (vector<int>::iterator it2 = nx->second.begin(); it2 != nx->second.end(); it2++) {
					if (*it2 == mID) {
						nx = it->second.path.erase(nx); removed = true; break;
					}
				}
				if (!removed) nx++;
			}
			if (it->second.path.empty()) it = downList[i].erase(it); // there is no link -> erase
			else it++;
		}
	}
	linkErase(mID);
}

int downloadFile(int mTime, int mComA, int mFileID) {
	update(mTime);
	if (!fileCapa.count(mFileID)) return 0;
	dijkstra(mComA);
	for (int i = 1; i <= N; i++) if (getList[i].count(mFileID)) {
		if (downList[i].count(mFileID) || i == mComA) continue;
		if (cost[i] <= 5) {
			int node = i;
			while (node != mComA) {
				downList[mComA][mFileID].path[i].push_back(route[node].mid);
				node = route[node].prev;
			}
		}
	}
	int dlCnt = downList[mComA][mFileID].path.size();
	if (!dlCnt) return 0;
	downList[mComA][mFileID].currentTime = mTime;
	return dlCnt;
}

int getFileSize(int mTime, int mComA, int mFileID) {
	update(mTime);
	if (!getList[mComA].count(mFileID)) return 0;
	int ret = getList[mComA][mFileID];
	return ret;
}
#endif // 1
```
