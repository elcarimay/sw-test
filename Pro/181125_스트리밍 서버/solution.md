```cpp
#if 1 // 5 ms
#define MAXLength 10000
#define MAXServer 10
#define MAX_SIZE 1000000
#define INF 100000000000
#include <queue>
#include <algorithm>
#include <iostream>
using namespace std;

struct Data
{
    int dist, id;
    long long pvalue;
    bool operator<(const Data& r)const {
        if (pvalue != r.pvalue) return pvalue > r.pvalue;
        return id < r.id;
    }
};

priority_queue<Data> outUser[MAXServer], standByUser[MAXServer], closeServer;
int MaxCapacity, Capacity[MAXServer], ServerLoc[MAXServer];
int ServerCount, Len, UserAxis[MAXLength], UserServer[MAXLength];

int getDist(int uId, int serverId) {
    return min(abs(UserAxis[uId] - ServerLoc[serverId]), Len - abs(UserAxis[uId] - ServerLoc[serverId]));
}

long long getPvalue(int uId) {
    int max = 0;
    for (int i = 0; i < ServerCount; i++) {
        int dist = getDist(uId, i);
        if (dist > max) max = dist;
    }
    return max * 100000 + 10000 - uId;
}

void init(int L, int N, int C, int axis[MAXServer]) {
    Len = L, ServerCount = N, MaxCapacity = C;
    for (int i = 0; i < N; i++) {
        ServerLoc[i] = axis[i];
        Capacity[i] = 0;
        outUser[i] = {}, standByUser[i] = {};
    }
}

int add_user(int uid, int axis) {
    UserAxis[uid] = axis;
    closeServer = {};
    long long pvalue = getPvalue(uid);
    Data srv;
    for (int i = 0; i < ServerCount; i++) {
        int dist = getDist(uid, i);
        closeServer.push({ dist, i, dist * 100 + i });
    }
    // 가까운 서버순서로 들어갈수 있는지 확인한다.
    while (!closeServer.empty()) {
        srv = closeServer.top(); closeServer.pop();

        // 서버내에서 팅길 user들중 쓰레기값을 제거한다.
        while (!outUser[srv.id].empty()) {
            Data user = outUser[srv.id].top();
            if (UserServer[user.id] == srv.id) break;
            else outUser[srv.id].pop();
        }
        if (Capacity[srv.id] < MaxCapacity) { // 수용인원이 남아있으면 해당 서버에 할당한다.
            outUser[srv.id].push({ srv.dist, uid, pvalue });
            UserServer[uid] = srv.id;
            ++Capacity[srv.id];
            break;
        }
        else if (outUser[srv.id].top().pvalue < pvalue) {// 최대수용인원이나 내가 우선순위가 높아서 그 서버에 들어갈 수 있다면
            outUser[srv.id].push({ srv.dist, uid, pvalue });
            UserServer[uid] = srv.id;
            Data user = outUser[srv.id].top(); outUser[srv.id].pop();
            UserServer[user.id] = -1;
            add_user(user.id, UserAxis[user.id]);// 메뚜기는 add_user 재귀호출로 처리한다.
            break;
        }
        else
            standByUser[srv.id].push({ srv.dist, uid, INF - pvalue });// 우선순위가 낮아서 들어갈 수 없다면 대기표를 적어둔다.
    }
    return srv.id;
}

// uid를 현재 서버위치에서 nextSid로 옮기는 함수
int move_user(int uid, int nextSid) {
    int currentSid = UserServer[uid];
    UserServer[uid] = nextSid;
    --Capacity[currentSid];
    if (nextSid >= 0) { // 다음 위치가 서버라면(remove되는게 아니라면)
        ++Capacity[nextSid];
        outUser[nextSid].push({ 0, uid, getPvalue(uid) });
    }

    // 대기표 적어둔 사람이 1명 이상이면
    Data user;
    bool findMoveUser = false;

    while (!standByUser[currentSid].empty()) {
        user = standByUser[currentSid].top(); standByUser[currentSid].pop();
        // 이미 나간 사용자라면
        if (UserServer[user.id] < 0) continue;
        int newDist = getDist(uid, currentSid) * 100 + currentSid;
        int currentDist = getDist(uid, UserServer[user.id]) * 100 + UserServer[user.id];
        // 이미 더 가까운 서버에 있는 사용자라면
        if (newDist >= currentDist) continue;
        // 이동할 사용자를 찾았다.
        findMoveUser = true;
        break;
    }
    // 이동할 사용자를 찾았다면
    if (findMoveUser) move_user(user.id, currentSid);// 그 사람을 현재 서버로 옮긴다.
    return currentSid;
}

int remove_user(int uid) {
    return move_user(uid, -1);
}

int get_users(int sid) {
    return Capacity[sid];
}
#endif // 1 // 5 ms

```
