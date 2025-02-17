```cpp
#if 1
// 순서대로 정보를 저장할 수 있도록 구조체에 저장
// 단순히 방문기록을 저장하는게 아니라 방문한 곳중에서 가장 최근/오래전을 찾는문제임
// 정수배열에 저장후 순위정할때마다 입력후 정렬
// 0번을 비우고 전제에 대한 정보 저장하는데 사용하므로 id는 1부터 시작
#include<unordered_map>
#include<queue>
#include<algorithm>
#include<string.h>
#include<set>
using namespace std;

#define MAXN 10003
#define MAXM 13

struct DB {      // visit 정보를 순서대로 저장하는 임시 구조체
    int id, portal;   // 1부터 부여된 유저 번호 , portal id
}db[MAXN * MAXM];

unordered_map<int, int> idMap;
int N, M, K, tick;
queue<int> user[MAXN][MAXM]; // id와 포탈에 따라 tick을 기록
int info[MAXN][MAXM]; // id와 포탈 방문 기록

void init(int N, int M, int K, int uIDList[]) {
    ::N = N, ::M = M, ::K = K, tick = 0;
    memset(info, 0, sizeof(info));
    idMap.clear();
    for (int i = 1; i <= N; i++) {
        idMap[uIDList[i - 1]] = i;
        for (int j = 1; j <= M; j++) {
            while (!user[i][j].empty()) user[i][j].pop();
        }
    }
}

void visit(int mUser, int mPortal) {
    int id = idMap[mUser], p;
    db[++tick] = { id, mPortal };
    user[id][mPortal].push(tick);
    info[0][mPortal]++, info[id][mPortal]++;
    if (tick - K > 0) {
        id = db[tick - K].id, p = db[tick - K].portal;
        user[id][p].pop();
        info[0][p]--, info[id][p]--;
    }
}

struct Newer {
    int id, tick;
    bool operator<(const Newer& r)const {
        return tick > r.tick;
    }
};
set<Newer> newer;
int getNewestVisited(int mUser, int mList[]) {
    int id = idMap[mUser];
    newer.clear();
    for (int i = 1; i <= M; i++)
        if (!user[id][i].empty()) newer.insert({ i,user[id][i].back()});
    int count = 0;
    for (set<Newer>::iterator it = newer.begin(); it != newer.end(); it++)
        mList[count++] = it->id;
    return count;
}

int getOldestVisited(int mUser, int mList[]) {
    int id = idMap[mUser];
    newer.clear();
    for (int i = 1; i <= M; i++) {
        if(!user[id][i].empty()) newer.insert({ i,user[id][i].front() });
    }
    int count = 0;
    for (set<Newer>::reverse_iterator it = newer.rbegin(); it != newer.rend(); it++)
        mList[count++] = it->id;
    return count;
}

struct Cnt {
    int id, cnt;
    bool operator<(const Cnt& r)const {
        if (cnt != r.cnt) return cnt > r.cnt;
        return id < r.id;
    }
};
set<Cnt> cnt;
void getMostVisited(int mUser, int mList[]) {
    int id = idMap[mUser];
    cnt.clear();
    for (int i = 1; i <= M; i++) cnt.insert({ i,info[id][i] });
    int count = 0;
    for (set<Cnt>::iterator it = cnt.begin(); it != cnt.end(); it++)
        mList[count++] = it->id;
}

void getMostVisitedAll(int mList[]) {
    cnt.clear();
    for (int i = 1; i <= M; i++) cnt.insert({ i,info[0][i] });
    int count = 0;
    for (set<Cnt>::iterator it = cnt.begin(); it != cnt.end(); it++)
        mList[count++] = it->id;
}
#endif // 1
```
