```cpp
#if 1
#include <queue>
#include <vector>
#include <unordered_set>
#include <string.h>
using namespace std;
using pii = pair<int, int>;

int n, gcnt; // 게임판크기, 마지막 부여된 구역번호
int bcnt[203][203]; // 겹쳐진 bar 개수
int gid[203][203]; // 빈 공간의 구역 번호
struct Bar {
	int x, y, dir, len;
}bar[1000000];

unordered_set<int> used; // 사용된 group id를 저장
int dx[] = { -1,1,0,0 }, dy[] = { 0,0,-1,1 };

void init(int N) {
	n = N, gcnt = 0;
	used = { 0 };
	memset(bcnt, 0, sizeof(bcnt));
	memset(gid, 0, sizeof(gid));
	for (int i = 0; i <= n + 1; i++)
		bcnt[0][i] = bcnt[n + 1][i] = bcnt[i][0] = bcnt[i][n + 1] = -1;
}

vector<pii> mark;

pii que[1000000];
int head, tail;
void bfs(int sx, int sy) {
	head = tail = 0;
	used.insert(++gcnt);
	que[tail++] = { sx, sy };
	gid[sx][sy] = gcnt;
	while (head < tail) {
		pii cur = que[head++];
		for (int i = 0; i < 4; i++) {
			int x = cur.first + dx[i];
			int y = cur.second + dy[i];
			if (bcnt[x][y] != 0 || gid[x][y] == gcnt) continue;
			que[tail++] = { x, y };
			gid[x][y] = gcnt;
		}
	}
}

void proc() {
	int orgMaxGid = gcnt;
	for (pii cur : mark)
		if (gid[cur.first][cur.second] <= orgMaxGid)
			bfs(cur.first, cur.second);
}

int addBar(int mID, int len, int row, int col, int dir) {
	mark.clear();
	bar[mID] = { row, col, dir, len };
	int x = row, y = col;
	for (int i = 0; i < len; i++) { // bar위치에 대해 표시
		if (bcnt[x][y] == 0) used.erase(gid[x][y]);
		bcnt[x][y]++;
		x += dx[dir], y += dy[dir];
	}
	x = row, y = col;
	for (int i = 0; i < len; i++) { // bar주위 지점들을 mark에 저장
		for (int j = 0; j < 4; j++) {
			int nx = x + dx[j], ny = y + dy[j];
			if (bcnt[nx][ny] == 0) {
				used.erase(gid[nx][ny]);
				mark.push_back({ nx, ny });
			}
		}
		x += dx[dir], y += dy[dir];
	}
	proc();
	return used.size();
}

int removeBar(int mID) {
	mark.clear();
	int x = bar[mID].x, y = bar[mID].y;
	for (int i = 0; i < bar[mID].len; i++) { // 막대제거, mark 추가
		if (--bcnt[x][y] == 0) mark.push_back({ x, y });
		x += dx[bar[mID].dir], y += dy[bar[mID].dir];
	}

	for (pii cur : mark) { // 제거한 막대 주변의 구역 해제
		for (int i = 0; i < 4; i++) {
			int x = cur.first + dx[i], y = cur.second + dy[i];
			if (bcnt[x][y] == 0) used.erase(gid[x][y]);
		}
	}
	proc();
	return used.size();
}
#endif // 1

```
