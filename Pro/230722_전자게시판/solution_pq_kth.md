```cpp
#if 1
/*
* pq, lazy update
* erase: 지워졌음을 표시
*/
#define _CRT_SECURE_NO_WARNINGS
#define MAXL    (10)
#include<vector>
#include<unordered_map>
#include<queue>
#include<string>
#include<string.h>
#include<algorithm>
using namespace std;
using pii = pair<int, int>;

int un, mn;                         // user수, message수
unordered_map<string, int> umap;    // user name => uid(0~)
unordered_map<int, int> mmap;       // mID => mid(0~)

struct User {
    char name[13];      // user이름
    int point;          // user총포인트
}user[10003];

struct Message {
    int type;           // 0=작성글, 1=댓글,답글
    int mID;            // 본래ID
    int uid;            // 작성한 user id
    int myPoint;        // 글 자체 포인트
    int totPoint;       // 총 포인트
    int parent;         // 작성글=>0, 댓글=>작성글, 답글=>댓글
    bool removed;       // 지워졌음을 표시
    vector<int> child;  // 작성글=>댓글리스트, 댓글=>답글리스트(0~1개)
}msg[50003];

struct UserComp {
    bool operator()(const pii l, const pii r) const {
        if (l.first != r.first) return l.first < r.first;
        return strcmp(user[l.second].name, user[r.second].name) > 0;
    }
};

struct MsgComp {
    bool operator()(const pii l, const pii r) const {
        if (l.first != r.first) return l.first < r.first;
        return msg[l.second].mID > msg[r.second].mID;
    }
};

priority_queue<pii, vector<pii>, UserComp> upq;     // {point, uid}
priority_queue<pii, vector<pii>, MsgComp> mpq;      // {point, mid}

void init()
{
    un = mn = 0;
    umap.clear();
    mmap.clear();
    upq = {};
    mpq = {};
}

// user name => uid 등록&반환
int getuid(char s[]) {
    // 기존에 있던 경우
    if (umap.count(s)) return umap[s];

    // 처음 나온 경우 (un 번호 부여, user 초기화)
    strcpy(user[un].name, s);
    user[un].point = 0;
    return umap[s] = un++;
}

// uid 유저의 포인트가 p만큼 증가
void updateUser(int uid, int p) {
    user[uid].point += p;
    upq.push({ user[uid].point, uid });
}

// mid 메시지의 포인트가 p만큼 증가
// mid는 작성글 or 댓글 (답글은 없음)
int updateMsg(int mid, int p) {
    // 댓글
    if (msg[mid].type) {
        msg[mid].totPoint += p;
        mid = msg[mid].parent;
    }

    // 작성글
    msg[mid].totPoint += p;
    mpq.push({ msg[mid].totPoint, mid });

    return msg[mid].totPoint;   // 작성 글 총 포인트 반환
}

int writeMessage(char mUser[], int mID, int mPoint)
{
    int uid = getuid(mUser);    // user id
    int mid = mmap[mID] = mn++; // message id

    // 작성 유저 업데이트
    updateUser(uid, mPoint);

    // 작성 글 업데이트
    msg[mid] = { 0, mID, uid, mPoint, mPoint };
    mpq.push({ mPoint, mid });
    return user[uid].point;
}

int commentTo(char mUser[], int mID, int mTargetID, int mPoint)
{
    int uid = getuid(mUser);        // user id
    int mid = mmap[mID] = mn++;     // message id
    int pid = mmap[mTargetID];      // parent message id

    // 작성 유저 업데이트
    updateUser(uid, mPoint);

    // 작성 댓글 or 답글 업데이트
    msg[mid] = { 1, mID, uid, mPoint, mPoint, pid };
    msg[pid].child.push_back(mid);
    return updateMsg(pid, mPoint);  // 상위 글 전부 업데이트
}

// 재귀로 모든 하위 메시지 유저 포인트 업데이트
void dfs(int mid) {
    updateUser(msg[mid].uid, -msg[mid].myPoint);
    for (int cid : msg[mid].child)
        if (msg[cid].removed == 0) dfs(cid);
}

int erase(int mID)
{
    int mid = mmap[mID];
    int pid = msg[mid].parent;

    // mid포함 모든 하위 메시지 작성 유저 업데이트
    dfs(mid);

    // 삭제됐음을 표시
    msg[mid].removed = 1;

    // 작성 글인 경우
    if (msg[mid].type == 0)
        return user[msg[mid].uid].point;

    // 댓글 or 답글인 경우
    return updateMsg(pid, -msg[mid].totPoint); // 상위 글 전부 업데이트
}

void getBestMessages(int mBestMessageList[])
{
    int n = 0;
    int top[5];
    while (n < 5) {
        int point = mpq.top().first;
        int mid = mpq.top().second;
        mpq.pop();

        if (msg[mid].removed) continue;             // 삭제된 경우
        if (msg[mid].totPoint != point) continue;   // point 바뀐 경우
        if (n && mid == top[n - 1]) continue;       // 중복

        mBestMessageList[n] = msg[mid].mID;
        top[n++] = mid;
    }

    for (int mid : top) mpq.push({ msg[mid].totPoint, mid });
}

void getBestUsers(char mBestUserList[][MAXL + 1])
{
    int n = 0;
    int top[5];
    while (n < 5) {
        int point = upq.top().first;
        int uid = upq.top().second;
        upq.pop();

        if (user[uid].point != point) continue;     // point 바뀐 경우
        if (n && uid == top[n - 1]) continue;       // 중복

        strcpy(mBestUserList[n], user[uid].name);
        top[n++] = uid;
    }
    for (int uid : top) upq.push({ user[uid].point, uid });
}
#endif // 1

```
