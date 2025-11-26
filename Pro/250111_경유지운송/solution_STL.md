```cpp
#if 1
#include <vector>
#include <queue>
using namespace std;
using pii = pair<int, int>;

enum { LM = 1005, INF = 987654321 };
int N, K, M, mnL, mxL, s, e;
vector<pii> adj[LM];
int limit[3401], L[LM][LM], stop[5];
priority_queue<pii> pq; // limit, node
vector<int> R;
bool v[5];

void add(int sCity, int eCity, int mLimit) {
	adj[sCity].push_back({ mLimit, eCity });
	adj[eCity].push_back({ mLimit, sCity });
}

void init(int N, int K, int sCity[], int eCity[], int mLimit[]) {
	::N = N, ::K = K;
	for (int i = 0;i < N;i++) adj[i].clear();
	for (int i = 0;i < K;i++) add(sCity[i], eCity[i], mLimit[i]);
}

void callLimit(int s) {
	memset(limit, 0, sizeof(limit));
	limit[s] = INF;
	pq = {}; pq.push({ INF, s });
	while (!pq.empty()) {
		pii c = pq.top(); pq.pop();
		if (c.first < limit[c.second]) continue;
		for (pii n : adj[c.second]) {
			int nLim = min(c.first, n.first);
			if (limit[n.second] < nLim) {
				limit[n.second] = nLim;
				pq.push({ nLim, n.second });
			}
		}
	}
	for (int i = 0;i <= M;i++) L[s][stop[i]] = limit[i];
}

void dfs(int level) {
	if (level == M) {
		mnL = min(L[s][stop[R[0]]], L[stop[R[M - 1]]][e]);
		for (int i = 0;i < R.size() - 1;i++)
			mnL = min(mnL, L[stop[R[i]]][stop[R[i + 1]]]);
		mxL = max(mxL, mnL);
		return;
	}
	for (int i = 1;i <= M;i++) {
		if (!v[i]) {
			R.push_back(i);
			v[i] = true;
			dfs(level + 1);
			v[i] = false;
			R.pop_back();
		}
	}
}

int calculate(int sCity, int eCity, int M, int mStopover[]) {
	::M = M;
	R.clear();
	mnL = INF, mxL = 0;
	memset(L, 0, sizeof(L));
	s = stop[0] = sCity, e = stop[M + 1] = eCity;
	for (int i = 0;i < M;i++) stop[i + 1] = mStopover[i];
	for (int i = 0;i < M + 2;i++) callLimit(stop[i]);
	dfs(0);
	return (mxL == INF || mxL == 0) ? -1 : mxL;
}
#endif // 1
```
