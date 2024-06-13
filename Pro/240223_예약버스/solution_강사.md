```cpp
#include <cstring>
#include <queue>
#include <vector>
#include <algorithm>
using namespace std;

struct Data
{
    int to, cost;
    bool operator<(const Data& r)const {
        return cost > r.cost;
    }
};

struct Info
{
    int from, to;
};

const int LM = 501;
const int INF = 987654321;

int N, tab[LM][LM]; // tab[LM][LM]: i정류장과 j정류장 사이 최단거리, tab[i][j] = 0이면 경로가 없는 경우
vector<Data> adj[LM];
vector<Info> prr; // prr: tab[i][j]값을 채운 경우 0으로 초기화 할 목록들, 최대 40개이다.

void addRoad(int s, int e, int d) {
    adj[s].push_back({ e, d });
    adj[e].push_back({ s, d });
}

void init(int N, int K, int a[], int b[], int d[]){
    for (int i = 1; i <= N; i++) adj[i].clear();
    ::N = N;
    for (int i = 0; i < K; i++) addRoad(a[i], b[i], d[i]);
}

int dist[LM];
int arr[10], M;
int used[LM], ucnt; // 전역변수는 0임.

int visit[LM];

int dijkstra(int src) {
    fill(dist + 1, dist + N + 1, INF);
    fill(visit+ 1, visit+ N + 1, INF);
    dist[src] = 0;
    priority_queue<Data> pq;
    pq.push({ src,0 });
    visit[src]++;
    while (!pq.empty()) {
        auto cur = pq.top(); pq.pop();
        if (used[cur.to] == 1) continue;
        visit[cur.to]++;
        for (int i = 0; i < adj[cur.to].size(); i++) {
            auto nx = adj[cur.to][i];
            int nextCost = dist[cur.to] + nx.cost;
            if (dist[nx.to] > nextCost) {
                dist[nx.to] = nextCost;
                if (nx.to != arr[0] && nx.to != arr[M]) // 출발지와 도착지가 아니라면 추가
                    pq.push({ nx.to, nextCost });
            }
        }
    }
    for (int i = 0; i <= M; i++) { // 중간경로에서 출발점과 도착점까지의 경로가 있는지 확인
        if (dist[arr[i]] == INF) return 0;
    }
    for (int i = 0; i <= M; i++) { // 최단거리 테이블에 저장하기
        tab[src][arr[i]] = tab[arr[i]][src] = dist[arr[i]];
        prr.push_back({ src, arr[i] }); // 정류장 정보 백업
    }
    return 1;
}

//int dijkstra(int src) {
//    fill(dist + 1, dist + N + 1, INF);
//    dist[src] = 0;
//    priority_queue<Data> pq;
//    pq.push({ src,0 });
//    ucnt++;
//    while (!pq.empty()) {
//        auto cur = pq.top(); pq.pop();
//        if (used[cur.to] == ucnt) continue;
//        used[cur.to] = ucnt;
//        for (int i = 0; i < adj[cur.to].size(); i++) {
//            auto nx = adj[cur.to][i];
//            int nextCost = dist[cur.to] + nx.cost;
//            if (dist[nx.to] > nextCost) {
//                dist[nx.to] = nextCost;
//                if (nx.to != arr[0] && nx.to != arr[M]) // 출발지와 도착지가 아니라면 추가
//                    pq.push({ nx.to, nextCost });
//            }
//        }
//    }
//    for (int i = 0; i <= M; i++) { // 중간경로에서 출발점과 도착점까지의 경로가 있는지 확인
//        if (dist[arr[i]] == INF) return 0;
//    }
//    for (int i = 0; i <= M; i++) { // 최단거리 테이블에 저장하기
//        tab[src][arr[i]] = tab[arr[i]][src] = dist[arr[i]];
//        prr.push_back({ src, arr[i] }); // 정류장 정보 백업
//    }
//    return 1;
//}

int ret, visited[10];
void dfs(int lev, int u, int dist) {
    if (lev == M) {
        if (tab[u][arr[M]])
            ret = min(ret, dist + tab[u][arr[M]]);
        return;
    }
    for (int i = 1; i < M; i++) {
        int v = arr[i];
        if (visited[i] == 0 && tab[u][v]) {
            visited[i] = 1;
            dfs(lev + 1, v, dist + tab[u][v]);
            visited[i] = 0;
        }
    }
}

int findPath(int mStart, int mEnd, int mM, int mStops[]){
    M = mM, ret = INF;
    int i;
    for (i = 0; i < M; i++) arr[i + 1] = mStops[i];
    arr[0] = mStart, arr[++M] = mEnd;
    for (i = 1; i < M && dijkstra(arr[i]); i++);

    if (i == M) { // 경로가 존재하는 경우
        dfs(1, arr[0], 0); // dfs를 이용하여 예약 정류장에 대한 순열을 생성해보기
    }
    for (i = 0; i < prr.size(); i++) { // 최단거리 테이블 초기화
        tab[prr[i].from][prr[i].to] = 0;
        tab[prr[i].to][prr[i].from] = 0;
    }
    prr.clear();
    return ret < INF ? ret : -1;
}
```
