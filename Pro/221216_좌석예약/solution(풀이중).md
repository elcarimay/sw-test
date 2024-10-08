```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<unordered_map>
#include<vector>
#include<string>
#include<string.h>
#include<algorithm>
using namespace std;
using pii = pair<int, int>;

struct Result {
	int id;
	int num;
};

int dc[] = { 1,0,-1,0 }, dr[] = {0,-1,0,1};

unordered_map<pii, vector<int>> m;
int cnt;

void init(int N){
	m.clear(); cnt = 0;
}

bool visit[10][10];
pii que[100];
int head, tail;
void bfs(int r, int c, int mID, int K) {
	head = tail = 0;
	memset(visit, 0, sizeof(visit));
	visit[r][c] = 1;
	K--;
	que[tail++] = { r, c };
	while (K--) {
		pii pos = que[head++];
		for (int i = 0; i < 4; i++) {
			int nr = pos.first + dr[i];
			int nc = pos.second + dc[i];
			if (visit[nr][nc]) continue;
			if (nr < 0 || nr > 10 || nc < 0 || nc > 10) continue;
			visit[nr][nc]++;
			m[{mID, cnt}].push_back(nr*10 + nc);
		}
	}
}


Result reserveSeats(int mID, int K)
{
	Result res;
	res.id = 0;
	res.num = 0;




	return res;
}

Result cancelReservation(int mID)
{
	Result res;
	res.id = 0;
	res.num = 0;
	return res;
}
```
