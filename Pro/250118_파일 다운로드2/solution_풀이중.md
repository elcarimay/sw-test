```cpp
#include <vector>
#include <queue>
#include <unordered_map>
using namespace std;
#define MAX_ONEFILE 50
#define MAXN 1003
#define MAX_FILES 50003
#define INF 987654321

struct File {
	int id, size, endTime;
};
unordered_map<int, vector<File>> fileIncom;
unordered_map<int, int> fileSize;

struct Download {
	int fileid, size, currentTime, endTime;
	vector<int> v;
};
unordered_map<int, Download> down; // [comA] fileid, size, ct, et, list

struct Link {
	int from, to, dist;
	bool removed;
};
unordered_map<int, Link> link;

struct Edge {
	int to, cost;
	bool operator<(const Edge& r)const {
		return cost > r.cost;
	}
};
vector<Edge> adj[MAXN];

struct FTC {
	int fileid;
	vector<int> coms;
}fileTocom[503]; // [startcom] = fileID, targetcoms
unordered_map<int, vector<int>> fileLocation;
int N;
void init(int N, int mFileCnt[], int mFileID[][MAX_ONEFILE], int mFileSize[][MAX_ONEFILE]){
	::N = N; link.clear(), fileLocation.clear(), fileSize.clear(), down.clear();
	for (int i = 0; i < MAXN; i++) adj[i].clear();
	for (int i = 1; i <= N; i++)
		for (int j = 0; j < mFileCnt[i - 1]; j++) {
		fileIncom[i].push_back({ mFileID[i - 1][j], mFileSize[i - 1][j], 0 });
		fileLocation[mFileID[i - 1][j]].push_back(i);
		fileSize[mFileID[i - 1][j]] = mFileSize[i - 1][j];
	}
}

void makeNet(int K, int mID[], int mComA[], int mComB[], int mDis[]){
	for (int i = 0; i < K; i++) {
		link[mID[i]] = { mComA[i], mComB[i], mDis[i], false };
		adj[mComA[i]].push_back({ mComB[i], mDis[i] });
		adj[mComB[i]].push_back({ mComA[i], mDis[i] });
	}
}

int update(int time) {
	int ret = 0;
	for (auto it = down.begin(); it != down.end();) {
		int size = it->second.size, currentTime = it->second.currentTime, endTime = it->second.endTime;
		if (currentTime < time && time < endTime) {
			ret += (size * 9) * (time - currentTime);
			it->second.size -= (size * 9) * (time - currentTime);
			if (it->second.size < 0) it->second.size = 0;
			it->second.currentTime = time;
			it++;
		}
		else if (endTime <= time) {
			it->second.size = 0;
			it = down.erase(it);
			fileSize.erase(it->second.fileid);
		}
	}
	return ret;
}

void removeLink(int mTime, int mID){
	update(mTime);
	int from = link[mID].from, to = link[mID].to, d = link[mID].dist;
	auto& a = adj[from];
	for (int i = 0; i < a.size(); i++) if (a[i].to == to) a.erase(a.begin() + i);
	auto& b = adj[to];
	for (int i = 0; i < b.size(); i++) if (b[i].to == from) b.erase(b.begin() + i);
	link[mID].removed = true;
}

bool dijkstra(int s, int e) {
	int cost[MAXN]{};
	for (int i = 0; i < N; i++) cost[i] = INF;
	priority_queue<Edge> pq;
	pq.push({ s, 0 });
	while (!pq.empty()) {
		Edge cur = pq.top(); pq.pop();
		if (cur.cost > cost[cur.to]) continue;
		if (cur.to == e) return cost[cur.to] > 5 ? false : true;
		for (Edge nx : adj[s]) {
			int nextCost = cost[cur.to] + nx.to;
			if (cost[nx.to] > nextCost)
				pq.push({ nx.to, cost[nx.to] = nextCost });
		}
	}
	return false;
}

int downloadFile(int mTime, int mComA, int mFileID){
	if (!fileSize.count(mFileID)) return 0;
	for (int nx : fileLocation[mFileID]) {
		bool flag = dijkstra(mComA, nx);
		if (flag) down[mComA].v.push_back({ mFileID });
	}
	if (down[mComA].v.empty()) return 0;
	int endTime = fileSize[mFileID] / (int)fileTocom[mComA].coms.size() * 9;
	down[mComA] = { mFileID, mTime, endTime };
	return (int)fileTocom[mComA].coms.size();
}

int getFileSize(int mTime, int mComA, int mFileID){
	int ret = update(mTime);
	return ret;
}
```
