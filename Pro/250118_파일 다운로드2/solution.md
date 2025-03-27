```cpp
#if 1
// 다운로드를 완료하지 못함의 뜻이 다운받는중이 아니라 정해진 파일 양을 최대한 다운받지 않았다는 뜻임
// 파일당 사이즈를 fileSize에 저장하고 개별컴퓨터마다 getList, donwList를 만들어서 관리하였음
// N이 최대 1000개이나 전체 순회를 해도 속도가 느리지 않음
// 다익스트라시 route에 경로를 저장해두고 상기 downList에 path를 저장하여 removeLink할때 확인하여 지움
#include <vector>
#include <unordered_map>
#include <algorithm>
#include <queue>
using namespace std;

#define MAXN 1003
#define MAX_ONEFILE		50
#define INF 5000

struct Down {
	int currentTime;
	unordered_map<int, vector<int>> path;
};

unordered_map<int, int> getList[MAXN], fileCapa; // getList -> [com] fid, size, fileCapa-> [fil] size
unordered_map<int, Down> downList[MAXN]; // [com] fid, struct Down
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
		for (auto it = downList[i].begin(); it != downList[i].end();) {
			int fid = it->first;
			getList[i][fid] += it->second.path.size() * 9 * (time - it->second.currentTime);
			if (getList[i][fid] >= fileCapa[fid]) {
				getList[i][fid] = min(getList[i][fid], fileCapa[fid]);
				it = downList[i].erase(it);
			}
			else it++->second.currentTime = time;
		}
	}
}

struct Route {
	int prev, mid;
}route[MAXN];

int cost[MAXN];
void dijkstra(int s) {
	fill(cost + 1, cost + N + 1, INF);
	priority_queue<Edge> pq;
	pq.push({ s, cost[s] = 0 });
	while (!pq.empty()) {
		Edge cur = pq.top(); pq.pop();
		if (cur.cost > cost[cur.to]) continue;
		for (Edge nx : adj[cur.to]) {
			int nextCost = cost[cur.to] + nx.cost;
			if (cost[nx.to] > nextCost) {
				route[nx.to] = { cur.to, nx.mid };
				pq.push({ nx.to, cost[nx.to] = nextCost });
			}
		}
	}
}

void linkErase(int mID) {
	int from = link[mID].first, to = link[mID].second;
	auto& a = adj[from]; auto& b = adj[to];
	for (int i = 0; i < a.size(); i++) if (a[i].to == to) {
		a.erase(a.begin() + i); break;
	}
	for (int i = 0; i < b.size(); i++) if (b[i].to == from) {
		b.erase(b.begin() + i); break;
	}
	link.erase(mID);
}

void removeLink(int mTime, int mID) {
	update(mTime);
	for (int i = 1; i <= N; i++) {
		if (downList[i].empty()) continue;
		for (auto it = downList[i].begin(); it != downList[i].end();) {
			for (auto nx = it->second.path.begin(); nx != it->second.path.end();) {
				bool removed = false;
				for (auto it2 = nx->second.begin(); it2 != nx->second.end(); it2++) {
					if (*it2 == mID) {
						nx = it->second.path.erase(nx); removed = true; break;
					}
				}
				if (!removed) nx++;
			}
			if (it->second.path.empty()) it = downList[i].erase(it);
			else it++;
		}
	}
	linkErase(mID);
}

int downloadFile(int mTime, int mComA, int mFileID) {
	update(mTime);
	dijkstra(mComA);
	for (int i = 1; i <= N; i++) if (getList[i].count(mFileID)) {
		if (downList[i].count(mFileID) || getList[i][mFileID] != fileCapa[mFileID]) continue;
		if (cost[i] <= 5) {
			int node = i; // 목적지부터 시작
			while (node != mComA) {
				downList[mComA][mFileID].path[i].push_back(route[node].mid);
				node = route[node].prev;
			}
		}
	}
	if (!downList[mComA].count(mFileID)) return 0;
	downList[mComA][mFileID].currentTime = mTime; // 다운받기 시작했기 때문에 현재시간 입력
	return downList[mComA][mFileID].path.size();
}

int getFileSize(int mTime, int mComA, int mFileID) {
	update(mTime);
	return getList[mComA].count(mFileID) ? getList[mComA][mFileID]: 0;
}
#endif // 1
```
