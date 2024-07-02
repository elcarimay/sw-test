```cpp
#if 1 // vcnt를 써야하는 이유 파악하는중
#include <string.h>
#define MAX_N 30
int (*A)[MAX_N];
int dr[] = { -1,0,1,0 }, dc[] = { 0,1,0,-1 };
struct Data
{
	int r, c, d, cnt;
}que[30 * 30 * 4 + 5];

bool visit[35][35][5];
int head, tail;

void init(int n, int mMap[MAX_N][MAX_N]) {
	A = mMap;
}

int user_visit[35][35], vcnt;

struct UserData
{
	int r, c;
}user_que[30 * 30 + 5];

// 캐릭터가 이동가능한 모든 위치 검색
// (user_visit[r][c]를 초기화하지 않고 vcnt로 기록)
void bfs(int sr, int sc) {
	vcnt++;
	int head = 0, tail = 0;
	user_que[tail++] = { sr, sc };
	user_visit[sr][sc] = vcnt;
	while (head < tail) {
		auto d = user_que[head++];
		for (int i = 0; i < 4; i++) {
			int r = d.r + dr[i], c = d.c + dc[i];
			if (A[r][c]) continue;
			if (user_visit[r][c] == vcnt) continue;
			user_que[tail++] = { r, c };
			user_visit[r][c] = vcnt;
		}
	}
}

int push(int sr, int sc, int mDir, int er, int ec) {
	head = tail = 0;
	memset(visit, 0, sizeof(visit));
	que[tail++] = { sr, sc, mDir, 0 };
	visit[sr][sc][mDir] = 1;
	while (head < tail) {
		Data& d = que[head++];
		if (d.r == er && d.c == ec) return d.cnt;
		int cur_user_r = d.r + dr[d.d];
		int cur_user_c = d.c + dc[d.d];
		A[d.r][d.c] = 1;
		bfs(cur_user_r, cur_user_c);
		A[d.r][d.c] = 0;
		for (int i = 0; i < 4; i++) {
			int rock_r = d.r - dr[i], rock_c = d.c - dc[i]; // 사람의 위치기준으로 돌의 위치를 계산
			int push_user_r = d.r + dr[i], push_user_c = d.c + dc[i];
			if (visit[rock_r][rock_c][i]) continue;
			if (A[rock_r][rock_c]) continue;
			if (user_visit[push_user_r][push_user_c] != vcnt) continue;
			que[tail++] = { rock_r, rock_c, i, d.cnt + 1 };
			visit[rock_r][rock_c][i] = 1;
		}
	}
	return 0;
}
#endif
```
