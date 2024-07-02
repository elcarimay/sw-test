```cpp
#if 1
/*
TS신소재케이블2_user_ver02
renumID : un_map
measure() : depth[]배열 이용한 linear LCA탐색
test() : BFS
*/
#include <vector>
#include <unordered_map>
#include <algorithm>
using namespace std;

constexpr int LM = 10005;
using pii = pair<int, int>;
int root, idcnt;                                 // root: 첫 노드, idcnt: 순차적 renumID
int sid, eid;                                    // measure()에서 사용
int par[LM], distToPar[LM], depth[LM];           // measure()에서 LCA 및 결과 구할 때 사용
vector<pii> adj[LM];                             // 인접배열
unordered_map<int, int> hmap;                    // renumID해시 테이블

int visited[LM], vcnt;
pii que[LM];

void init(int mDevice)
{
	for (int i = 0; i <= idcnt; ++i) adj[i].clear();
	root = idcnt = 0, hmap.clear();
	hmap.insert({ mDevice, idcnt++ });
	return;
}

void connect(int mOldDevice, int mNewDevice, int mLatency)
{
	int pid = hmap[mOldDevice];                   // 기존 노드 renumID얻기
	int cid = ++idcnt;
	hmap[mNewDevice] = cid;                       // 새로운 노드 renumID부여
	adj[pid].push_back({ cid, mLatency });        // 양방향 그래프 구성
	adj[cid].push_back({ pid, mLatency });
	par[cid] = pid, depth[cid] = depth[pid] + 1;
	distToPar[cid] = mLatency;
	return;
}

int measure(int mDevice1, int mDevice2)           // LCA 찾기 및 결과 구하기
{
	sid = hmap[mDevice1], eid = hmap[mDevice2];
	if (depth[sid] > depth[eid]) swap(sid, eid);  // 더 깊은 노드를 eid로 하기
	int diff = depth[eid] - depth[sid];           // 두 노드의 깊이 차 구하기
	int ret = 0;
	while (diff--) {                              // 깊이가 다른 동안
		ret += distToPar[eid];                    // eid의 거리를 ret에 합하기
		eid = par[eid];                           // eid를 부모노드로 이동시켜 깊이 맞추기
	}
	while (sid != eid) {                          // LCA가 아닌 동안
		ret += distToPar[sid] + distToPar[eid];   // 두 노드의 거리를 ret에 합하기
		sid = par[sid], eid = par[eid];           // 두 노드를 부모노드로 이동시키기
	}
	return ret;
}

int bfs(int st) {                                 // bfs탐색을 이용하여
	int head = 0, tail = 0;
	visited[st] = vcnt;
	que[tail++] = { st, 0 };
	int longest = 0;                              // st를 시점으로 최장거리 구하기
	while(head < tail){
		int u = que[head].first, ud = que[head++].second;
		longest = max(longest, ud);
		for (auto& nx:adj[u]) {
			int v = nx.first, td = ud + nx.second;
			if (visited[v] == vcnt) continue;
			visited[v] = vcnt;
			que[tail++] = { v, td };
		}
	}
	return longest;
}

int test(int mDevice)
{
	int ret1 = 0, ret2 = 0;
	int sid = hmap[mDevice];                      // sid를 가상의 루트로
	visited[sid] = ++vcnt;
	for (auto& nx:adj[sid]) {                   // sid의 자식을 시점으로 최장거리 구하기
		int ret = bfs(nx.first) + nx.second;
		if (ret1 < ret) ret2 = ret1, ret1 = ret;  // ret가 최장 1티어보다 더 크다면
		else if (ret2 < ret) ret2 = ret;          // ret가 최장 2티어보다 더 크다면
	}

	return ret1 + ret2;
}
#endif // 1

```
