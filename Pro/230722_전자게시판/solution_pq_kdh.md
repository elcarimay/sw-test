```cpp
#if 1
// erase에서 댓글 삭제시 가장 최상위 글에 대한 포인트를 반환
// best Message나 best User를 할때 단순히 5개만 뽑으면 안되고 첫번째가 같더라도 두번째가 다른게 있기 때문에 set으로 구현시 실시간 update가 필요함
// 동일한 mid로 다른 글이 쓰여질수 있기 때문에 getID함수를 만들때 주의해야 함
// 우큐에서 중복된 것 제외할때 두번째부터 첫번째랑 같은지 비교해서 제외함.if (cnt && cur.mid == top[cnt - 1])
#define _CRT_SECURE_NO_WARNINGS
#include <vector>
#include <unordered_map>
#include <string>
#include <queue>
using namespace std;
#define MAXL 10

struct User {
	char name[13];
	int point;
}user[10003];

struct Message {
	int type, mid, uid, point, totPoint, pid; // type: 0 작성글, 1 댓글, 답글
	bool removed;
	vector<int> child;
}msg[50003];
unordered_map<string, int> uMap;
unordered_map<int, int> mMap;
int getUID(char c[]) {
	if (uMap.count(c)) return uMap[c];
	int uid = uMap.size() + 1;
	strcpy(user[uid].name, c);
	user[uid].point = 0;
	return uMap[c] = uid;
}

struct BestMessage {
	int totPoint, mid;
	bool operator<(const BestMessage& r)const {
		if (totPoint != r.totPoint) return totPoint < r.totPoint;
		return msg[mid].mid > msg[r.mid].mid;
	}
};
priority_queue<BestMessage> m;
struct BestUser {
	int point, uid;
	bool operator<(const BestUser& r)const {
		if (point != r.point) return point < r.point;
		return strcmp(user[uid].name, user[r.uid].name) > 0;
	}
};
priority_queue<BestUser> u;
void init() {
	uMap.clear(), mMap.clear(), m = {}, u = {};
}

void updateUser(int uid, int point) {
	user[uid].point += point;
	u.push({ user[uid].point, uid });
}

int writeMessage(char mUser[], int mID, int mPoint) {
	int uid = getUID(mUser), mid = mMap[mID] = mMap.size() + 1;
	updateUser(uid, mPoint);
	msg[mid] = { 0, mID, uid, mPoint, mPoint };
	m.push({ mPoint, mid });
	return user[uid].point;
}

int update_parent(int pid, int point) {
	if (msg[pid].type) {
		msg[pid].totPoint += point;
		pid = msg[pid].pid;
	}
	msg[pid].totPoint += point;
	m.push({ msg[pid].totPoint, pid });
	return pid;
}

int commentTo(char mUser[], int mID, int mTargetID, int mPoint) {
	int uid = getUID(mUser), mid = mMap[mID] = mMap.size() + 1;
	int tid = mMap[mTargetID];
	updateUser(uid, mPoint);
	msg[mid] = { 1, mID, uid, mPoint, mPoint, tid };
	msg[tid].child.push_back(mid);
	int root = update_parent(tid, mPoint);
	return msg[root].totPoint;
}

void updateUserAll(int mid) {
	updateUser(msg[mid].uid, -msg[mid].point);
	for (int cid : msg[mid].child) updateUserAll(cid);
}

int erase(int mID) {
	int mid = mMap[mID], root;
	int pid = msg[mid].pid;
	updateUserAll(mid); // 자식노드는 user point를 update
	msg[mid].removed = true;
	if (!msg[mid].type) return user[msg[mid].uid].point;
	msg[pid].child.erase(find(msg[pid].child.begin(), msg[pid].child.end(), mid)); // 댓글은 부모에서 자식을 삭제
	root = update_parent(pid, -msg[mid].totPoint); // 부모노드는 totPoint를  update
	return msg[root].totPoint;
}

int cnt, top[5];
void getBestMessages(int mBestMessageList[]) {
	cnt = 0;
	while (cnt < 5) {
		auto cur = m.top(); m.pop();
		if (msg[cur.mid].removed) continue;
		if (msg[cur.mid].totPoint != cur.totPoint) continue;
		if (cnt && cur.mid == top[cnt - 1]) continue;
		mBestMessageList[cnt] = msg[cur.mid].mid;
		top[cnt++] = cur.mid;
	}
	for (int mid : top) m.push({ msg[mid].totPoint, mid });
}

void getBestUsers(char mBestUserList[][MAXL + 1]) {
	cnt = 0;
	while (cnt < 5) {
		auto cur = u.top(); u.pop();
		if (user[cur.uid].point != cur.point) continue;
		if (cnt && cur.uid == top[cnt - 1]) continue;
		strcpy(mBestUserList[cnt], user[cur.uid].name);
		top[cnt++] = cur.uid;
	}
	for (int uid : top) u.push({ user[uid].point, uid });
}
#endif // 0
```
