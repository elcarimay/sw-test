```cpp
#include <vector>
#include <queue>
using namespace std;

#define INF 987654321

struct Airline
{
	int to, startTime, travelTime, price;
};

vector<Airline> adj[60];

struct Edge {
	int to, cost;
	bool operator<(const Edge& edge)const {
		return cost > edge.cost;
	}
};

int N;
void init(int _N) {
	N = _N;
	for (int i = 0; i < N; i++) adj[i].clear();
}

void add(int mStartAirport, int mEndAirport, int mStartTime, int mTravelTime, int mPrice) {
	adj[mStartAirport].push_back({ mEndAirport, mStartTime, mTravelTime, mPrice });
}

int cost[60];
int minTravelTime(int mStartAirport, int mEndAirport, int mStartTime) {
	fill(cost, cost + N, INF);
	cost[mStartAirport] = mStartTime;
	priority_queue<Edge> Q;
	Q.push({ mStartAirport, mStartTime });
	while (!Q.empty()) {
		auto cur = Q.top(); Q.pop();
		if (cur.cost > cost[cur.to]) continue;
		for (auto nx : adj[cur.to]) {
			int curTime = cur.cost % 24, waitTime;
			if (curTime > nx.startTime) waitTime = 24 - (curTime - nx.startTime);
			else waitTime = nx.startTime - curTime;
			int nextTime = cost[cur.to] + waitTime + nx.travelTime;
			if (cost[nx.to] > nextTime) Q.push({ nx.to, cost[nx.to] = nextTime });
		}
	}
	return cost[mEndAirport] == INF ? -1 : cost[mEndAirport] - mStartTime;
}

int minPrice(int mStartAirport, int mEndAirport) {
	fill(cost, cost + N, INF);
	cost[mStartAirport] = 0;
	priority_queue<Edge> Q;
	Q.push({ mStartAirport, 0 });
	while (!Q.empty()) {
		auto cur = Q.top(); Q.pop();
		if (cur.cost > cost[cur.to]) continue;
		for (auto nx : adj[cur.to]) {
			int nextPrice = cost[cur.to] + nx.price;
			if (cost[nx.to] > nextPrice) Q.push({ nx.to, cost[nx.to] = nextPrice });
		}
	}
	return cost[mEndAirport] == INF ? -1 : cost[mEndAirport];
}
```
