```cpp
#if 1
#include<unordered_map>
#include<vector>
#include<algorithm>
#include<string.h>
using namespace std;
using pii = pair<int, int>;
#define MAX_N 20

// 약속한 방향 → 0: 북쪽, 1 : 동쪽, 2 : 남쪽, 3 : 서쪽
int N;
int A[25][25]; // int(*A)[MAX_N];
int dr[] = { -1,0,1,0 }, dc[] = { 0,1,0,-1 };
struct Data { int sr, sc, dir; };
unordered_map<int, vector<Data>> hmap;

int key, rev_key;
void setKeyFromLand(int n, int arr[]) {
    int minVal = *min_element(arr, arr + n) - 1;
    key = rev_key = 0;
    for (int i = 0; i < n; i++) {
        key = key * 10 + arr[i] - minVal;
        rev_key = rev_key * 10 + arr[n - i - 1] - minVal;
    }
}

int getKeyFromStructure(int n, int arr[]) {
    int maxVal = *max_element(arr, arr + n) + 1;
    int key = 0;
    for (int i = 0; i < n; i++)
        key = key * 10 + maxVal - arr[i];
    return key;
}

void init(int N, int mMap[MAX_N][MAX_N]) {
    ::N = N;
    hmap.clear();
    for (int i = 0; i < N; i++)
        for (int j = 0; j < N; j++)
            A[i][j] = mMap[i][j];
    int sub[5];
    for (int len = 2; len <= 5; len++) {
        for (int i = 0; i < N; i++) {
            for (int j = 0; j <= N - len; j++) {
                // 가로
                memcpy(sub, A[i] + j, 4 * len);
                setKeyFromLand(len, sub);
                hmap[key].push_back({ i, j, 1 }); // 1: 동쪽
                if (key != rev_key)
                    hmap[rev_key].push_back({ i, j + len - 1, 3 }); // 3: 서쪽
// insert함수와 가장 큰 차이는 중복 key가 존재하면 insert는 삽입하지 않지만 
// operator []는 중복 key가 문제가 아니라 해당 key에 해당하는 값(value)의 참조를 반환하기에 값을 덮어씌울 수 있으며
// 해당 key가 존재하지 않는다면 해당 key와 함께 0이라는 값을 채워 std::unordered_map에 삽입해버린다
                // 세로
                for (int k = 0; k < len; k++)
                    sub[k] = A[k + j][i];
                setKeyFromLand(len, sub);
                hmap[key].push_back({ j, i, 2 });
                if (key != rev_key)
                    hmap[rev_key].push_back({ j + len - 1, i, 0 });
            }
        }
    }
}

int numberOfCandidate(int M, int mStructure[]) {
    int key = getKeyFromStructure(M, mStructure);
    return hmap[key].size();
}

void updateStructure(int sign, Data d, int m, int structure[]) {
    int r = d.sr, c = d.sc, dir = d.dir;
    for (int i = 0; i < m; i++)
        A[r + dr[dir] * i][c + dc[dir] * i] += structure[i] * sign;
}

int visit[25][25], vcnt, head, tail;
pii que[25 * 25];

void push(int r, int c) {
    que[tail++] = { r ,c };
    visit[r][c] = vcnt;
}

int bfs(int dir) { // 도착지점부터 시작해서 bfs탐색후 전체 가능수에서 뺌
    vcnt++;
    head = tail = 0;
    for (int i = 0; i < N; i++) { // mapSize에 따라 시작위치를 우큐에 넣음
        switch(dir){
            case 0: push(0, i); break; // 0: 북쪽
            case 1: push(i, N - 1); break; // 1: 동쪽
            case 2: push(N - 1, i); break; // 2: 남쪽
            case 3: push(i, 0); break; // 3: 서쪽
        }
    }
    while (head < tail) {
        int r = que[head].first;
        int c = que[head++].second;
        for (int i = 0; i < 4; i++) {
            int nr = r + dr[i], nc = c + dc[i];
            if (nr < 0 || nr >= N || nc < 0 || nc >= N) continue;
            if (visit[nr][nc] == vcnt) continue;
            if (A[r][c] > A[nr][nc]) continue;
            push(nr, nc);
        }
    }
    return N * N - tail;
}

int maxBlockedRobots(int M, int mStructure[], int mDir) {
    int res = 0;
    int key = getKeyFromStructure(M, mStructure);
    for (Data d : hmap[key]) {
        updateStructure(1, d, M, mStructure); // 구조물 설치
        res = max(res, bfs(mDir));
        updateStructure(-1, d, M, mStructure); // 구조물 해체
    }
    return res;
}
#endif // 1

```
