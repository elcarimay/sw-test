```cpp
#if 1
// 예제 16번 note부터 문제 오타있음
// node가 끝나는 시점을 finish에 설정하고 node 생성시점부터 대기가 가능하므로 finish를 생성시점에서 1을 빼서 저장
// 종류당 노드수가 최대 20개이므로 그냥 노드별로 task 탐색해서 우선순위 결정 가능함
// 이전 작업끝난시점(finish)가 mTimestamp보다 나중일 경우 일의 시작점을 finish + 1로 설정
#define _CRT_SECURE_NO_WARNINGS
#include <string>
#include <vector>
#include <string.h>
#include <unordered_map>
#include <cmath>
using namespace std;

#define MAXN 100003
#define NAME 1
#define TYPE 2

unordered_map<string, int> nameMap, typeMap;
int nameCnt, typeCnt;
char name[MAXN][13];

struct Task {
    int start, finish;
}task[MAXN];

struct Node {
    int finish, nid, speed;
    vector<int> task; // node가 끝나는 시점을 finish에 설정
    bool operator<(const Node& r)const {
        if (finish != r.finish) return finish < r.finish;
        return strcmp(name[nid], name[r.nid]) < 0 ? true : false;
    }
}node[MAXN];

vector<int> type[MAXN]; // tid 
int nodeCnt;
void init() {
    nameCnt = typeCnt = nodeCnt = 0, nameMap.clear(), typeMap.clear();
    for (int i = 0; i < MAXN; i++) type[i].clear();
}

void destroy() {}

int getID(int flag, char c[]) {
    if (flag == NAME) return nameMap.count(c) ? nameMap[c] : nameMap[c] = nameCnt++;
    return typeMap.count(c) ? typeMap[c] : typeMap[c] = typeCnt++;
}

void newNode(int mTimestamp, char mNodeName[], char mNodeType[], int mSpeed) {
    int nid = getID(NAME, mNodeName), tid = getID(TYPE, mNodeType);
    node[nodeCnt++] = { mTimestamp - 1, nid, mSpeed }; // 현재 시작점부터 대기가능하므로 finish에 1을 빼서 저장
    type[tid].push_back(nid);
    strcpy(name[nid], mNodeName);
}

void newTask(int mTimestamp, int mTaskId, char mNodeType[], int mWorkload, char mAssignedNode[]) {
    int tid = getID(TYPE, mNodeType); int nid = type[tid][0];
    for (int i = 1; i < type[tid].size(); i++)// 종류당 노드수가 20개이므로 20개탐색해서 우선순위 결정 가능함
        if (node[type[tid][i]] < node[nid]) nid = type[tid][i];
    strcpy(mAssignedNode, name[nid]);
    int s = node[nid].finish;
    // 이전 작업끝난시점이 mTimestamp보다 나중일경우 일의 시작점을 현재 노드 시작점 + 1로 설정
    s = (s < mTimestamp) ? mTimestamp : s + 1;
    int f = s + int(ceil(float(mWorkload) / float(node[nid].speed))) - 1;
    task[mTaskId] = { s, node[nid].finish = f };
    node[nid].task.push_back(mTaskId);
}

int listTasks(int mTimestamp, char mNodeName[], int mTaskIds[]) {
    int nid = getID(NAME, mNodeName), cnt = 0;
    auto& n = node[nid].task;
    for (int i = 0; i < n.size(); i++)
        if (task[n[i]].finish >= mTimestamp) mTaskIds[cnt++] = n[i];
        else n.erase(n.begin() + i), i--;
    return cnt;
}

int taskStatus(int mTimestamp, int mTaskId) {
    if (task[mTaskId].finish < mTimestamp) return 0; // 종료
    if (mTimestamp < task[mTaskId].start) return 2; // 대기
    return 1; // 작업중
}
#endif // 0

```
