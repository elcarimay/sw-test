```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <cstdio>
#include <cstring>
#include <vector>
#include <queue>
using namespace std;

const int INF = 987'654'321;

inline int min(int a, int b) { return a < b ? a : b; }

struct Node {
	int to, cost;
	bool operator<(const Node& node)const {
		return cost > node.cost;
	}
};

int n, k, C[7][7], DP[5][1 << 5], mSt, mEd;
vector<Node> adj[500];
vector<int> v, dist;

void Dijkstra(int st, int mStart, int mEnd) {
	fill(dist.begin(), dist.end(), INF);
	priority_queue<Node> PQ;
	PQ.push({ st, dist[st] = 0 });
	while (!PQ.empty()) {
		auto cur = PQ.top(); PQ.pop();
		if (cur.to == mStart || cur.to == mEnd) continue; // 비용을 업데이트하지 않을 지점을 처리
		if (dist[cur.to] < cur.cost) continue;
		for (auto next : adj[cur.to]) {
			if (dist[next.to] > cur.cost + next.cost)
				PQ.push({ next.to, dist[next.to] = cur.cost + next.cost });
		}
	}
}

int Tsp(int cur, int state) { // Traveling Salesman Problem
	if (state == (1 << k) - 1) return C[cur][k + 1];
	if (DP[cur][state] != -1) return DP[cur][state];
	else DP[cur][state] = INF;
	for (int i = 0; i < k; i++)
		if (!(state & 1 << i)) { // state와 1<< i가 겹치지 않을때만 참이 됨.
			int res = Tsp(i, state | 1 << i); // 합집합
			if (res != INF) DP[cur][state] = min(DP[cur][state], res + C[cur][i]);
		}
	return DP[cur][state];
}

void init(int N, int K, int mRoadAs[], int mRoadBs[], int mLens[]){
	n = N;
	for (int i = 0; i < 500; i++) adj[i].clear();
	for (int i = 0; i < K; i++)
	{
		--mRoadAs[i], --mRoadBs[i];
		adj[mRoadAs[i]].push_back({ mRoadBs[i], mLens[i] });
		adj[mRoadBs[i]].push_back({ mRoadAs[i], mLens[i] });
	}
}

void addRoad(int mRoadA, int mRoadB, int mLen){
	--mRoadA, --mRoadB;
	adj[mRoadA].push_back({ mRoadB, mLen });
	adj[mRoadB].push_back({ mRoadA, mLen });
}

int findPath(int mStart, int mEnd, int M, int mStops[]){
	v.clear(); v.resize(M); k = M;
	for (int i = 0; i < M; i++) v[i] = mStops[i] - 1;
	mSt = --mStart, mEd = --mEnd;
	v.push_back(mSt), v.push_back(mEd);
	dist.clear(), dist.resize(n);
	memset(C, -1, sizeof C);
	for (int i = 0; i < v.size(); i++) {
		if(i < v.size() - 2) Dijkstra(v[i], mSt, mEd); // 정점까지만 다익스트라 수행
		else if(i < v.size()-1) Dijkstra(v[i], -1, mEd); // 시작지점부터 다익스트라수행
		else Dijkstra(v[i], mSt, -1); // 끝점에서 다익스트라 수행
		for (int j = 0; j < v.size(); j++) C[i][j] = dist[v[j]];
	}
	int ans = INF; memset(DP, -1, sizeof DP);
	for (int i = 0; i < M; i++) {
		auto res = Tsp(i, 1 << i);
		if (res != INF) ans = min(ans, res + C[M][i]); // M은 출발지점을 의미함
	}
	return (ans == INF ? -1 : ans);
}
```
