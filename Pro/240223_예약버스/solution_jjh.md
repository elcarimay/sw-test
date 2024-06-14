```cpp
#include <vector>	// 인접 리스트
#include <queue>	// 우선순위큐 -> 다익스트라

using namespace std;

struct Edge
{
	int to;	// 어디가니?
	int cost; // 비용은?
};

struct cmp
{
	bool operator()(const Edge& a, const Edge& b) const {
		return a.cost > b.cost;
	}
};

const int MAX = 501;	// 노드의 수 500, 1~500까지 접근
vector<Edge> adj[MAX];	// 인접 리스트 선언
int N;	// 전체 노드의 수


void init(int _N, int K, int mRoadAs[], int mRoadBs[], int mLens[])
{
	N = _N;
	// 이전에 저장했던 인접 리스트를 초기화
	for (int i = 1; i <= N; i++)
		adj[i].clear();

	// 새로운 인접 리스트 저장
	for (int i = 0; i < K; i++)
	{
		// A → B로 가는 간선
		adj[mRoadAs[i]].push_back({ mRoadBs[i], mLens[i] });

		// B → A로 가는 간선
		adj[mRoadBs[i]].push_back({ mRoadAs[i], mLens[i] });
	}
}

void addRoad(int mRoadA, int mRoadB, int mLen)
{
	// A → B로 가는 간선
	adj[mRoadA].push_back({ mRoadB, mLen });

	// B → A로 가는 간선
	adj[mRoadB].push_back({ mRoadA, mLen });
}

// 1억
// 1 -> 2 -> 3 -> 4-> 5-> .. -> N : (N - 1 ) *30 = 499*30 = 
const int INF = 1'000'000;
// const int INF = 0x7fff'ffff;

int DijkstraResult[MAX];
void Dijkstra(int start, int pass1, int pass2)
{
	for (int i = 1; i <= N; i++)
		DijkstraResult[i] = INF;

	DijkstraResult[start] = 0;

	priority_queue<Edge, vector<Edge>, cmp> Q;
	Q.push({ start, 0 });

	while (Q.size())
	{
		// 현재 최단 경로를 불러옴
		auto cur = Q.top();
		Q.pop();

		/* ★★★★★★★★★★★★★★★★★★★ */
		// 추가적으로 서치할 필요 없는 경우 PASS
		// 지금까지 저장했던 비용이 현재 계산하려고 하는 비용보다 작으면
		if (DijkstraResult[cur.to] < cur.cost)
			continue;

		/* ★★★★★★★★★★★★★★★★★★★ */

		// 다음 경로에 대해 탐색
		for (auto& next : adj[cur.to])
		{
			if (next.to == pass1 || next.to == pass2)
				continue;

			// 다음 이동 경로의 비용 = 현재까지의 비용 + 이동에 필요한 비용
			int nextCost = DijkstraResult[cur.to] + next.cost;

			// 갱신을 해야 할 조건 : 지금까지 얘가 더 가까운줄 알았는데, 다시 보니 아니네?
			// 지금까지 저장된 경로 > 현재 계산한 비용
			if (DijkstraResult[next.to] > nextCost)
			{
				// 갱신 후 추가 탐색을 한다.
				DijkstraResult[next.to] = nextCost;
				Q.push({ next.to, nextCost });
			}
		}
	}
}

// 경유지가 5개 + 시작 + 종료
int path[7];

// 최단 경로 저장
int cost[7][5];

int pass[7][2];

int M;
int visit[5];

int search(int n, int cur, int sum)
{
	// 마지막 경유지까지 탐색했으면
	// 최종 비용 = 현재까지의 비용 + 마지막 경유지로 이동하는 비용
	if (n == M)
		return sum + cost[M + 1][cur];

	int ret = INF;
	for (int i = 0; i < M; i++)
	{
		// 이미 서치한 경우 탐색을 하지 않는다.
		if (visit[i] == 1)
			continue;

		// 이번에 i번째 탐색할거라고 표시
		visit[i] = 1;

		// n + 1 번째를 탐색
		// 최소값을 갱신해줍니다.
		ret = min(ret, search(n + 1, i, sum + cost[cur][i]));

		// 탐색 했으면 탐색 끝났다고 표시
		visit[i] = 0;
	}

	return ret;
}

int findPath(int mStart, int mEnd, int _M, int mStops[])
{
	M = _M;

	for (int i = 0; i < M; i++) {
		path[i] = mStops[i];
		pass[i][0] = mStart;
		pass[i][1] = mEnd;
	}

	path[M] = mStart;
	pass[M][0] = mEnd, pass[M][1] = -1;

	path[M + 1] = mEnd;
	pass[M + 1][0] = mStart, pass[M + 1][1] = -1;

	for (int i = 0; i < M + 2; i++)
	{
		Dijkstra(path[i], pass[i][0], pass[i][1]);

		// 결과값을 cost 배열에 갱신
		for (int j = 0; j < M; j++)
			cost[i][j] = DijkstraResult[path[j]];
	}

	// 전체 탐색
	int ret = search(0, M, 0);

	if (ret >= INF)
		return -1;

	return ret;
}
```
